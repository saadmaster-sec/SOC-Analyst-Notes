# LetsDefend — Phishing Email Analysis

> Notes based on the LetsDefend Phishing Email Analysis lessons provided in the course material.

---

## 1. Introduction to Phishing

### What is Phishing?

Phishing usually aims to steal personal information by tricking users into:

- Clicking malicious links in emails.
- Running malicious files.

Phishing falls into the **Delivery** phase of the **Cyber Kill Chain**, where an attacker transfers pre-arranged malicious content to the victim's systems or people.

Attackers commonly use tricky or persuasive phrases such as:

- "You have won a gift."
- "Don't miss out on the big discount."
- "If you don't click on the link in the email your account will be suspended."

LetsDefend describes phishing as the **most common initial attack vector**.

The purpose is not only to steal passwords. Phishing also exploits the **human factor**, which LetsDefend describes as the weakest link in the chain, as a first step to infiltrate systems.

---

# 2. Information Gathering

## Spoofing

Emails do not necessarily have an authentication mechanism, allowing attackers to send emails in someone else's name.

**Spoofing** is used to make an incoming email appear reliable.

The following can be used to determine whether a sender address is fake or real:

- **SPF — Sender Policy Framework**
- **DKIM — DomainKeys Identified Mail**
- **DMARC**

Some email programs check these automatically. Their use is not mandatory and can cause problems in some cases.

---

## Manually Checking for Spoofing

To manually investigate possible spoofing:

1. Identify the SMTP address of the email.
2. Use tools such as **MXToolbox** to obtain:
   - SPF records
   - DKIM records
   - DMARC records
   - MX records
3. Compare the information to determine whether the email is spoofed.

---

## SMTP IP & WHOIS

Large institutions that use their own mail servers have their own IP addresses.

The **WHOIS** records of the SMTP IP can be checked to determine whether the IP belongs to the institution the sender claims to represent.

---

## Important Limitation

Even if the sender address is not spoofed, the email may still be unsafe.

A trusted corporate or personal email account can be compromised and then used to send malicious emails.

Therefore:

> A legitimate-looking sender does not automatically mean the email is safe.

---

## E-mail Traffic Analysis

Many parameters are needed to analyze a phishing attack.

Searching the mail gateway for different parameters can help understand the **size of the attack** and its **target audience**.

Useful parameters include:

| Parameter | Example |
|---|---|
| Sender Address | `info@letsdefend.io` |
| SMTP IP Address | `127.0.0.1` |
| Domain | `@letsdefend.io` |
| Sender/account name | `letsdefend` |
| Subject | Email subject |

The sender address and SMTP address may constantly change.

Attackers may also use Gmail, Hotmail, or other email providers.

---

## Recipient & Time Analysis

Search results should include:

- Recipient addresses
- Time information

If repeated malicious emails are sent to the same users, this may indicate that the addresses were leaked or shared on websites such as **PasteBin**.

---

## Email Address Discovery

Attackers can find email addresses using tools such as **theHarvester** on Kali Linux.

Publicly posting personal email addresses can provide a potential attack vector.

LetsDefend recommends not explicitly sharing such information.

---

## Time Analysis

Emails sent outside office hours may indicate that the attacker is located in a different time zone.

Gathering this information can help understand the nature of the attack.

---

# 3. What is an Email Header and How to Read Them?

## What is an Email Header?

The email header is a section of an email containing information such as:

- Sender
- Recipient
- Date

It also contains components such as:

- `Return-Path`
- `Reply-To`
- `Received`

The header provides information that can be used to investigate the email and its route.

---

## What Does the Email Header Do?

### Identify the Sender and Recipient

The **From** and **To** fields identify who is sending and receiving the email.

Example:

```text
From: ogunal@letsdefend.io
To: info@letsdefend.io
```

---

### Spam Detection

Header analysis and other methods can help detect spam emails.

This can prevent users from receiving unwanted spam messages.

---

### Track an Email's Route

The email header can be used to investigate the route an email took.

For example, an email may appear to come from:

```text
ogunal@letsdefend.io
```

But this does not by itself prove that the email actually came from the `letsdefend.io` domain or from an authorized server.

Header information can be used to investigate this.

---

# 4. Important Email Header Fields

## From

The `From` field shows the **name and email address of the sender**.

---

## To

The `To` field contains the details of the recipient, including their name and email address.

