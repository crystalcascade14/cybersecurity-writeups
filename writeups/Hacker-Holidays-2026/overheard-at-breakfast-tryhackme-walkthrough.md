# Overheard at Breakfast TryHackMe walkthrough

---

**Day 6** of TryHackMe’s Hacker Holidays challenge is all about OSINT. I’ll use publicly available information to identify a hidden profile and recover the flag without exploiting a single system.

![Overheard at Breakfast TryHackMe walkthrough screenshot](../../assets/overheard-at-breakfast-tryhackme-walkthrough/001.png)

## Step 1: Run Holehe

According to the photo we were given, the user uses the email [lambobytelotushotel@gmail.com](mailto:lambobytelotushotel@gmail.com)

![Overheard at Breakfast TryHackMe walkthrough screenshot](../../assets/overheard-at-breakfast-tryhackme-walkthrough/002.png)

Firstly, I launch holehe on my Kali machine and check what accounts the user has online.


```sql
holehe — only-used lambobytelotushotel@gmail.com
```


It found a hidden page


```bash
https://gravatar.com/cheerfullysongf28e3c3716
```


![Overheard at Breakfast TryHackMe walkthrough screenshot](../../assets/overheard-at-breakfast-tryhackme-walkthrough/003.png)

The page shows a base64 hash:

![Overheard at Breakfast TryHackMe walkthrough screenshot](../../assets/overheard-at-breakfast-tryhackme-walkthrough/004.png)


```text
VEhNe1MzY3JlVF9QcjBmaWwzX0g0c19iMzNuX0lkZW50MWZpM2R9
```


## Step 2: Crack the hash and get the flag


```bash
echo ‘VEhNe1MzY3JlVF9QcjBmaWwzX0g0c19iMzNuX0lkZW50MWZpM2R9’ | base64 -d
```


![Overheard at Breakfast TryHackMe walkthrough screenshot](../../assets/overheard-at-breakfast-tryhackme-walkthrough/005.png)

P.S to install holehe just run:


```text
pipx install holehe
```


If you haven’t read my previous walkthroughs, you can find the solutions for [**Day 1**](https://crystalcascade14.medium.com/the-concierge-knows-too-much-tryhackme-walkthrough-a73a3b65aba6) , [**Day 2**](https://medium.com/@crystalcascade14/room-404-tryhackme-walkthrough-aa32146fafba), [**Day 3**](https://medium.com/@crystalcascade14/complimentary-tryhackme-walkthrough-0282c00a700c), [**Day 4**](https://medium.com/@crystalcascade14/packed-light-tryhackme-walkthrough-83390b1f2117) **and** [**Day 5**](https://medium.com/@crystalcascade14/beach-bar-tryhackme-walkthrough-5fbc8e0989c1)**.**

