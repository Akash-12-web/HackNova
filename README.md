# SafeInbox

**One inbox that blocks scams and surfaces what matters.**

Built for the HackNowa Global Hackathon 2026 · Problem statement: **AI for Everyday Life**

- **Live demo:** https://claude.ai/artifact/2zQwEnGYFYez9xVJh2kMCR
- **Demo video:** `https://drive.google.com/file/d/1X12ZE3w7hdzbyF_8zz0bzpE2ou-MuXdR/view?usp=sharing`
- **Author:** Akash (individual entry)

---

## The problem

People get messages from three places all day: WhatsApp, SMS and email. Two things go wrong:

1. **Scams and hoaxes look like normal messages.** Fake KYC and OTP requests, bill-disconnection threats, "work from home" task offers and forwarded chain hoaxes cost people money and spread misinformation.
2. **Important messages get buried.** A message from a family member, or an interview email from a company you applied to, sits among promotions and spam, and the reply is delayed or forgotten.

## The solution

SafeInbox brings messages from WhatsApp, SMS and email into one feed and classifies each one as it arrives:

| Lane | What goes here | Example |
|---|---|---|
| **Needs your reply** | Messages from your priority contacts or organisations that ask for a response | Mom asking you to call, an interview confirmation |
| **Important** | Matters to you, no reply needed | A hackathon deadline reminder |
| **Everything else** | Bank alerts, promotions, newsletters | A bank debit alert, a food-delivery offer |
| **Spam and scams** | Scams, phishing and forwarded hoaxes | A fake "SBI KYC" text with a look-alike link |

Each message shows a priority score and the reasons behind its lane, so the sorting is never a black box.

## Features

- **Scam and hoax detection.** Rules cover patterns common in Indian scams: OTP and PIN requests, fake KYC updates, account-blocked threats, electricity-disconnection threats, lottery and prize claims, upfront fees, easy-money task jobs, fake courier and "digital arrest" calls, remote-access app requests, and "forward to everyone" chain messages.
- **Link inspection.** Flags shortened URLs, insecure http links, bare IP addresses, unusual domain endings, look-alike characters, `@` tricks, and links that imitate banks or apps (for example `sbi-kyc-update.xyz`).
- **Personal priorities.** You set priority contacts, organisations to watch, important words, always-low-priority senders and blocked senders. Changing any of them re-sorts every message instantly.
- **Learns from you.** "Mark as spam", "Not spam" and "Always prioritise sender" update the rules for future messages.
- **Highlighted evidence.** "Show details" marks the exact suspicious phrases and links inside a message.
- **AI reply drafts.** For messages in "Needs your reply" and "Important", Claude drafts a short reply in a suitable tone. It uses placeholders instead of inventing facts like your availability.
- **Live demo feed.** Simulate incoming messages, switch on auto-receive, or type your own test message.

## How it works

```
 WhatsApp ─┐
 SMS ──────┼─► Intake ─► Spam and hoax engine ─► Priority engine ─► Lanes + reasons
 Email ────┘             (wording + links)        (sender, content,       │
                                                   deadline, reply)      ▼
                                                                  AI reply drafts
```

1. **Spam and hoax engine.** Weighted pattern rules score the message text, and a separate link checker scores every URL. Scores combine as `1 − ∏(1 − weight)`, so several weak signals add up without any single one dominating. A combined risk of 50 or more sends the message to Spam and scams.
2. **Priority engine.** For messages that pass, the score comes from who sent it (priority contact, watched organisation), what it mentions (your important words), whether it asks for a reply, and whether it has a deadline. Promotions and automatic alerts are capped low.
3. **Claude** drafts replies on request. Messages are treated as untrusted input, and the prompt tells the model never to follow instructions found inside a message.

The classifier runs in the browser. Message text is only sent to Claude when you click **Draft reply with AI**.

## Honest scope: what is simulated

The feed in this prototype is **simulated**, because a web page cannot read a person's WhatsApp, SMS or Gmail. Every message goes through the real classifier, so the intelligence is the real part. Connecting real accounts changes only the intake:

- **Email:** the Gmail API with a read-only OAuth scope and push notifications, so new mail is classified as it arrives.
- **SMS:** an Android companion app with the SMS-receive permission. iPhones do not let apps read texts.
- **WhatsApp:** personal chats have no public API. An Android companion app with notification access can read incoming message text on the device. Businesses can use the WhatsApp Business API.

The classifier here is rule-based plus a Claude layer. A trained model could replace or sit beside the rules once labelled message data is available.

## Run it

It is a single self-contained file with no build step and no dependencies.

1. Download `index.html` (the SafeInbox page).
2. Open it in any modern browser.

The scam check, prioritisation and learning work offline. The **Draft reply with AI** button needs the Claude-enabled page linked above.

## Try these in the demo

- Click **Simulate incoming message** a few times and watch each message land in its lane.
- Open the **Spam and scams** tab, then **Show details** on the fake KYC text to see the highlighted evidence.
- Add `Karthik` or `Rahul Sir` to **Priority contacts** (or remove them) and watch the lanes change.
- In **Test a message**, paste a message of your own.

## Limits

- Detection is pattern-based. A message that avoids every known pattern can pass, and an unusual but genuine message can be flagged. A clean result is not a guarantee, so the **Not spam** and **Mark as spam** buttons exist.
- The rules are tuned to English and common Indian scam patterns. Hindi, Hinglish and other languages need more rules or a trained model.

## Tech

HTML, CSS and vanilla JavaScript in one file. Claude for reply drafting.
