# Leave Management System (ServiceNow)

A leave request application built on the ServiceNow platform (Personal Developer Instance). Employees submit leave requests, managers approve or reject them through a Flow Designer workflow, and both parties receive email notifications.

## Features

- **Scoped application** `Leave Management System` (`x_1901607_leave_0`) with a custom Leave Request table
- **Auto-numbered requests** (LREQ00xxxx)
- **Automatic day calculation**: Number of Days is calculated from Start and End Date (inclusive), on insert and update
- **Validation**: End Date cannot be before Start Date; both dates are required
- **Status workflow**: Draft → Submitted → Approved / Rejected, enforced by a Business Rule (Draft cannot jump directly to Approved)
- **Approval flow** (Flow Designer): on submit, requests approval from the Manager; on approve or reject, updates the request status
- **Notifications**: Manager is emailed on submit; Employee is emailed on approve and on reject
- **Access control**: role-based read ACLs for `employee` and `manager` roles
- **Report**: group-by Status view of requests

## Tech Stack

- ServiceNow platform: Tables, Forms, Business Rules (server-side JavaScript), Flow Designer, Notifications, ACLs, Reports

## Test Results

12 test cases were run. 10 passed, 1 is partial, and 1 is a known limitation. Full details are in `leave_management_test_matrix.md`.

| # | Test | Result |
|---|------|--------|
| 1 | Inclusive day calculation | Pass |
| 2 | Calculation on update | Pass |
| 3 | End Date before Start Date blocked | Pass |
| 4 | Missing dates blocked | Pass |
| 5 | Invalid status transition blocked | Pass |
| 6 | Valid status transition allowed | Pass |
| 7 | Manager approves | Pass |
| 8 | Manager rejects | Partial (see open issues) |
| 9 | Notification on submit | Pass |
| 10 | Notification on approve and reject | Pass |
| 11 | Duplicate approval prevention | Pass |
| 12 | Row-level access | Known limitation |

## Open Issues and Limitations

1. **Status auto-update on one rejected request:** On LREQ001034, the approval was rejected and the flow completed, and the email was sent, but the request status stayed Submitted. Manual update was needed. The reject path worked on another request (LREQ001029). Root cause not yet identified. Next step: debug in Flow Designer.
2. **No row-level access:** Any employee can read all leave requests. Only role-based read ACLs are in place. Next step: add row-level conditions and test with impersonation.
3. **Leave Type is empty:** The Leave Type choice field is not filled and not mandatory.

## Screenshots

See the `screenshots/` folder.

## Demo Video

<!-- Add your video link here -->

## Author

<!-- Your name and contact -->
