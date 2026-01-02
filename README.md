# Request to Leave Automation

![Power Automate](https://img.shields.io/badge/Power_Automate-Blue?logo=microsoft-power-automate&logoColor=white)
![SharePoint](https://img.shields.io/badge/SharePoint-Green?logo=microsoft-sharepoint&logoColor=white)
![Microsoft Forms](https://img.shields.io/badge/Microsoft_Forms-Teal?logo=microsoft-forms&logoColor=white)

An enterprise-level Power Automate solution designed to streamline employee leave requests. The system captures form submissions, dynamically routes approvals based on department, and maintains a centralized audit log in SharePoint.

---

## 📋 System Overview

This automation eliminates manual email chains by creating a structured approval workflow. It ensures that the correct manager is notified immediately upon an employee's submission.



### Key Features
- **Dynamic Routing:** Automatically identifies the department manager via a Switch logic.
- **Audit Trail:** Logs every request, including the manager's comments and final status, to SharePoint.
- **Real-time Notifications:** Uses the Power Automate Approvals connector for push and email notifications.

---

## 🛠️ Technical Architecture

### Logic Flow
1. **Trigger:** `When a new response is submitted` (Microsoft Forms).
2. **Data Extraction:** `Get response details` to pull employee name, dates, and division.
3. **Variable Initialization:** `varManagerEmail` (String) is initialized to store the target approver.
4. **Switch Logic:** Matches the **Division** field to assign the correct email.
5. **Approval:** `Start and wait for an approval` (Basic Approval).
6. **Condition:** Evaluates if the outcome is `Approve`.
7. **Database Update:** `PostItem` to SharePoint with status "Manager Approved" or "Manager Disapproved".

### Routing Table
The flow evaluates the **Division** input to set the approver:

| Division | Assigned Manager |
| :--- | :--- |
| Finance & Admin | Erwina.Sibayan@mt.com |
| Lab | Kay.Ching@mt.com |
| PI | Gie.Belen@mt.com |
| PRO | SarahLyn.Molinyawe@mt.com |
| SVC | Orlando.Encabo2@mt.com |
| Marcom | Kaye.Molase@mt.com |
| Mgmt (Default) | yuri.delacruz@mt.com |

---

## 📂 Repository Structure

The exported package contains the following critical files:
- `manifest.json`: Metadata about the flow package and dependencies.
- `definition.json`: The core logic, actions, and trigger definitions of the Power Automate flow.
- `connectionsMap.json`: Mapping of the flow's internal IDs to environment-specific connections.
- `apisMap.json`: Maps the logical service names to Power Platform API identifiers.

---

## 🚀 Deployment Instructions

1. **Importing the Flow:**
   - Go to [make.powerautomate.com](https://make.powerautomate.com).
   - Select **Import** > **Import Package (Legacy)**.
   - Upload the `.zip` file.
   
2. **Configuration:**
   - During import, select your existing connections for **Microsoft Forms**, **Approvals**, and **SharePoint**.
   - Update the **Form ID** in the trigger to match your local Microsoft Form.
   - Update the **SharePoint Site Address** and **List Name** in the `Create item` actions.

3. **Environment Variables:**
   - The flow currently references the SharePoint site: `https://mt1-my.sharepoint.com/personal/sarahlyn_molinyawe_mt_com`. Ensure you have access or update this to your production site.

---

## 📧 Contact & Support
**Project Leads:** Yuri Dela Cruz & Sarah Lyn Molinyawe  
**System Status:** Active (As of Jan 2026)


flowchart TD
    %% Trigger Section
    Start([<b>Trigger:</b> When a new response is submitted]) --> GetDetails[<b>Action:</b> Get response details]

    %% Variable Setup
    GetDetails --> InitVar[<b>Action:</b> Initialize variable: varManagerEmail]
    InitVar --> CompDiv[<b>Action:</b> Compose: Division Value]

    %% Routing Section
    CompDiv --> Switch{<b>Switch:</b> On Division Name}

    Switch -- "Finance & Admin" --> V1[Set varManagerEmail: Erwina.Sibayan@mt.com]
    Switch -- "Lab" --> V2[Set varManagerEmail: Kay.Ching@mt.com]
    Switch -- "PI" --> V3[Set varManagerEmail: Gie.Belen@mt.com]
    Switch -- "PRO" --> V4[Set varManagerEmail: SarahLyn.Molinyawe@mt.com]
    Switch -- "SVC" --> V5[Set varManagerEmail: Orlando.Encabo2@mt.com]
    Switch -- "Marcom" --> V6[Set varManagerEmail: Kaye.Molase@mt.com]
    Switch -- "Default/Mgmt" --> V7[Set varManagerEmail: yuri.delacruz@mt.com]

    %% Approval Section
    V1 & V2 & V3 & V4 & V5 & V6 & V7 --> Approve[<b>Action:</b> Start and wait for an approval]

    %% Conditional Logic
    Approve --> Cond{<b>Condition:</b> Outcome is 'Approve'}

    Cond -- YES --> SP_App[<b>Action:</b> Create SharePoint Item<br/>Status: Manager Approved]
    Cond -- NO --> SP_Rej[<b>Action:</b> Create SharePoint Item<br/>Status: Manager Disapproved]

    %% Endings
    SP_App --> End([End Flow])
    SP_Rej --> End
