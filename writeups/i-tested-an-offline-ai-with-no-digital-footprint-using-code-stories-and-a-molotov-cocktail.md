# I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail

---

## **I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail**

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/001.png)

ChatGPT vs Docket (offline AI model) — which is better in terms of privacy, censorship, creativity, speed, and precision?

Life without AI is already barely possible, but the very thought of AI knowing everything about us (from medical issues to food preferences) is frightening. Not only because this data can be disclosed and used against us, if necessary, but also because the model is often not objective. Moreover, sometimes the answers are ridiculously incorrect or the model suddenly blocks your prompts because of censorship guardrails.

That’s why I decided to test an offline AI system Docket (LLM created by Google), whose developers claim that it can be used without the Internet, has no censorship and leaves no digital footprint.

## **Hardware matters**

I placed an order for $75.36 and in a week got my flash drive Docket system with both USB and micro-USB plugs. In the beginning, I was a bit apprehensive to plug some unknown drive into a work computer, so I started with my old Acer Swift 3 (AMD Ryzen 7, 8GB LDDR4), Windows OS. Before testing I disconnected it from everything, including the Wi-Fi to actually check how good it works with no network.

The system asked for a couple of minutes to boot and after that I got a screen with a search bar below — aka ChatGPT. It looked promising.

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/002.jpeg)

However, as soon as I wrote my prompt and was ready to send it the button “Send” became inactive. Rebooting didn’t help. Nor a restart of the system.

I wrote to customer support. They asked me if I could copy the text of the error code — but there was no such message. The system simply didn’t work because the button was stuck.

I thought the issue was this particular laptop and decided to try it on an identical machine — my husband’s Acer Swift 3 with the exactly same parameters. The result was the same.

The third guinea pig device was my Windows-powered Digma tablet which didn’t even see the flash drive plugged in. I was pissed off at this point.

The only device remaining was my working Lenovo Yoga Intel Core 7 32GB RAM. I hesitated but was too curious to give up and plugged Docket in. This time it worked — and surprisingly was very lightweight:

It appeared that the system has different models and presets: minimal, creator, writer, survival, coder, etc. I decided to test them exclusively offline.

## **Censorship and reverse engineering**

The first test was a censorship test. Choosing a Survival preset and Gemma 3n 2B model, I asked it for a recipe to create a Molotov cocktail. First, the Survival model started lecturing me and refused to give the answer. I was disappointed — Docket’s ad promised no limits and no moderation:

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/003.png)

However, a couple of reverse engineering prompts later and I finally got what I wanted — all the ingredients, proportions and good advice on how to use it safely.

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/004.png)

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/005.png)

I felt like I was becoming ready to fight with the regime.

## **Creativity and clichés check**

The second test was a creativity test. As an ESL teacher, I often see when text is written by AI. Especially by ChatGPT because it is notorious for using awful cliches, which can be easily identified; for example, in some of my students’ essays.

GPT’s favorites are: broken syntaxes (in an attempt to convey emotions and create drama), banal and overused collocations, predictable endings, and oversaturation of adverbs. I had years of work with it to make a fair comparison.

So, I prompted both Docket and ChatGPT to write a deeply emotional story about a boy who lost his parents. I wanted it to be moving and make readers cry. This is what I got from Docket:

And this is GPT:

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/006.png)

It was surprising to discover that Docket used less cliched language and more sophisticated ideas. When I asked it which writer it is trying to mimic it admitted that it is a mix of Hemingway, Dickinson, and modern writers. The text indeed was saturated with different languages and expressive devices like metaphors, similes, and emphasis.

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/007.png)

However, when I asked both models to finish the below sentences using no more than 10 words, remaining creative, poetic, but simple, GPT won the battle.

ChatGPT:

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/008.png)

Docket:

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/009.png)

When I asked Docket to be less formal and banal it replied: *“You’re right to call me out! I’m trying to be useful, not exciting”*. Fair enough.

## **Coding test**

The third test was a prompt to write a Python code which would broadcast a message to all open shell terminals in Kali Linux by different users (including SSH connected). I wrote the same prompt to both ChatGPT and Docket and both of them failed from the first attempt. In the case of GPT, after a 20-minute conversation and corrections it gave me the code that worked, which I still had to correct slightly. Docket’s Coder kept making syntax mistakes and after 30 minutes I was simply tired and gave up.

While playing with the code, once an interesting error popped up:

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/010.png)

It proves that the system isn’t trying to connect to external services — it tried to contact a local AI server on my own machineat127.0.0.1:8080 (means localhost), but it could not reach it.

## **Security aspect**

As I mentioned, it is not always clear how and when your data from GPT or Co-pilot can be used. Moreover, in ChatGPT, prompts, messages, and account data are protected during transmission and storage ONLY, but they are still accessible to the service provider. In other words, your messages are not “only you can read them” end-to-end encrypted like some secure messengers.

The primary advantage of Docket is that it runs locally on your device or within a secure environment you control. This significantly reduces the risk of your data being exposed to a third party like OpenAI. You also have more direct control over where your data resides and how it’s processed. You aren’t sending prompts to a remote server managed by another company.

Another thing is that you use Docker with a password and restrict access to the environment where you run it. ChatGPT lacks this function: everyone who has access to your computer can read your prompts if you forgot to log out.

![I Tested an Offline AI With No Digital Footprint — Using Code, Stories, and a Molotov Cocktail screenshot](../assets/i-tested-an-offline-ai-with-no-digital-footprint-using-code-stories-and-a-molotov-cocktail/011.png)

## **Conclusion**

All in all, I remain mostly carefully optimistic about my purchase. Yes, Docket’s pre-training data has a knowledge cutoff date and won’t have information about events that occurred after this date unless they are explicitly found through the Google Search integration.

Yes, with offline Docket you have to be patient — as it is not as fast as Co-Pilot and GPT (waiting time sometimes is 3–4 times long), and from time to time the screen may freeze mid-answer or throw an error on you:

Yes, it may not be as tech-savvy as GPT in terms of coding.

Yes, you cannot upload a picture or PDF file to Docket to analyze it.

But the reward of leaving no digital footprint online sounds promising, especially when Big Tech Brother is watching you (and selling your data).
