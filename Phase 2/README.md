# VTS — Session 2 Tasks

## 1. Requests UI
### Employee View
### Manager View

## 2. Supporting New Statuses

### HR_Pending and HR_Approval

Add `HR_Pending` and `HR_Approval` to the existing **Vacation Status** table without changing existing status IDs.

### Required Changes

Add a **Status Transition** table with:

- From Status
- Action
- To Status
- Allowed Role

The application reads transitions and allowed roles from the database instead of hardcoding them.

| From Status | Action | To Status | Allowed Role |
|---|---|---|---|
| Pending Approval | Approve | HR_Pending | Manager |
| HR_Pending | Approve | HR_Approval | HR |
| HR_Pending | Reject | Rejected | HR |

Once this configuration is supported, these stages can be added by updating the database without changing the code.

### Updated ERD

![Updated VTS ERD](VTS_Updated_ERD.png)

## 3. Pseudocode

### Requests UI

```text
BEGIN
    employee = GET current employee
    DISPLAY employee's requests and leave balances

    IF employee is a manager THEN
        DISPLAY subordinate requests pending approval
    END IF

    WHEN a request is selected
        DISPLAY request details
        DISPLAY allowed actions based on status and employee permissions
END
```

### Supporting New Statuses

```text
FUNCTION processAction(requestId, action, currentEmployee)
    request = GET request by requestId

    transition = FIND transition WHERE
        From Status = request.status
        AND Action = action
        AND Allowed Role matches currentEmployee's role

    IF transition is not found THEN
        RETURN error
    END IF

    CHECK currentEmployee has permission to act on this request

    IF permission is denied THEN
        RETURN error
    END IF

    UPDATE request.status TO transition.To Status
    SAVE request
    RETURN success
END FUNCTION
```

## 4. Request State Machine Diagram

## 5. Main Flow — Submit New Request
### Sequence Diagram

## 6. Alternate Flow — Withdraw Request
### Flowchart
### Sequence Diagram
### Database Changes

## 7. Alternate Flow — Cancel Approved Request
### Flowchart
### Sequence Diagram
### Database Changes

## 8. Alternate Flow — Edit Pending Request
### Flowchart
### Sequence Diagram
### Database Changes
