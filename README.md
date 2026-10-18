# Vacation_Tracking_System

VTS (Vacation Tracking System) helps employees and managers manage vacation, sick leave, and personal time off, automating policy validation and approvals to reduce manual HR processing.

## Vision

The Vacation Tracking System enables employees to manage their own vacation, sick leave, and personal time off without needing detailed knowledge of company leave policies.

It automatically validates requests against company rules and obtains manager approval when required, reducing delays and administrative work for managers and HR personnel.

HR remains responsible for maintaining employee leave data.

## Actors

| Actor | Responsibilities |
| --- | --- |
| **Employee** | Manages personal leave requests, including creating, viewing, and canceling requests. |
| **Manager** | Has all employee capabilities, approves or rejects requests from direct subordinates, and awards comp time within system limits. |
| **Clerk (HR)** | Maintains employee records, sets leave allowances and manager award limits, and can override rule-based request rejections. |
| **System Admin** | Maintains the system’s technical resources, including the web server and database, and collects, archives, and backs up system logs. |

## Use Case Diagram

![Manage Time Use Case Diagram](Phase%201/manage-time-use-case.png)

## Functional Requirements

1. Allow employees to create and view their own leave requests.
2. Allow employees to edit the title, comments, and dates of requests pending approval.
3. Allow employees to withdraw requests pending approval after confirmation.
4. Allow employees to cancel approved requests scheduled in the future or within the previous five business days.
5. Require a short explanation when canceling an approved request in the recent past.
6. Restore the leave allowance used by an approved request when it is canceled.
7. Display employees’ outstanding leave balances by category.
8. Allow employees to select a leave category with a positive balance and specify dates and hours per date.
9. Capture a short title and a description of no more than one paragraph for each request.
10. Validate leave requests against applicable company and location leave policies.
11. Allow employees to correct invalid request information or cancel submission.
12. Support manager approval when required, allowing managers to approve or reject requests from direct subordinates.
13. Require managers to provide an explanation when rejecting a request.
14. Track request states, including pending approval, approved, rejected, withdrawn, and canceled.
15. Allow managers to award comp time to direct subordinates within system-defined limits.
16. Provide access to requests from the previous calendar year and allow requests for dates up to 18 months in the future.
17. Send email notifications for approval requests, approval decisions, withdrawals, and cancellations.
18. Allow HR clerks to maintain employee records, leave allowances, and maximum manager award limits.
19. Allow HR clerks to manage locations and their associated rules.
20. Allow HR clerks to manage leave categories and their associated rules.
21. Allow authorized HR and system administration personnel to override rule-restricted actions and log all overrides.
22. Maintain activity logs for all transactions.
23. Allow system administrators to back up system logs.
24. Provide a web service interface for internal systems to query an employee’s vacation request summary.
25. Retrieve required employee information and subsequent changes from legacy HR systems.

## Non-Functional Requirements

### Usability

- Enable employees to manage their leave requests without requiring detailed knowledge of company leave policies.
- Provide a visual calendar to help employees select and compare dates.

## Constraints

- The system shall use the company’s existing hardware and middleware.
- The system shall be implemented as a web application extending the existing intranet portal.
- The system shall use the portal’s existing single sign-on (SSO) mechanism for all authentication.

## Flowchart

### Submit New Request

![Manage Time Flowchart](Phase%201/manage-time-flowchart.png)

```text
Start
→ Open VTS from intranet portal
→ Display requests and balances
  (previous 6 months / next 18 months)
→ Select leave category with a positive balance
→ Select dates and hours using calendar
→ Enter title and short description
→ Submit request
→ Valid request?
  - No → Display highlighted errors → Cancel submission?
    - Yes → End.
    - No → Correct information and resubmit.
  - Yes → Return employee to VTS home page
    → Manager approval required?
      - No → Further processing not specified in text → End.
      - Yes → Notify authorized manager by email
        → Set request to Pending Approval
        → Manager opens VTS via email link or portal
        → Authenticate if required
        → Display own requests and balances
        → Select pending employee request
        → View request details
        → Approve request?
          - Yes → Set status to Approved.
          - No → Require rejection explanation
            → Set status to Rejected.
        → Submit decision and update request
        → Email result to employee
        → Return manager to VTS home page
→ End
```

## Data Model

| Entity | Attributes |
| --- | --- |
| **Employee** | employeeId, name, email, managerId, locationId |
| **LeaveCategory** | categoryId, name |
| **LeaveBalance** | employeeId, categoryId, remainingHours |
| **LeaveRequest** | requestId, employeeId, categoryId, title, description, status, rejectionReason, cancellationExplanation |
| **LeaveRequestDay** | requestDayId, requestId, date, hours |
| **Location** | locationId, name |

### Relationships

- One Employee can submit many LeaveRequests. Each request belongs to one employee.
- One LeaveCategory can be associated with many LeaveRequests. Each request uses one category.
- One LeaveRequest contains one or more LeaveRequestDays. Each day belongs to one request.
- One Employee can have many LeaveBalances, with one balance per leave category.
- One LeaveCategory can be associated with many LeaveBalances. Each balance belongs to one employee and one category.
- One Location can have many Employees. Each employee has one primary work location.
- A Manager is an employee who supervises other employees. An employee may supervise many employees; each subordinate references their manager through managerId.

### Request Status Values

- `PENDING_APPROVAL`
- `APPROVED`
- `REJECTED`
- `WITHDRAWN`
- `CANCELED`

### Design Notes

- LeaveRequestDay stores the date and requested hours separately because a request can cover multiple days with different hours.
- Manager is represented through the employee relationship rather than a separate entity.

## Pseudocode

### Submit New Request

```text
BEGIN
    DISPLAY leave requests and balances
    SELECT leave category with a positive balance
    ENTER dates, hours per date, title, and description

    REPEAT
        SUBMIT request
        VALIDATE request against applicable policies

        IF request is invalid THEN
            DISPLAY errors
            ALLOW employee to correct information or cancel

            IF employee cancels THEN
                EXIT
            END IF
        END IF
    UNTIL request is valid

    SAVE request
    DISPLAY employee home page

    IF manager approval is required THEN
        SET status TO PENDING_APPROVAL
        SEND approval email to authorized managers

        WHEN an authorized manager reviews the request
            IF manager approves THEN
                SET status TO APPROVED
            ELSE
                REQUIRE rejection reason
                SET status TO REJECTED
            END IF

            SAVE decision
            SEND result email to employee
            DISPLAY manager home page
    ELSE
        // Further processing is not specified in the source.
    END IF
END
```

## Sequence Diagram

### Submit New Request

> **Image placeholder:** Submit New Request sequence diagram will be added here.

<!-- Add the image to images/ and uncomment the following line when ready. -->
<!-- ![Submit New Request Sequence Diagram](images/submit-new-request-sequence.png) -->