**CC (carbon copy)** and **BCC (blind carbon copy)** also contain recipient information.

---

## Date

The `Date` field shows the timestamp indicating when the email was sent.

Gmail usually follows a format similar to:

```text
Wed, 16 Nov 2021 16:57:23
```

---

## Subject

The `Subject` is the topic of the email.

It summarizes the content of the email body.

---

## Return-Path / Reply-To

LetsDefend describes this field as the address to which a reply is sent.

When replying to an email, the reply is sent to the address specified in this field.

> **Important:** In the practical analysis, compare the `From` and `Reply-To` information.

---

## Domain Key and DKIM Signatures

**Domain Key** and **DomainKeys Identified Mail (DKIM)** are email signatures that help email service providers identify and authenticate emails, similar to SPF signatures.

---

## Message-ID

The `Message-ID` is a unique combination of letters and numbers that identifies an email.

According to the course:

> No two emails will have the same Message-ID.

---

## MIME-Version

**MIME — Multipurpose Internet Mail Extensions** is an Internet coding standard.

It converts non-text content such as:

- Images
- Videos
- Attachments

into text so that the content can be attached to an email and sent via **SMTP (Simple Mail Transfer Protocol)**.

---

## Received

The `Received` section lists the mail servers an email passed through before arriving in the recipient's inbox.

The entries are listed in **reverse chronological order**:

- The server at the **top** is the last server the email passed through.
- The server at the **bottom** is where the email originated.

This field is especially useful when investigating the route an email took.

---

## X-Spam Status

`X-Spam Status` shows the spam score of an email message.

It can show:

- Whether the message is classified as spam.
- The spam score.
- The spam threshold.

If an email meets or exceeds the spam threshold, it can be automatically classified as spam and sent to the Spam folder.

---

# 5. How to Access an Email Header

## Gmail

1. Open the email.
2. Click the **three dots (`...`)** at the top right.
3. Click **Download message**.
4. Open the downloaded `.eml` file with a notebook/text application.

---

## Outlook

1. Open the email.
2. Go to:
   **File → Info → Properties**
3. View **Internet headers**.

---

# 6. Email Header Analysis

When a phishing email is suspected, LetsDefend focuses on key questions that help determine whether the email is legitimate.

## Two Key Questions

### 1. Was the email sent from the correct SMTP server?

### 2. Are the `From` and `Return-Path / Reply-To` details the same?

---

## 6.1 Was the Email Sent From the Correct SMTP Server?

The `Received` field can be used to examine the path the email took.

Example from the course:

```text
SMTP IP: 101[.]99.94.116
```

The sender appeared to be from:

```text
letsdefend.io
```

Under normal circumstances, the question becomes:

> Should `letsdefend.io` be using `101[.]99.94.116` as its mail server?

To investigate this, LetsDefend recommends checking the **MX servers** actively used by the domain.

### MXToolbox

**MXToolbox** can show the MX servers used by a domain.

In the course example, `letsdefend.io` uses **Google addresses** as its email server.

There was no relationship between those Google mail servers and:

```text
emkei[.]cz
101[.]99.94.116
```

Therefore, the examination showed that the email did **not** come from the original/authorized address and was **spoofed**.

### Investigation Flow

```text
Email
  ↓
Check From domain
  ↓
Check Received / SMTP IP
  ↓
Check domain's MX servers
  ↓
Compare the SMTP server with the legitimate mail infrastructure
  ↓
Determine whether the sender infrastructure matches
```

---

# 7. From vs Return-Path / Reply-To

Except in exceptional cases, LetsDefend expects the sender of the email and the address receiving replies to be the same.

Attackers can abuse this in phishing attacks.

### Example scenario

An attacker sends an email that appears to be from someone at a trusted organization.

The attacker may place a legitimate employee's email address in the `Reply-To` field.

This can make the email look more convincing because the fake sender address may not stand out when the victim replies.

---

## How to Analyze It

Compare:

```text
From:
Reply-To:
```

If they are different, this is a **suspicious indicator**.

However:

> A different `From` and `Reply-To` does not automatically mean the email is phishing.

The event must be considered as a whole.

Additional suspicious indicators could include:

- Malicious attachment
- Malicious URL
- Misleading email content

---

# 8. Static Analysis

## What is Static Analysis?

Static analysis means examining suspicious email content, URLs, domains, IP addresses, and files **without executing them**.

