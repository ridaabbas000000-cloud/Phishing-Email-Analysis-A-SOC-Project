# Phishing-Email-Analysis-A-SOC-Project
![Category](https://img.shields.io/badge/Domain-SOC_&_Incident_Response-blue?style=for-the-badge)

![Severity](https://img.shields.io/badge/Severity-CRITICAL-red?style=for-the-badge)

![SIEM](https://img.shields.io/badge/SIEM-Splunk-orange?style=for-the-badge)

![Framework](https://img.shields.io/badge/Framework-MITRE_ATT%26CK-green?style=for-the-badge)


## 📁Repository Structure
* `README.md` — Main project overview, IOCs, workflow & MITRE mapping
* `SOC Incident Report.pdf` — Full IR report for Incident CLD-012 (Reports 1–5)
* `SOC Triage Note.pdf` — Work note / Triage closure for Report 6 (False Positive)
 
## 📌 Project Overview
An end-to-end investigation of a simulated payroll phishing campaign, starting from six user-reported emails and ending in a confirmed account compromise and escalation to incident response.
Five of the six reports belonged to one campaign, and the sixth was closed as a false positive. The investigation covers email header and authentication analysis (SPF, DKIM, DMARC), IOC extraction and enrichment, campaign scoping and sign-in log analysis in Splunk, MITRE ATT&CK mapping, and containment and eradication recommendations. Searching the logs showed that 32 users received the campaign although only 5 reported it, 3 clicked the link, and 1 account was compromised

> **Note:** all data in this project is simulated. The company, users, and domains are fictional.



## 📑 Executive Summary

Six employees at a fictional company reported suspicious emails. I triaged them, found that five belonged to one fake payroll campaign, and closed the sixth as a false positive. Searching the logs showed the real scale of the attack:

| Measure | Result |
|---|---|
| Reports received | 6 (5 campaign, 1 false positive) |
| Users who received the campaign | **32** |
| Users who clicked the link | **3** |
| Users who submitted credentials | **1** |
| Accounts confirmed compromised | **1** (`priya.sharma`) |

The compromised account was signed in to from an IP located in Bucharest, Romania, six minutes after the user submitted credentials, using legacy authentication that bypassed MFA. The user's normal location was London, UK. The incident was escalated to incident response with containment and eradication actions requested.
 
## Scenario

On 17 August 2026, employees reported an email titled "Payroll update: action required before Friday payrun". It appeared to come from Company HR and asked staff to confirm bank details.


## ⚙️ Investigation Workflow

1. **Alert intake:** record what was reported, by whom, and what the alert claims.
2. **Header analysis:** compare From, Return-Path, and Reply-To, trace the Received chain, and check authentication results (SPF, DKIM, DMARC).
3. **IOC analysis:** read the email body, extract the IOCs (sender, domain, IP, URL, attachments), and enrich them with VirusTotal, AbuseIPDB, URLScan, and WHOIS. 
4. **Campaign analysis:** search the message trace logs to find how far the attack spread: who received it and who clicked.
5. **Compromise analysis:** review the sign-in logs of the users who clicked to find which accounts the attacker actually reached.
6. **Containment and escalation:** recommend the actions needed to stop and remove the attacker, and escalate to incident response.
7. **Documentation:** write the incident report with the evidence, timeline, IOCs, and MITRE mapping.
   
## 🔍 Key Findings

* **Spoofed Sender:** `From` displayed `payroll@cloudora.com`, but `Return-Path`, `Reply-To`, and `Message-ID` routed through `cloudora-hr-portal.example`.
* **Authentication Failures:** SPF failed, DKIM signature was missing, and DMARC failed.
* **Clicked ≠ Compromised:** 3 users clicked the link, but sign-in log auditing verified that only 1 submitted credentials.
* **Confirmed Compromise:** Detected an **Impossible Travel** anomaly (London at 07:12 UTC vs. Bucharest at 08:26 UTC), legacy protocol sign-in bypassing MFA, and active Exchange Online sessions.

 ##  Indicators of Compromise (IoCs)
| Type | Value |
| :--- | :--- |
| **Sending IP** | 192.0.2.10 |
| **Attacker sign-in IP** | 192.0.2.77 |
| **Attacker domain** | cloudora-hr-portal.example |
| **Hostname** | mail-relay-out.cloudora-hr-portal.example |
| **Return-Path** | bounce@cloudora-hr-portal.example. |
| **Reply-To** | hr-support@cloudora-hr-portal.example. |
| **URL** | `http://cloudora-hr-portal.example/payroll/confirm?empid=EMP1042&token=7f3ac9` |
| **Subject** | Payroll update: action required before Friday payrun |


## 🗺️ MITRE ATT&CK Mapping
| Attack Phase | MITRE ID | Technique Name | Details in Your Investigation |
| :--- | :--- | :--- | :--- |
| **Phishing Email** | **T1566.002** | Spearphishing Link | Phishing email containing fake payroll link sent to 32 users. |
| **Compromised Account** | **T1078.004** | Valid Accounts: Cloud Accounts | Attacker logged into M365 / Exchange Online using Priya's stolen credentials. |
| **Impossible Travel** | **T1535** | Unused/Unusual Location | Sign-in flagged from Bucharest, Romania, 6 minutes after activity in London, UK. |

## ⚖️ Final Verdict
* **Reports 1–5:** **True Positive (Malicious)** — Confirmed payroll phishing campaign. Escalated to Incident Response with containment and eradication requested.
* **Report 6:** **False Positive** — Legitimate email, closed during initial triage with a triage note.

## 🛠️ Tools & Technologies Used
* **SIEM:** Splunk for searching the message trace and sign-in logs

* **Threat Intelligence / Enrichment:** VirusTotal, AbuseIPDB for IOC enrichment

* **Analysis:** A text editor for reading raw email headers
