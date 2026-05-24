# My Helpdesk Lab: Connecting Jira to Active Directory

## 📋 Project Overview
After building my multi-server Active Directory home lab, I wanted to practice how a real IT Support technician handles day-to-day work. I set up a free **Jira Service Management** instance to act as my corporate helpdesk portal. 

Instead of just clicking around randomly in my servers, I used Jira to submit real support requests, assign them to myself, and track the troubleshooting steps manually from start to finish.

---

##  Tickets Processed & Solved (Step-by-Step)

### Ticket 1: New Employee Onboarding (Service Request)
<img width="2223" height="1206" alt="Screenshot (188)" src="https://github.com/user-attachments/assets/a593fb54-1295-435e-981d-6f418f406d17" />

- **The Ticket:** HR submitted an onboarding request for a new hire named `John Doe` starting in the Finance department.
- **My Action in the Lab:** 
  1. Picked up the ticket in Jira and moved it to `In Progress`.
  2. Logged into my main Domain Controller (**DC-01**) and opened *Active Directory Users and Computers*.
  3. Created the user account `jdoe` inside the `Finance` organizational folder.
<img width="1247" height="743" alt="Screenshot (189)" src="https://github.com/user-attachments/assets/2c7be330-0877-4865-85fe-a18dd6197624" />
  4. Added him to the **SG_Finance** security group so he has the right file access.
<img width="1132" height="749" alt="Screenshot (190)" src="https://github.com/user-attachments/assets/f870cf1d-a32b-48bc-a213-dd5a23e84fdd" />
  5. Opened the *Microsoft Entra Connect Sync* tool and forced a manual **Delta Sync** to send his account to the cloud.
<img width="953" height="782" alt="Screenshot (192)" src="https://github.com/user-attachments/assets/15b60ed9-0619-40bb-b4c5-3230a1c7a323" />

- **The Result:** Verified John Doe appeared successfully inside the cloud-based **Microsoft Entra Admin Center** with an "On-premises synced" status, then closed the Jira ticket.
<img width="2047" height="1260" alt="Screenshot (193)" src="https://github.com/user-attachments/assets/ca83ebec-f34c-4897-b594-ff99b13dcd7a" />
<img width="2104" height="1233" alt="Screenshot (195)" src="https://github.com/user-attachments/assets/0f1c2f12-3721-4b0e-a493-b7eeeae60c27" />

---

### Ticket 2: Mapped Drive Access Issue (Troubleshooting)
<img width="955" height="819" alt="Screenshot (201)" src="https://github.com/user-attachments/assets/33198ece-0d65-48c3-932f-7de8c90fd633" />

- **The Ticket:** A user from the IT department filed a ticket saying they got an "Access Denied" error when trying to open their team's shared network folder.
<img width="1735" height="1000" alt="Screenshot (199)" src="https://github.com/user-attachments/assets/e7a4fa71-25ca-4de6-a03c-6f4ade5a30d2" />

- **My Action in the Lab:** 
  1. Opened the user's properties in Active Directory and found they were completely missing from their team's security group.
  2. Manually added the user to the **SG_IT** group.
<img width="1180" height="782" alt="Screenshot (202)" src="https://github.com/user-attachments/assets/b610aa52-623d-4dec-a0ab-52ba5c8708bb" />
   3. *The Real Fix:* Discovered that when I moved my server IPs to a new network network scheme, my Group Policy (GPO) path was still trying to look for the old IP address! 
  4. Edited the GPO file path to use the server's clean name (`\\dc01.lab.local\design_department`) instead of a hardcoded IP address.
- **The Result:** Logged into the Windows 10 client VM as that user, ran `gpupdate /force`, and watched the **G: Drive** pop up on the desktop with full access working. Marked the ticket as `Resolved` in Jira.
<img width="993" height="679" alt="Screenshot (203)" src="https://github.com/user-attachments/assets/b5054872-0967-40cd-a946-2e0da5fc679d" />
<img width="957" height="811" alt="Screenshot (204)" src="https://github.com/user-attachments/assets/169f96db-24f5-4580-bd7f-0de3a8246500" />
<img width="2120" height="1146" alt="Screenshot (212)" src="https://github.com/user-attachments/assets/25adc996-f340-43c5-926d-6c85adfed581" />
---

