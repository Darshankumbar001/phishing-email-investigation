# Phishing Email Investigation: Incident Report

**Analyst:** Darshan
**Sample:** `evidence/sample-10075.eml, taken from the public phishing_pot collection
**Email date:** 15 Apr 2026, 08:37 UTC
**Verdict:** Phishing / fraudulent email (assessed, fairly high confidence)
**Severity:** Low to Medium (no evidence in this sample that anyone clicked)

> Note: this is a public practice sample, not an incident from a real company. All web addresses and IPs below are written in defanged form so they can't be clicked by accident.

---

## 1. Summary

An email titled "A simple way to feel a bit more balanced each day | No. KLU-540727" arrived pretending to be a wellness brand. It failed sender checks, its content didn't match the sender, and every link in it pointed to a disguised IP address that has phishing reports against it. I assessed it as phishing and mapped it to MITRE ATT&CK T1566.002.

## 2. Email details

| Field | Value |
|---|---|
| Subject | A simple way to feel a bit more balanced each day \| No. KLU-540727 |
| From | "The Calm Collective" HEALTH@TheCalmCollectiveUES[.]com |
| Return-Path | iit6b9osb968n7d@wkavi[.]onmicrosoft[.]com |
| First sending server | 64[.]120[.]94[.]224 |
| Date | Wed, 15 Apr 2026 08:37:45 +0000 |

## 3. Header analysis

- **Return-Path does not match From.** The visible sender domain and the real return address are unrelated, and the return address is a random-looking name on a Microsoft onmicrosoft domain.
- **SPF failed for the first sending server.** The first hop (64[.]120[.]94[.]224) is not allowed to send for that domain. A later "SPF pass" appears for a Microsoft relay IP (52[.]102[.]171[.]40), but that only shows the mail passed through Microsoft's servers. It says nothing good about the original sender.
- **No DKIM signature, no DMARC policy, and Microsoft's composite authentication check failed** (compauth=fail).

## 4. Body analysis

- The sender name is "The Calm Collective", but the email is an advert for a product called **TruFull**. The brand changes inside one email.
- Pressure tactics: a "Stock Status: Low" badge, a "CLAIM YOUR SUPPLY" button and a fake testimonial.
- The footer says the email was sent to "a registered member", which there is no evidence of.
- **All links go to a bare number address**, `hxxp://026250572540/...`, instead of a domain name. This includes the product image, the button, "Unsubscribe" and "Privacy Policy".
- The number `026250572540` is an IP address written in octal. Decoded, it is **178[.]162[.]245[.]96**.
- The email loads a remote image from `assets[.]adversial[.]com`, which could let the sender see that the email was opened (observation only, not confirmed).

## 5. Indicators of compromise (IOCs)

| Type | Value |
|---|---|
| Sender address | HEALTH@TheCalmCollectiveUES[.]com |
| Return-Path | iit6b9osb968n7d@wkavi[.]onmicrosoft[.]com |
| Sending IP | 64[.]120[.]94[.]224 |
| Link host (IP) | 178[.]162[.]245[.]96 (written as 026250572540 in the email) |
| Link URL pattern | hxxp://026250572540/[random tracking code] |
| Image URL | hxxps://assets[.]adversial[.]com/resources/offers/cbd2.png |

## 6. Threat intelligence checks (AbuseIPDB, November 2026)

| IP | Result |
|---|---|
| 64[.]120[.]94[.]224 | Leaseweb, Singapore (hosting provider). 7 reports from 4 reporters, mostly phishing, including reports dated 5 Apr and 19 Apr 2026. |
| 178[.]162[.]245[.]96 | Leaseweb, Germany (hosting provider). 3 reports from 2 reporters, all phishing, including one dated 14 Apr 2026, the day before this email. |

Both IPs show a 0% abuse score, but that score decays when there are no recent reports. It does not mean the IPs are clean. Community reports are supporting evidence, not proof, and hosting IPs can change hands over time.

## 7. MITRE ATT&CK mapping

| Stage | ID | Technique | How it appears here |
|---|---|---|---|
| Initial Access | T1566.002 | Phishing: Spearphishing Link | Email with a deceptive link hidden behind an octal-encoded IP |
| Execution | T1204.001 | User Execution: Malicious Link | The victim must click the link for the attack to work |

T1566.001 (attachment) does not apply because the email had no attachment. T1566.003 (via service) and T1566.004 (voice) do not apply because it was delivered by normal email.

## 8. Conclusion

Based on the failed authentication, the mismatched sender and brand, the disguised link addresses, and the phishing reports on both the sending IP and the link IP, I assess this email as phishing. I did not open the link or the website, so I can't say what the destination does.

## 9. Recommended actions

1. Block the sender domain and the sending IP at the mail gateway.
2. Block the link IP and the image domain at the web proxy or firewall.
3. Search mail logs for the same subject, sender and link pattern to find other recipients, and remove the email from their mailboxes.
4. If any user clicked the link, reset their password, review their recent sign-ins and check their device.
5. Remind users not to click "unsubscribe" links in unexpected emails and to report them instead.

## 10. Limitations

- The link destination was not visited, for safety.
- Threat-intelligence results are community reports and may be incomplete.
- This is a single sample, so no wider campaign conclusions are made.

## 11. Evidence

| File | What it shows |
|---|---|
| `screenshots/01-ip-reputation.png` | AbuseIPDB result for the sending IP |
| `screenshots/02-link-ip-reputation.png` | AbuseIPDB result for the link IP |
| `screenshots/03-mitre-technique.png` | MITRE ATT&CK page for T1566.002 |
