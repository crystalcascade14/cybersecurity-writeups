# Beach Bar TryHackMe walkthrough

---

**Day 5** of TryHackMe’s Hacker Holidays explores unsafe YAML deserialization. We’ll exploit PyYAML to gain a reverse shell, then escalate privileges by recovering exposed service credentials and abusing password reuse.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/001.png)

## Step 1: Discover credentials and login

Page source revealed a developer comment. the credentials are dj/dj

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/002.png)

After logging in, the application provides several pages:

- Dashboard
- Import
- Export

The **Import** page immediately stands out because it accepts playlists in **YAML** format.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/003.png)

Before attempting to exploit it, click **Export** and download one of the existing playlists. This provides the expected YAML structure that we can modify for our own testing.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/004.png)

## Step 2: Identify the vulnerability

Let’s understand **how it processes YAML files**.

We’ll start with a simple playlist containing different YAML data types.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/005.png)

After clicking **Load playlist**, the application displays:


```css
{‘playlist’: {‘number’: 
123
, ‘decimal’: 
1.5
, ‘enabled’: True,
‘empty’: None, ‘date’: datetime.
date
(
2026
, 
7
, 
31
)}}
```


Notice what happened.

The application didn’t simply print our text back to us. Instead, it converted the YAML values into **Python objects**:

`123 > 123` (integer)

`1.5 > 1.5` (float)

`true > True` (boolean)

`null > None`

This tells us that the server is actually **deserializing** the YAML into Python objects rather than treating it as plain text.

It gives us an important clue: the application is using a Python YAML parser such as **PyYAML**.

Now let’s test if it also understand Python-specific YAML tags.

To test this, submit the following playlist:


```yaml
playlist:
 
test:
 
!!python/name:os.getcwd
```


![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/006.png)

As we can see, instead of treating `!!python/name:os.getcwd` as plain text, the application resolved it into the actual Python function:


```bash
<built-in function getcwd>
```


At this point we know the application understands Python constructors, so the next step is to determine whether we can use one of them to execute code.

## **Step 3: Reverse shell payload prep**

Once we know the application accepts Python-specific constructors, the next question becomes:

***Which Python function should we call?***

We know that Python provides the function:


```bash
os.system(command)
```


Rather than executing a single command, we can execute a **reverse shell**, giving us an interactive session on the target.

Replace the playlist name with the following payload:

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/007.png)

Before executing, don’t forget to set up a listener:


```bash
nc -lvnp 444
```


Execute and get a shell. Navigate search for the file using:


```bash
find / -type f -name user.txt 2>dev/null
```


Task one is done. The flag is retrieved.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/008.png)

## Step 4: Inspect the systemd service

After obtaining a shell as the `bartender` user, the next objective is privilege escalation. I began by enumerating the application files under `/opt`.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/009.png)

This Python script tells us that the jukebox service **requires a password when it starts**.  
The key line is:


```python
parser.add_argument(
 “ — stream-
pass
”,
 required=
True
,
 
help
=”stream backend password”
)
```


Because the argument is marked as `required=True`, the application **cannot start unless someone provides a password**.

The script then stores that value here:


```ini
args = parser.parse_args()
```


Since the application is managed by **systemd**, we can inspect how the service is launched:


```perl
systemctl show jukeboxd.service | grep ExecStart
```


![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/010.png)

The password is exposed because it is passed as a **command-line argument**. Command-line arguments are visible through `systemctl show`, `ps`, `/proc`, and similar tools. They should **never** be used to store secrets. As you can see above, the password is:


```text
SunsetSpritz2024!
```


Change to root and retrieve the flag.

![Beach Bar TryHackMe walkthrough screenshot](../assets/beach-bar-tryhackme-walkthrough/011.png)

If you haven’t read my previous walkthroughs, you can find the solutions for [**Day 1**](https://crystalcascade14.medium.com/the-concierge-knows-too-much-tryhackme-walkthrough-a73a3b65aba6) , [**Day 2**](https://medium.com/@crystalcascade14/room-404-tryhackme-walkthrough-aa32146fafba), [**Day 3**](https://medium.com/@crystalcascade14/complimentary-tryhackme-walkthrough-0282c00a700c) **and** [**Day 4**](https://medium.com/@crystalcascade14/packed-light-tryhackme-walkthrough-83390b1f2117)**.**
