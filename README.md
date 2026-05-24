# My Helpdesk Lab: Connecting Jira to Active Directory

## 📋 Project Overview
After building my multi-server Active Directory home lab, I wanted to practice how a real IT Support technician handles day-to-day work. I set up a free **Jira Service Management** instance to act as my corporate helpdesk portal. 

Instead of just clicking around randomly in my servers, I used Jira to submit real support requests, assign them to myself, and track the troubleshooting steps manually from start to finish.

---

##  Tickets Processed & Solved (Step-by-Step)

### Ticket 1: New Employee Onboarding (Service Request)
- **The Ticket:** HR submitted an onboarding request for a new hire named `John Doe` starting in the Finance department.
- **My Action in the Lab:** 
  1. Picked up the ticket in Jira and moved it to `In Progress`.
  2. Logged into my main Domain Controller (**DC-01**) and opened *Active Directory Users and Computers*.
  3. Created the user account `jdoe` inside the `Finance` organizational folder.
  4. Added him to the **SG_Finance** security group so he has the right file access.
  5. Opened the *Microsoft Entra Connect Sync* tool and forced a manual **Delta Sync** to send his account to the cloud.
- **The Result:** Verified John Doe appeared successfully inside the cloud-based **Microsoft Entra Admin Center** with an "On-premises synced" status, then closed the Jira ticket.

---

### Ticket 2: Mapped Drive Access Issue (Troubleshooting)
- **The Ticket:** A user from the IT department filed a ticket saying they got an "Access Denied" error when trying to open their team's shared network folder.
- **My Action in the Lab:** 
  1. Opened the user's properties in Active Directory and found they were completely missing from their team's security group.
  2. Manually added the user to the **SG_IT** group.
  3. *The Real Fix:* Discovered that when I moved my server IPs to a new network network scheme, my Group Policy (GPO) path was still trying to look for the old IP address! 
  4. Edited the GPO file path to use the server's clean name (`\\dc01.lab.local\design_department`) instead of a hardcoded IP address.
- **The Result:** Logged into the Windows 10 client VM as that user, ran `gpupdate /force`, and watched the **G: Drive** pop up on the desktop with full access working. Marked the ticket as `Resolved` in Jira.

---

### Ticket 3: Urgent Account Lockout (Incident Management)
- **The Ticket:** An employee intentionally typed their password wrong too many times, triggering our security policy and locking them out of their computer right before a big meeting.
- **My Action in the Lab:** 
  1. Flagged the ticket priority as **High** in Jira and assigned it to myself.
  2. Logged into my backup domain controller (**SV-02**) to test my server failover setup.
  3. Found the user account, opened its properties, went to the *Account* tab, and checked the **"Unlock account"** box.
  4. *GPO Catch:* Realized my custom lockout rules weren't working at first because they were linked to a sub-folder. I edited the **Default Domain Policy** at the root level to force the 2-failed-attempt lockout rule system-wide.
- **The Result:** Verified the user could log back into their Windows 10 desktop perfectly and closed out the emergency ticket.
