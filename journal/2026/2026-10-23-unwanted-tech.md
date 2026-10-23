---
title: An Unwanted Tech Page, Reusing the Form Defences
date: 2026-10-23
category: Web
tags:
  - Cloudflare
  - Cloudflare Pages
  - Turnstile
  - Web
  - AI-assisted
summary: Adding a page and enquiry form for donated and bought second-hand tech to my repair site, by extending the hardened contact form instead of building a second one, including optional photo uploads.
public: true
featured: false
---

[Back to journal index](../README.md)

> [!NOTE]
> **In short:** I added a page to my repair shop's website for people who have old computers and electronics to give away or sell. Its enquiry form, which can include photos, runs through the contact form's existing anti-spam protections instead of a new copy of them. I tested it on the live site and it worked: the photos arrived and both the customer and I got an email.

New to some of the terms? The [glossary](../../GLOSSARY.md) explains them in plain English.

## What I worked on

A new page on my repair business website saying that I accept unwanted computers and electronics, either donated or bought if they are worth refurbishing. It has an enquiry form with optional photos. Project page: [Business Websites on Cloudflare Pages](../../projects/business-websites.md).

I used Claude to write the page, change the form code and open the pull request. I set the requirements, chose between the options it put to me, and tested the result on the live site myself.

## What changed

- **A new page** explaining what I take (working or faulty), that donations are welcome and some items may be bought, what happens to equipment (refurbished, resold, used for parts or repair training), a reminder to remove personal data first, and a plain statement that this is not a recycling or disposal service.
- **One form backend, not two.** The enquiry form sends its details to the same server code as the contact form, with a field saying which form it came from. That means it automatically gets the protections from the September spam fix (a hidden trap field for bots, the Turnstile check, a per-visitor limit and input checks) with no copied security code.
- **Optional photos** (up to four small images) are emailed to me as attachments and are not stored anywhere. The server checks the number, the size, the type the browser claims and the file's actual first few bytes, so a renamed non-image is turned away.
- **Links to it:** a menu item on every page, a card on the services page and a callout on the home page.
- **First-person wording** (I, my) throughout, because I am a sole trader. I also changed the older contact confirmation email to match.

## Problems / debugging

- The menu already had eight links plus the phone button. One more made the logo overlap the menu on laptop-width screens, so I tightened the spacing and switched to the phone-style menu at a wider screen size.
- The one part I could not test on my own computer was a real submission, because Turnstile needs a human to solve it. Instead I sent hand-built requests to check the rules, and ran the email-sending code with the outside services faked. Then I tested the real thing once it was live.

## Decisions

- **Extend the existing form code** rather than write a second one. The trade-off is that both forms share one limit on how often a visitor can submit, which suits a low-traffic site.
- **Email attachments instead of storing photos.** No new service, nothing extra to secure or clean up. The cost is a cap on photo size.
- **Do not trust the file type the browser reports.** It is easy to fake, so the server looks at the file itself.
- **Photos only on the new form.** The plain contact form rejects them.

## What I learned

- Reusing a protected path is cheaper and safer than copying it. A new form is also a good moment to re-check the old one still behaves.
- Test what you can in pieces, and be clear about what you cannot test until it is live.
- After deploying, a real end-to-end test is what counts. It passed: the form submitted, the photos arrived and opened, and both the customer and I received emails.

## Next steps

- Watch for spam or oversized uploads and tighten the limits if needed.
- Add an alert for failed deploys (still open from the 12/10 entry).
