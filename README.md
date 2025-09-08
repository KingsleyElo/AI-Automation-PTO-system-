# Automated Employee Time-Off Management System  
*A Low-Code AI Automation Project*

## 📌 Introduction  
Tracking employee leave requests manually often leads to errors, delays, and inefficiency.  

This project demonstrates a **low-code AI automation approach** using **Airtable** and **Zapier** to streamline the entire leave management process.  

The system enables employees to:  
- Submit leave requests.
- Allow managers to approve or reject them.  
- Automatically update leave balances.  
- Receive email notifications.  

It also supports **annual leave resets** with scheduled automation.

---

## 🚀 Features  
- **Leave Requests** – Employees can submit leave requests with start and end dates.  
- **Manager Approvals** – Managers receive notifications and can approve/reject requests.  
- **Automatic Balance Updates** – Approved leave days update employee records automatically.  
- **Notifications** – Email updates are sent to both employees and managers.  
- **Annual Reset** – Leave balances reset at the start of the year.

---

## 🛠️ Tech Stack  

- **Airtable** – Serves as the database with three core tables:  
  - Employees Table
  - Managers Table
  - Leave Requests Table
  - Airtable also handles the scheduled resets  


- **Zapier** – Handles automated notifications to the manager and employee at the right stage.  

- **Email (Gmail)** – For sending approval/rejection notifications.  

---

## 📊 Airtable Base Schema  

### Employees Table  
| Field | Type | Description |
|-------|------|-------------|
| Name | Single line text | Employee’s full name |
| Employee ID | Autonumber | Unique identifier |
| Email | Email | Employee’s email address |
| Manager | Linked record (Managers) | Direct manager |
| Manager Name (from Managers) | Lookup | Auto-filled manager name |
| Manager Email (from Managers) | Lookup | Auto-filled manager email |
| Department | Single line text | Employee’s department |
| Role | Single line text | Employee’s role |
| Approved Leave Days | Number | Updated automatically on approval |
| Employee PTO Leave Balance | Number | Updated automatically on approval |
| Approved PTO Leave Days | Rollup | Pulls total approved leave days from Leave Requests |
| Employee Leave Balance | Formula | `22 - {Approved PTO Leave Days}` |

---

### Managers Table  
| Field | Type | Description |
|-------|------|-------------|
| Name | Single line text | Manager’s full name |
| Manager ID | Autonumber | Unique identifier |
| Email | Email | Manager’s email address |
| Department | Single line text | Manager’s department |
| Employees | Linked record (Employees) | List of managed employees |

---

### Leave Requests Table  
| Field | Type | Description |
|-------|------|-------------|
| Request ID | Autonumber | Unique identifier |
| Employees | Linked record (Employees) | Employee submitting the request |
| Manager (from Employees) | Lookup | Auto-filled manager name |
| Manager Email (from Employees) | Lookup | Auto-filled manager email |
| Start Date | Date | Leave start date |
| End Date | Date | Leave end date |
| Number of Days Requested | Formula | `WORKDAY_DIFF({Start Date}, {End Date})` → calculates working days requested |
| PTO Leave Balance | Formula | `22 - {Number of Days Requested}` (for reference only) |
| Status | Single select | Request status: Pending, Approved, or Rejected |
| Notes | Long text | Employee’s comments or reason for leave |
| Manager’s Comments | Long text | Manager’s feedback or reason for rejection |
| Created Time | Created time | Auto-generated timestamp of request creation |
| Name (from Employees) | Lookup | Auto-filled employee name |
| Name (from Manager) (from Employees) | Lookup | Auto-filled manager name |
| Record Link | Formula | Generates a Kanban view link for the request (used in Zapier emails) |
| Last Modified Time | Last modified | Tracks changes to request status (Zapier trigger field) |
| Employee Email | Lookup | Auto-filled employee email |
| Current Year | Formula | `YEAR({Start Date}) = YEAR(TODAY())` → checks if request is in current year |
| Approved PTO Leave Days | Lookup | Pulls approved days taken from Employees table |
| Approved Leave Balance | Lookup | Pulls leave balance from Employees table |



## 🔄 Workflow Overview  

1. **Leave Request Submission**  
   - Employees fill out an Airtable form with:  
     - Name  
     - Leave Start Date & End Date  
     - Notes (optional)  
   - Request enters the system with status = `Pending`.  

2. **Manager Notification (Zap 1)**  
   - Zapier watches new submissions.  
   - A notification email is sent to the Manager with a **Kanban view link** in Airtable.  
   - Manager reviews and updates the status to `Approved` or `Rejected`.  

3. **Approval Workflow (Zap 2 + Airtable Automation)**  
   - If status = `Approved`:  
     - Airtable Automation updates:  
       - **Approved PTO Days**.  
       - **Employee PTO Leave Balance**.  
     - Zapier sends an **approval email** to the Employee (cc: Manager).  

4. **Rejection Workflow (Zap 3)**  
   - If status = `Rejected`:  
     - No change to balances.  
     - Zapier sends a **rejection email** to the Employee.  

5. **Annual Reset (Airtable)**  
   - Scheduled at the beginning of the year.  
   - Airtable finds all Employee records.  
   - Updates:  
     - **Approved PTO Days → 0**  
     - **Employee PTO Leave Balance → 22**  

---

## 🔄 Zapier Automations  

### Zap 1: Notify Manager on New Leave Request  
- **Trigger:** Employee submits Airtable form (new record created with `Pending` status).  
- **Action:** Send email to Manager.  
  - Includes request details (ID, employee, dates, notes).  
  - Contains link to Airtable Kanban view for manager approval/rejection.  

---

### Zap 2: Employee Notified on Approval  
- **Trigger:** Record updated → Status = `Approved` (Airtable Approved view).  
- **Action:** Send email to Employee.  
  - Confirms approval, lists leave details.  
  - Shows updated PTO balance.  
  - Manager is CC’d.  

---

### Zap 3: Employee Notified on Rejection  
- **Trigger:** Record updated → Status = `Rejected` (Airtable Rejected view).  
- **Action:** Send email to Employee.  
  - Shares rejection notice + manager’s comment.  
  - Manager is CC’d.  

---

## 🙏 Acknowledgments

This project was developed as part of the **Low-Code AI Automation** assessment by **Loubby AI** — thank you for the curriculum, guidance, and practical exercises.

Thanks also to:
- **Airtable** — for the database, interfaces and automations.
- **Zapier** — for workflow automation and notifications.
- **ScreenPal** — for demo recording tools.