### Ticket 3: Urgent Account Lockout (Incident Management)
- **The Ticket:** An employee intentionally typed their password wrong too many times, triggering our security policy and locking them out of their computer right before a big meeting.
  <img width="1953" height="836" alt="Screenshot (208)" src="https://github.com/user-attachments/assets/f948bb60-fee1-489a-908a-8fc986ea5164" />
<img width="945" height="743" alt="Screenshot (216)" src="https://github.com/user-attachments/assets/04522107-9bc1-43b5-b8e5-b195e424abb6" />
- **My Action in the Lab:** 
  1. Flagged the ticket priority as **Highest** in Jira and assigned it to myself.
<img width="2150" height="1163" alt="Screenshot (215)" src="https://github.com/user-attachments/assets/b6b11745-6aa8-4e65-915b-654727386795" />
  2. Logged into my backup domain controller (**SV-02**) to test my server failover setup.
  3. Found the user account, opened its properties, went to the *Account* tab, and checked the **"Unlock account"** box.
<img width="1137" height="791" alt="Screenshot (217)" src="https://github.com/user-attachments/assets/5b19d2e1-4c54-48d7-968f-c4f648157a96" />
  4. *GPO Catch:* Realized my custom lockout rules weren't working at first because they were linked to a sub-folder. I edited the **Default Domain Policy** at the root level to force the 2-failed-attempt lockout rule system-wide.
- **The Result:** Verified the user could log back into their Windows 10 desktop perfectly and closed out the emergency ticket.
<img width="1984" height="1039" alt="Screenshot (218)" src="https://github.com/user-attachments/assets/c4acaf90-e233-4cb6-96f4-887b0f52c5ba" />
---

---

### Ticket 4: Software Installation Request (Service Request)
<img width="1826" height="770" alt="Screenshot (220)" src="https://github.com/user-attachments/assets/ab856423-3ae2-48dc-9d90-abbc78f13a41" />
- **The Ticket:** A standard user submitted a service request asking for the `7-Zip` utility to be installed on their workstation. Because of strict computer Group Policies, they were blocked by a Windows User Account Control (UAC) prompt and could not install it themselves.
<img width="918" height="692" alt="Screenshot (219)" src="https://github.com/user-attachments/assets/22d620b9-7a67-42ef-89c9-54401a484c3f" />

- **My Action in the Lab:** 
  1. Assigned the ticket to myself in Jira and moved it to `In Progress`.
<img width="2090" height="1024" alt="Screenshot (221)" src="https://github.com/user-attachments/assets/191cb248-7c32-4f5a-a991-0abb69f25ca1" />
  2. Logged into the secondary server (**SV-02**) and opened **PDQ Inventory** to verify the client machine was online and check its current software list.
  3. Switched over to **PDQ Deploy** and selected my pre-built `7-Zip Silent Deployment` custom package.
<img width="1455" height="874" alt="Screenshot (242)" src="https://github.com/user-attachments/assets/54e39e6d-dbd9-4e2c-8931-623035acb596" />
   4. Targeted the user's specific client machine (`Client1`) and triggered a remote background installation across the external virtual switch.
<img width="1334" height="821" alt="Screenshot (243)" src="https://github.com/user-attachments/assets/32ccd857-1296-48fa-a88f-084169b421b4" />
 <img width="1415" height="881" alt="Screenshot (244)" src="https://github.com/user-attachments/assets/621994ef-c79d-4dfa-ad63-0ce75bec9fc7" />
- **The Internal IT Note:** *"Approved application request. Verified target endpoint status via PDQ Inventory asset scans. Deployed 7-Zip silently over the network using a custom MSI package rollout from SV-02. Verified zero-touch background installation completed with an exit code of 0. No end-user downtime or credential exposure required."*
<img width="2142" height="1200" alt="Screenshot (246)" src="https://github.com/user-attachments/assets/58dbd81f-c722-4c1a-bd01-c5f9c0f3d2b4" />
- **The Result:** The software installed completely in the background with zero popups or UAC prompts disrupting the user. Sent a friendly public message to the customer letting them know the app was ready, and closed the ticket as `Resolved`.
  <img width="986" height="715" alt="Screenshot (245)" src="https://github.com/user-attachments/assets/20125f62-5c35-478a-9d81-8534d46efa30" />