The goal is to gather information before interacting with potentially dangerous content.

---

## HTML Emails & Hidden URLs

Email programs support HTML, allowing attackers to create attractive emails.

Attackers can use HTML to:

- Hide malicious URLs behind buttons.
- Hide malicious destinations behind harmless-looking text.

The address displayed to the user can be different from the actual destination.

The real address can be seen by hovering over the link.

> **Do not trust how a link looks. Check the actual destination.**

---

## New Domains

LetsDefend states that attackers commonly:

1. Obtain a new domain.
2. Use it for a phishing attack.
3. Complete the attack within a few days.

Therefore:

> A new domain in an email can be more likely to be associated with phishing.

A new domain is an indicator to investigate, not automatic proof of malicious activity.

---

## VirusTotal

VirusTotal can be used to check:

- Web addresses
- Files

It can show whether antivirus engines have detected the URL or file as harmful.

### Important limitation

VirusTotal may show a previous analysis instead of analyzing the item from scratch.

Example:

```text
Result: Harmless
Last analysis: 9 months ago
```

That old result may no longer represent the current state of the website.

An attacker may have had a harmless website when it was previously scanned and later changed it into a phishing site.

Therefore, check the age of the analysis and re-analyze when appropriate.

---

## Previous VirusTotal Scans

LetsDefend notes that if a site was previously scanned by VirusTotal, the attacker may have been checking the detection rate of the site during the preparation phase.

A new analysis may produce a different result if the site's behavior or content has changed.

---

## Static vs Dynamic

Static analysis of a file can provide insight into the file's **capacity/capability**.

However:

> Static analysis can take a long time.

Dynamic analysis can sometimes provide the required information more quickly by observing the file's behavior.

---

## Cisco Talos Intelligence

Cisco Talos Intelligence provides reputation information for IP addresses.

The SMTP IP of a suspicious email can be checked in Talos to determine its reputation and whether it appears on a blacklist.

LetsDefend states that if the SMTP address is blacklisted, it can be assumed that the attack was carried out on a **compromised server**.

---

# 9. Dynamic Analysis

## What is Dynamic Analysis?

Dynamic analysis involves safely interacting with suspicious URLs or files and examining the changes or behavior they produce.

Suspicious content should not be run directly on a personal computer.

Instead:

```text
Suspicious URL/File
        ↓
Safe / Sandbox Environment
        ↓
Interact or Execute
        ↓
Observe Behavior
```

---

## Browserling

**Browserling** can be used to quickly check web addresses contained in emails.

### Advantage

You are not visiting the website directly on your own computer.

This means you are not exposed directly to a possible browser zero-day vulnerability affecting your own browser.

### Disadvantage

If the website downloads a malicious file, Browserling may not allow you to run that file.

This can interrupt the analysis.

---

## Check the URL Before Visiting

Before accessing a link from an email, examine the URL.

The URL may contain important information.

### Example

A phishing URL may contain the user's email address as a parameter:

```text
https://popularshoppingsite[.]com/login?email=user@example.com
```

Even if the user never enters their password, visiting the website could tell the attacker that the email address is valid.

The attacker could then use valid users for later social-engineering attacks.

### Therefore

Before visiting a suspicious website:

1. Examine the URL.
2. Look for important/sensitive information.
3. Change or remove information such as email addresses when necessary.
4. Access the website using a safe environment.

---

## Sandbox Environments

Sandbox environments allow suspicious files and websites to be examined without the risk of infecting the analyst's computer with malware.

LetsDefend lists:

- **VMRay**
- **JoeSandbox**
- **AnyRun**
- **Hybrid Analysis (Falcon Sandbox)**

---

## Malware Can Delay Its Behavior

Malware can wait for a certain amount of time before taking action.

This can make detection more difficult.

Therefore:

> **No immediate malicious activity does not automatically mean a file is safe.**

The analyst may need to wait for the malware to take action before deciding that the file is not malicious.

---

## No URL or File Does Not Mean Safe

An email does not need to contain an obvious URL or attachment to be malicious.

LetsDefend notes that attackers may send malware as an **image** to avoid detection by analysis tools.

Therefore:

> No obvious URL or file does not automatically mean the email is safe.

---

# 10. Practical SOC Alert — SOC146

## Phishing Mail Detected — Excel 4.0 Macros

This LetsDefend alert is based on a real phishing attack.

