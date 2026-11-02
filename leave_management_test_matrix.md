# Leave Management System: Test Matrix

**Platform:** ServiceNow PDI (dev308463)
**Scope:** Leave Management System (`x_1901607_leave_0`)
**Table:** Leave Request (`x_1901607_leave_0_leave_request`)

## Test Results

| # | Test | Input | Expected | Actual | Result | Evidence |
|---|------|-------|----------|--------|--------|----------|
| 1 | Inclusive day calculation | Start 2026-11-02, End 2026-11-04 | 3 days | 3 days | Pass | LREQ001022, 001024, 001025, 001028 |
| 2 | Calculation on update | Update existing record | Value recalculated and saved | 3 days persisted | Pass | LREQ001022 (Updates = 2) |
| 3 | End Date before Start Date | End = 2026-11-01, Start = 2026-11-02 | Save blocked with error | "End Date cannot be before Start Date." | Pass | Record not saved, value unchanged |
| 4 | Missing Start or End Date | Start set, End blank | Save blocked with error | "Start Date and End Date are required." | Pass | Record not created |
| 5 | Invalid status transition | Draft to Approved | Blocked | "Invalid status change: draft to approved is not allowed." | Pass | LREQ001022 stayed Draft |
| 6 | Valid status transition | Draft to Submitted | Allowed | Submitted saved | Pass | LREQ001029, 001031, 001032, 001034 |
| 7 | Manager approves | Approve request | Status becomes Approved | Status = Approved | Pass | LREQ001028, LREQ001032 |
| 8 | Manager rejects | Reject request | Status becomes Rejected | LREQ001029: Status = Rejected (auto). LREQ001034: Status stayed Submitted, set manually | Partial | LREQ001029, LREQ001034. See Open Issue 1 |
| 9 | Notification on submit | Submit record | Email to Manager | Email to abraham.lincoln@example.com, subject "Leave request submitted: LREQ001032" | Pass | sys_email list |
| 10 | Notification on approve and reject | Approve and reject requests | Email to Employee | Approved: email to abel.tuter@example.com, "Leave request approved: LREQ001032". Rejected: email to abel.tuter@example.com, "Leave request rejected: LREQ001034" | Pass | sys_email list |
| 11 | Duplicate approval prevention | Approve already approved record | Flow not triggered again | Only one Leave Approval Flow run for LREQ001032 | Pass | sys_flow_context list |
| 12 | Unauthorized record access | Employee views another employee's leave | Blocked | Not blocked. Read ACL is role-based only | Known limitation | See Limitation 1 |

## Open Issues

1. **Auto status update on reject (LREQ001034):** The approval was rejected and the flow completed, but the Leave Request Status stayed Submitted. It was set to Rejected manually. The reject path works for LREQ001029, so the root cause for this record is not confirmed. Next step: check the flow execution log for LREQ001034.
2. **Rejected notification verified:** The "Leave Rejected - Notify Employee" email was sent to abel.tuter@example.com for LREQ001034 (2026-10-31 07:23:08). Resolved.

## Known Limitations

1. **No row-level access control:** Any user with the employee role can read every Leave Request. Only role-based (employee, manager) read ACLs were added. Planned improvement: add row-level conditions to the existing read ACLs and test with impersonation.
2. **Leave Type is empty:** The Leave Type choice field is not filled on any record, and it is not mandatory.
3. **Test data quality:** LREQ001027 has manager Gale Nolau (extra test record). Records with blank initial status (LREQ001033) were created during testing and cannot move to Draft or Submitted.

## Summary

- Tests passed: 10 of 12 (Test 8 with open issue)
- Known limitations: 1 (Test 12), plus 2 open issues