---

##  Lessons Learned & Technical Takeaways

Building and managing this helpdesk sandbox taught me how a real-world enterprise IT environment balances security with day-to-day operations. Here are my key takeaways:

### 1. The Importance of Internal IT Documentation
- **Lesson:** End-users just want to know their problem is fixed without being confused by technical jargon. 
- **Takeaway:** I learned to structure my ticket responses into two separate channels: a simple, helpful public reply for the user, and a highly technical internal note for the IT team documenting the root cause, tools used (PDQ/GPO), and system exit codes.

### 2. Group Policy Hierarchy and Scoping Controls
- **Lesson:** Group Policies will fail or behave unexpectedly if they are linked to the wrong directory container.
- **Takeaway:** I discovered firsthand that strict account security rules (like account lockout thresholds) are completely ignored by the domain if they are linked to sub-OUs. They *must* live inside the Default Domain Policy at the root level because the domain engine handles authentication, not individual department folders.

### 3. Hardcoded IPs vs. Centralized Domain Paths (FQDN)
- **Lesson:** Relying on raw IP addresses inside corporate configurations creates massive infrastructure risks if the network layout changes.
- **Takeaway:** When I switched my lab network subnet scheme, my mapped drive GPOs instantly broke because they were pointed to an old static IP. I resolved this by re-engineering the target paths to use the server's fully qualified domain name (`\\dc01.lab.local\share`), ensuring the network remains resilient during future upgrades.

### 4. Privilege Isolation & Endpoint Hardening
- **Lesson:** Giving every user "Local Administrator" rights is a major security risk that leaves endpoints vulnerable to unauthorized software and malware execution.
- **Takeaway:** By disabling local admin rights via GPO and forcing User Account Control (UAC) blocks, I learned how to securely elevate permissions in the background. Using **PDQ Deploy** from a secondary server allowed me to patch software silently over the network, completely protecting administrative credentials from being exposed on the user's monitor.


//

---

## 🚀 Project Roadmap: Upcoming Workflow Expansions

To continue building my Tier 1/2 helpdesk skills, I am actively developing and expanding this sandbox with future additional enterprise-style ITIL workflows:

### 5. Hardware Asset Lifecycle & Procurement (Service Request)
- **The Workflow:** `Submitted` ➡️ `Manager Approval` ➡️ `Asset Tagging & Registry` ➡️ `Deployed` ➡️ `Closed`
- **The Lab Goal:** Practice using **Jira Assets** to track computer inventory. When an asset request is approved, I will manually assign a fake serial number/asset tag to a new client VM registry before deploying it to a user profile.

### 6. Security Incident & Phishing Escalation (Incident Management)
- **The Workflow:** `Reported` ➡️ `Triage` ➡️ `SecOps Escalation` ➡️ `Containment` ➡️ `Post-Mortem` ➡️ `Closed`
- **The Lab Goal:** Simulate a user reporting a suspicious email link. I will practice high-priority ticket escalation procedures in Jira, use Group Policy to immediately disable the compromised user account, and simulate host isolation protocols.

### 7. Major System Outage & Change Management (Change Control)
- **The Workflow:** `RFC Submitted` (Request for Change) ➡️ `CAB Review` (Change Advisory Board) ➡️ `Implementation` ➡️ `Rollback Testing` ➡️ `Closed`
- **The Lab Goal:** Practice the formal corporate change process for infrastructure updates. I will log a major change ticket in Jira before running a manual update or network switch overhaul on my Active Directory environment, complete with back-out/rollback plans.

### 8. Temporary Privileged Access Request (Access Management)
- **The Workflow:** `Requested` ➡️ `Security Review` ➡️ `Timed Approval` ➡️ `Revoked` ➡️ `Closed`
- **The Lab Goal:** Practice the principle of least privilege. When a user requests temporary admin rights, I will use Jira to track the request, manually add them to a restricted administrative group in Active Directory, and then manually remove them after a simulated 2-hour window to complete the ticket lifecycle.
