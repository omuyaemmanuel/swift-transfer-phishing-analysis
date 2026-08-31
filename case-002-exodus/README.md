## 1. Executive Summary

This investigation analyzes a real phishing email that impersonated Exodus, a cryptocurrency wallet provider.

The message attempted to convince the recipient that account re-identification was mandatory and that failure to comply would result in account restrictions.

The email contained a deceptive hyperlink. The visible link was presented as:

`Exodus.com/identify`

However, the actual hyperlink destination was:

`hxxps://pxlme[.]me/zAVvQVdl`

Header analysis identified significant discrepancies between the visible sender identity and the underlying email infrastructure.

---

## 2. Initial Findings

The email claimed to originate from:

`team@exodus.com`

However, the envelope sender and Return-Path were:

`admin@apps.aishwaryainteriors.in`

This represents a significant sender-domain mismatch.

The email authentication results were:

- SPF: NONE
- DKIM: PASS
- DKIM signing domain: `apps.aishwaryainteriors.in`
- DMARC: FAIL
- DMARC header.from: `exodus.com`

The DKIM signature was valid for the sending domain, but the signing domain did not align with the visible From domain.

---

## 3. Email Header Analysis

### Visible Sender

```text
From: Exodus <team@exodus.com>
````

### Envelope Sender

```text
Sender: admin@apps.aishwaryainteriors.in
```

### Return-Path

```text
Return-Path: admin@apps.aishwaryainteriors.in
```

### Message ID

```text
<H65MKHQRQHU4.KDODSKQQ3JCD2@aishwaryainteriors.in>
```

The Message-ID also references the `aishwaryainteriors.in` domain rather than `exodus.com`.

---

## 4. Authentication Analysis

### SPF

```text
spf=none
```

The receiving system reported that `apps.aishwaryainteriors.in` did not designate the observed sender as an authorized host.

### DKIM

```text
dkim=pass
header.d=apps.aishwaryainteriors.in
```

DKIM validation succeeded.

However, a DKIM pass does not establish that the message is legitimate. The signature was associated with `apps.aishwaryainteriors.in`, not `exodus.com`.

### DMARC

```text
dmarc=fail
header.from=exodus.com
```

The visible From domain was `exodus.com`, while the authenticated domains were associated with `apps.aishwaryainteriors.in`.

This represents a domain-alignment failure.

---

## 5. URL Analysis

The email displayed the following text:

```text
Exodus.com/identify
```

However, the HTML revealed the actual destination:

```text
https://pxlme.me/zAVvQVdl
```

This is a classic example of deceptive hyperlink text.

### IOC

```text
hxxps://pxlme[.]me/zAVvQVdl
```

The URL should not be opened directly on a normal workstation.

Historical threat-intelligence reporting has associated `pxlme.me` infrastructure with phishing and redirect activity.

---

## 6. Social Engineering Indicators

The email contains several social-engineering characteristics:

### Urgency

The recipient is given one week to complete the requested action.

### Threat of account restriction

The message claims the user's account will eventually be blocked if the action is not completed.

### Impersonation

The attacker presents the message as originating from Exodus.

### Required action

The recipient is instructed to re-identify through a provided link.

### Deceptive URL

The displayed URL suggests `Exodus.com`, while the actual hyperlink points to `pxlme.me`.

---

## 7. Infrastructure Analysis

### Earliest Observed Source IP

```text
92.60.40.237
```

The earliest visible SMTP connection in the supplied headers shows:

```text
Received: from [92.60.40.237]
```

The system presented the hostname:

```text
LAPTOP-J4B2ABC4
```

and authenticated to the mail server as:

```text
admin@apps.aishwaryainteriors.in
```

### Intermediate Mail Infrastructure

```text
192.185.51.139
```

This IP appears later in the SMTP chain and should therefore be treated as intermediate mail infrastructure unless additional evidence establishes malicious ownership or control.

---

## 8. Mail Security Detection

Microsoft Exchange Online assigned:

```text
X-MS-Exchange-Organization-SCL: 7
```

The message was also processed through the Junk Email filtering path.

This provides additional evidence that the receiving mail-security system considered the message highly suspicious.

---

## 9. Indicators of Compromise

| IOC                                | Type   | Role                          | Assessment          |
| ---------------------------------- | ------ | ----------------------------- | ------------------- |
| `team@exodus.com`                  | Email  | Spoofed/impersonated identity | Suspicious          |
| `admin@apps.aishwaryainteriors.in` | Email  | Envelope sender               | Suspicious          |
| `apps.aishwaryainteriors.in`       | Domain | DKIM/envelope domain          | Suspicious          |
| `pxlme.me`                         | Domain | Redirect infrastructure       | Phishing-associated |
| `hxxps://pxlme[.]me/zAVvQVdl`      | URL    | Phishing link                 | Malicious           |
| `92.60.40.237`                     | IPv4   | Earliest observed source      | Suspicious          |
| `192.185.51.139`                   | IPv4   | Intermediate mail relay       | Context             |

---

## 10. MITRE ATT&CK Mapping

### T1566.002 — Phishing: Spearphishing Link

The attacker used an email containing a deceptive link to direct the victim toward a phishing destination.

The technique is supported by:

* impersonation of Exodus;
* social-engineering content;
* deceptive hyperlink text;
* external redirect infrastructure.

---

## 11. Analyst Verdict

### Final Classification

**MALICIOUS — PHISHING**

### Confidence

**HIGH**

### Likely Objective

**Credential/account information theft**

The available evidence strongly supports a phishing attempt targeting Exodus users.

However, the available email evidence alone does not prove that credentials were successfully captured.

---

## 12. Recommended SOC Response

If this message were received within an enterprise environment:

1. Quarantine the email.
2. Block the confirmed malicious URL/domain where appropriate.
3. Search mailboxes for the same sender, subject, URL, and related IOCs.
4. Identify all recipients.
5. Determine whether any users interacted with the URL.
6. If interaction occurred, investigate endpoint and authentication telemetry.
7. Reset credentials if compromise is confirmed or suspected.
8. Monitor affected accounts for suspicious activity.
9. Add confirmed indicators to appropriate security controls.
10. Document the incident and preserve the original email as evidence.

---

## 13. Investigation Limitations

The exact final landing page associated with the URL should not be claimed without evidence directly tying that specific URL to a captured page.

Therefore:

**Confirmed:**

* The email impersonates Exodus.
* The visible and actual URLs differ.
* DMARC fails.
* The envelope sender does not match Exodus.
* The URL uses `pxlme.me`.
* The infrastructure has historical phishing associations.

**Not independently confirmed from the email alone:**

* The exact final landing page.
* Whether a victim submitted credentials.
* Whether credentials were successfully harvested.

---

## 14. Evidence Handling

Live malicious URLs should not be opened directly from a normal workstation.

URLs and domains are defanged in documentation where appropriate:

```text
hxxps://pxlme[.]me/zAVvQVdl
```
The original email should be retained separately as evidence and should not be published publicly if it contains sensitive information or active malicious payloads.
