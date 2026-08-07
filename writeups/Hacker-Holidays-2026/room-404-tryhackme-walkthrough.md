# Room 404 TryHackMe walkthrough

---

The Room 404 is the **Day 2** challenge of TryHackMe’s Hacker Holidays, where we were presented with a web application running on port **8080**.

![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/001.png)

## Step 1: Directory Enumeration

Scan the web server with gobuster.


```bash
gobuster dir -u http://10.67.147.0:8080 -w /usr/share/wordlists/dirb/common.txt
```


![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/002.png)

The scan revealed an interesting result:


```bash
/.git/HEAD (Status: 200)
```


This immediately suggested that the web server was exposing its **Git repository**, which is a common security misconfiguration. So, let’s inspect the file. Opening the file in the browser showed:


```bash
ref: refs/heads/main
```


![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/003.png)

This tells Git that the current branch is **main**.

## Step 2: Following the Reference

I used `curl`:


```bash
curl http://10.67.147.0:8080/.git/refs/heads/main
```


![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/004.png)

The request returned the latest commit identifier, confirming that the Git metadata was publicly accessible.

## Step 3: Dumping the Repository

Since the `.git` directory was publicly accessible instead of manually downloading Git objects, I used **git-dumper** to reconstruct the entire repository.


```bash
git-dumper http://10.67.147.0:8080/.git repo
```


![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/005.png)

When it finished, I entered the recovered repo:


```bash
cd repo
```


and listed the files:


```lua
find . -maxdepth 2 -type f
```


![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/006.png)

## Step 4: Retrieving the Flag

I just cated README.md

![Room 404 TryHackMe walkthrough screenshot](../assets/room-404-tryhackme-walkthrough/007.png)
