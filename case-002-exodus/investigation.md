# Phishing Email Investigation — Exodus Impersonation

## 1. Case Overview

**Case Type:** Phishing Email Analysis
**Target Brand:** Exodus
**Date of Email:** 29 August 2022
**Initial Classification:** Suspicious
**Final Classification:** Phishing
**Severity:** High

### Executive Summary

An email claiming to be from Exodus was analyzed to determine whether it was legitimate or malicious.

The message used the sender address `team@exodus.com`, but the underlying sender and authentication information pointed to `apps.aishwaryainteriors.in`. The message also failed DMARC and Microsoft composite authentication.

The email attempted to create urgency by requiring the recipient to "re-identify" within one week and threatened account blocking if the recipient did not comply.

The displayed link appeared as `Exodus.com/identify`, while the actual hyperlink pointed to `https://pxlme.me/zAVvQVdl`.

Based on the combination of sender impersonation, authentication failures, infrastructure inconsistencies, deceptive URL usage, and social-engineering techniques, the email was classified as **phishing**.

---

## 2. Email Header Analysis

### Claimed Sender

```text
From: Exodus <team@exodus.com>
```

The visible sender claims to represent Exodus.

However, other header fields identify a different sending identity.

### Sender Identity

```text
Sender: admin@apps.aishwaryainteriors.in
Return-Path: admin@apps.aishwaryainteriors.in
```

The sender and return-path domains do not match the claimed `exodus.com` domain.

The message ID also uses the `aishwaryainteriors.in` domain:

```text
Message-Id: <...@aishwaryainteriors.in>
```

This indicates a significant identity mismatch.

**Evidence:** `evidence/01-email-header-authentication.png`

---

## 3. Email Authentication

The receiving mail system reported:

```text
spf=none
dkim=pass
dmarc=fail
header.from=exodus.com
compauth=fail
```

### SPF

SPF returned `none`. The sending IP was not established as an authorized sender for the claimed identity.

### DKIM

DKIM passed, but the signing domain was:

```text
apps.aishwaryainteriors.in
```

A successful DKIM signature therefore does not establish that the message was sent by Exodus.

### DMARC

DMARC failed because the authenticated sending identity did not align with the visible `From:` domain.

### Composite Authentication

Microsoft reported:

```text
compauth=fail
```

This provides additional evidence that the message could not be reliably associated with the claimed sender.

---

## 4. Mail Infrastructure

The message passed through several hosts before reaching the recipient.

The header identifies:

```text
Originating IP: 92.60.40.237
SMTP submission host: cp-in-1.webhostbox.net
Relay IP: 192.185.51.139
Relay host: gateway24.websitewelcome.com
```

The message was submitted using authenticated SMTP from a host identifying itself as:

```text
LAPTOP-J4B2ABC4
```

The infrastructure does not match what would be expected from a straightforward message originating from the claimed `exodus.com` identity.

Microsoft also assigned the message:

```text
X-MS-Exchange-Organization-SCL: 7
```

indicating a high spam-confidence classification.

**Evidence:** `evidence/01-email-header-authentication.png`

---

## 5. Social Engineering Analysis

The email attempts to persuade the recipient to take immediate action.

### Identified techniques

**Impersonation**

The message presents itself as an Exodus communication.

**Urgency**

The recipient is given a one-week deadline.

**Threat of account loss**

The message claims that failure to respond will result in loss of platform access and eventual account blocking.

**Mandatory action**

The recipient is told that re-identification is required.

These techniques are designed to reduce the likelihood that the recipient will independently verify the request.

**Evidence:** `evidence/02-phishing-email-content.png`

---

## 6. URL Analysis

The visible link text was:

```text
Exodus.com/identify
```

However, the underlying hyperlink was:

```text
https://pxlme.me/zAVvQVdl
```

The displayed destination and actual destination do not match.

This is a strong phishing indicator because the visible text attempts to make the recipient believe the link leads to Exodus while the actual destination uses a different domain.

