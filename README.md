# Phishing Email Investigation

A hands-on SOC analyst exercise: I took a real phishing sample, analysed it the way an L1 analyst would, and wrote up the findings as an incident report.

**Full write-up:** [report.md](report.md)

> The email comes from the public [phishing_pot](https://github.com/rf-peixoto/phishing_pot) practice collection. This is a learning project, not an incident from a real company. All IPs and links are defanged so they can't be clicked by accident.

## What I did

1. **Read the email headers** (sender, Return-Path, Received chain, SPF / DKIM / DMARC results) and compared them with what the email claimed to be.
2. **Read the email body** and looked at the wording, branding and every link.
3. **Decoded a disguised link.** The links pointed to a number-only address (`026250572540`), which is an IP address written in octal. Decoded, it is `178[.]162[.]245[.]96`.
4. **Checked both IPs** (the sending server and the link host) on AbuseIPDB.
5. **Mapped the behaviour to MITRE ATT&CK** and wrote an incident report with a verdict and recommended actions.

## Key findings

- The sender name and the real return address did not match, and SPF failed for the first sending server.
- There was no DKIM signature and Microsoft's composite authentication check failed.
- The brand in the sender name differed from the brand advertised in the email body.
- Every link, including "Unsubscribe", went to the same disguised IP address.
- Both the sending IP and the link IP had phishing reports on AbuseIPDB, with reports dated close to the email's date.
- **Verdict:** assessed as phishing. **MITRE ATT&CK:** T1566.002 (Phishing: Spearphishing Link) and T1204.001 (User Execution: Malicious Link).

## Tools used

- Plain text editor, for reading the raw `.eml` file safely (the email was never opened in a mail client)
- AbuseIPDB, for IP reputation
- MITRE ATT&CK, for technique mapping

## Repository contents

```
phishing-investigation/
├── README.md
├── report.md
├── evidence/          the .eml sample
└── screenshots/       AbuseIPDB results and the MITRE ATT&CK page
```

## Limitations

I did not visit the link or the website. Threat-intelligence results are community reports, so they support the verdict but are not proof on their own.