| Field | Value |
|---|---|
| Severity | High |
| EventID | 93 |
| Event Time | `2021-06-13T14:13:28+03:00` |
| Rule | `SOC146 - Phishing Mail Detected - Excel 4.0 Macros` |
| Role | Security Analyst |
| Alert Type | Exchange |
| MITRE ATT&CK | `T1566` |
| SMTP Address | `24.213.228.54` |
| Device Action | Allowed |
| Email Subject | `RE: Meeting Notes` |
| Source Address | `trenton@tritowncomputers.com` |
| Destination Address | `lars@letsdefend.io` |

### Why it matters

The alert involves an **Excel 4.0 Macro**, making the suspicious attachment important to investigate.

The `Device Action` is:

```text
Allowed
```

So the email was allowed through and requires investigation.

The SMTP address can also be investigated using the reputation techniques covered in the course.

---

# 11. Quick Revision Cheat Sheet

## Phishing

**Goal:**
Trick users into clicking malicious links or running malicious files.

**Cyber Kill Chain:**
Delivery

**Key idea:**
Phishing exploits the human factor.

---

## Information Gathering

### Spoofing

Check:

- SPF
- DKIM
- DMARC
- SMTP IP
- MX records
- WHOIS

### Email Traffic

Search using:

- Sender
- SMTP IP
- Domain
- Sender/account name
- Subject
- Recipient
- Time

### Other Clues

- Repeated targeting of users
- Publicly exposed email addresses
- Emails outside office hours
- theHarvester for email discovery

---

# Email Header

Important fields:

| Field | What to remember |
|---|---|
| `From` | Sender |
| `To` | Recipient |
| `Date` | Time email was sent |
| `Subject` | Email topic |
| `Return-Path / Reply-To` | Address used for replies |
| `Message-ID` | Unique email identifier |
| `MIME-Version` | Supports non-text content/attachments |
| `Received` | Mail-server route |
| `X-Spam Status` | Spam classification/score |
| `DKIM` | Email authentication/signature |

### Received

Remember:

```text
Top = last server
Bottom = originating server
```

---

# Header Analysis

Ask:

### 1. Did the email come from the correct SMTP server?

```text
From domain
      ↓
Received / SMTP IP
      ↓
MX records
      ↓
Compare
```

### 2. Do From and Reply-To match?

```text
From ≠ Reply-To
        ↓
Suspicious indicator
        ↓
Investigate the entire email
```

A mismatch alone does **not** prove phishing.

---

# Static vs Dynamic Analysis

## STATIC = LOOK

**Don't execute. Inspect.**

Check:

- Actual URL destination
- HTML
- Domain
- Newness of domain
- VirusTotal
- Age of VirusTotal results
- SMTP IP reputation
- Cisco Talos
- File characteristics

---

## DYNAMIC = DO + OBSERVE

**Safely interact/execute and observe behavior.**

Use safe environments such as:

- Browserling
- VMRay
- JoeSandbox
- AnyRun
- Hybrid Analysis / Falcon Sandbox

Remember:

- Inspect URLs before visiting them.
- Remove/change sensitive information in URL parameters when necessary.
- Don't run suspicious files on your personal computer.
- Malware may delay its behavior.
- No immediate behavior does not automatically mean safe.
- No visible URL/file does not automatically mean safe.

---

# 🧠 One-Minute Mental Model

When a suspicious phishing email arrives:

```text
                 PHISHING EMAIL
                       │
                       ▼
              ┌─────────────────┐
              │ INFORMATION      │
              │ GATHERING        │
              └────────┬────────┘
                       │
                       ▼
                CHECK HEADER
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        SMTP SERVER          FROM vs
        CORRECT?             REPLY-TO
             │                   │
             └─────────┬─────────┘
                       ▼
                STATIC ANALYSIS
                       │
              Inspect without
                  executing
                       │
                       ▼
               DYNAMIC ANALYSIS
                       │
              Safely interact/
                 execute
                       │
                       ▼
                OBSERVE BEHAVIOR
                       │
                       ▼
              DETERMINE WHETHER
               EMAIL IS MALICIOUS
```

## The 4 things to remember most

> **1. Phishing = exploiting the human factor.**

> **2. Header analysis = find out who sent it, where it came from, and where replies go.**

> **3. Static analysis = inspect without executing.**

> **4. Dynamic analysis = safely execute/interact and observe behavior.**