The URL was not opened during the investigation.

**Evidence:** `evidence/03-deceptive-url.png`

---

## 7. Threat Intelligence

The domain used by the embedded URL was:

```text
pxlme.me
```

Historical threat-intelligence and abuse-reporting data associates the domain with phishing and malicious-link activity.

The email also passed through:

```text
192.185.51.139
```

which has historical abuse reports including phishing-related activity.

These reputation findings provide supporting context but do not, by themselves, prove that the specific sender controlled the infrastructure.

**Evidence:** `evidence/04-ip-threat-intelligence.png`

---

## 8. Indicators of Compromise

| Type   | Indicator                          | Relevance                       |
| ------ | ---------------------------------- | ------------------------------- |
| Email  | `team@exodus.com`                  | Claimed sender                  |
| Email  | `admin@apps.aishwaryainteriors.in` | Actual sender/envelope identity |
| Domain | `apps.aishwaryainteriors.in`       | DKIM signing domain             |
| Domain | `aishwaryainteriors.in`            | Message-ID domain               |
| URL    | `https://pxlme.me/zAVvQVdl`        | Embedded phishing URL           |
| Domain | `pxlme.me`                         | URL destination domain          |
| IP     | `192.185.51.139`                   | Mail relay infrastructure       |
| IP     | `92.60.40.237`                     | Apparent originating client IP  |
| Host   | `cp-in-1.webhostbox.net`           | SMTP submission host            |
| Host   | `gateway24.websitewelcome.com`     | SMTP relay                      |

---

## 9. MITRE ATT&CK Mapping

| Tactic            | Technique                                    | Evidence                                                     |
| ----------------- | -------------------------------------------- | ------------------------------------------------------------ |
| Initial Access    | T1566.002 — Phishing: Spearphishing Link     | Email contains a deceptive hyperlink                         |
| Initial Access    | T1566 — Phishing                             | Malicious email used to influence the recipient              |
| Defense Evasion   | T1036 — Masquerading                         | Sender impersonates Exodus                                   |
| Credential Access | T1056.002 — Input Capture: GUI Input Capture | Potentially relevant if the linked page requests credentials |

The final technique is treated as **potential** rather than confirmed because the phishing destination was not opened during the investigation.

---

## 10. Risk Assessment

**Severity: High**

The email presents a credible risk because it combines:

* Brand impersonation
* Authentication failures
* Sender-domain mismatch
* Deceptive hyperlink
* Urgency
* Account-blocking threats
* Suspicious external infrastructure

If a recipient followed the link and submitted wallet credentials, recovery information, or other sensitive information, the attacker could potentially gain unauthorized access to the victim's account or assets.

---

## 11. Recommended Response

If this message were received in an enterprise environment:

1. Do not click the embedded link.
2. Quarantine the message.
3. Block the identified malicious URL/domain where appropriate.
4. Search mail logs for other messages containing the same URL, sender, or domains.
5. Identify users who interacted with the message.
6. If credentials were submitted, initiate credential-reset procedures.
7. Review authentication and endpoint logs for affected users.
8. Report the phishing campaign through the organization's established security process.
9. Preserve the original email and headers for further investigation.

---

## 12. Final Verdict

**Classification: PHISHING**

The email impersonates Exodus and attempts to persuade the recipient to follow a deceptive link.

The strongest indicators are the mismatch between the claimed sender and authenticated infrastructure, DMARC and composite authentication failures, the deceptive `Exodus.com/identify` hyperlink, the use of `pxlme.me`, and the use of urgency and account-threat language.

The available evidence is sufficient to classify the message as a phishing attempt without interacting with the destination website.

---

## 13. Evidence

The investigation was supported by four screenshots captured during analysis:

* `01-email-header-authentication.png` — Header and authentication evidence
* `02-phishing-email-content.png` — Social-engineering content
* `03-deceptive-url.png` — Displayed versus actual hyperlink
* `04-ip-threat-intelligence.png` — Infrastructure reputation evidence

