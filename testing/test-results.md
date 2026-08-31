# Test Results

## 1. Document Purpose
This document records the actual execution results of the functional, regression, and end-to-end tests for the `NIP-MovieTicket-RishvinReddy` Movie Ticket Booking Management Application.

The test scenarios are defined in:
`testing/test-cases.md`

The testing strategy is defined in:
`testing/test-plan.md`

Known defects are documented under:
`testing/defects/`

This document should contain only results based on actual execution or direct verification of the Pega application.

---

## 2. Application Under Test

| Item | Details |
|---|---|
| Application | NIP-MovieTicket-RishvinReddy |
| Case Type | Movie Ticket Request |
| Platform | Pega |
| Project | Movie Ticket Booking Management |
| Primary Persona | Customer |
| Supporting Persona | Booking Agent |

---

## 3. Result Status Definitions

| Status | Meaning |
|---|---|
| **PASS** | Test executed successfully and actual result matched expected result |
| **FAIL** | Test executed but actual result did not match expected result |
| **BLOCKED** | Test could not be completed because of another issue |
| **NOT RUN** | Test has not yet been executed |
| **VERIFIED** | Configuration or behavior was directly verified, but full test execution was not required |

---

## 4. Overall Test Summary
The test suite covers:

**Total Test Cases:** 38

Initial execution status:
- **PASS:** 0
- **FAIL:** 0
- **BLOCKED:** 0
- **VERIFIED:** 0
- **NOT RUN:** 38

*Update these numbers after executing the test cases in the Pega application.*

---

## 5. Test Execution Summary

| Test ID | Test Description | Result | Evidence | Notes |
|---|---|---|---|---|
| TC-001 | Create Movie Ticket Request | NOT RUN | — | — |
| TC-002 | Required Field Validation | NOT RUN | — | — |
| TC-003 | Availability With Sufficient Seats | NOT RUN | — | — |
| TC-004 | Availability With Insufficient Seats | NOT RUN | — | — |
| TC-005 | Calculate Total Cost | NOT RUN | — | — |
| TC-006 | Recalculate Total Cost | NOT RUN | — | — |
| TC-007 | Confirm Available Booking | NOT RUN | — | — |
| TC-008 | Cancel Booking | NOT RUN | — | — |
| TC-009 | Reject Booking With Unavailable Seats | NOT RUN | — | — |
| TC-010 | Verify Movie Data Object | NOT RUN | — | — |
| TC-011 | Verify Show Data Object | NOT RUN | — | — |
| TC-012 | Review Booking Details | NOT RUN | — | — |
| TC-013 | Process Confirmed Booking | NOT RUN | — | — |
| TC-014 | Allocate Seats | NOT RUN | — | — |
| TC-015 | Generate Ticket ID | NOT RUN | — | — |
| TC-016 | Maintain Booking Confirmation Status | NOT RUN | — | — |
| TC-017 | Send Booking Confirmation | NOT RUN | — | — |
| TC-018 | Verify SLA Goal | NOT RUN | — | — |
| TC-019 | Verify SLA Deadline | NOT RUN | — | — |
| TC-020 | Verify SLA Goal Breach Behavior | NOT RUN | — | — |
| TC-021 | Verify SLA Deadline Breach | NOT RUN | — | — |
| TC-022 | Route Premium/Special Show | NOT RUN | — | — |
| TC-023 | Route Standard Show | NOT RUN | — | — |
| TC-024 | Number of Tickets Lower Boundary | NOT RUN | — | — |
| TC-025 | Number of Tickets Upper Boundary | NOT RUN | — | — |
| TC-026 | Number of Tickets Second Range | NOT RUN | — | — |
| TC-027 | Number of Tickets Second Range Upper Boundary | NOT RUN | — | — |
| TC-028 | Number of Tickets Third Range | NOT RUN | — | — |
| TC-029 | DEF-001 Regression Test | NOT RUN | — | — |
| TC-030 | Approve + Available | NOT RUN | — | — |
| TC-031 | Cancel + Available | NOT RUN | — | — |
| TC-032 | Approve + Unavailable | NOT RUN | — | — |
| TC-033 | Otherwise Condition | NOT RUN | — | — |
| TC-034 | Successful End-to-End Booking | NOT RUN | — | — |
| TC-035 | Customer Cancels Booking | NOT RUN | — | — |
| TC-036 | Booking With Insufficient Seats | NOT RUN | — | — |
| TC-037 | Invalid Ticket Quantity | NOT RUN | — | — |
| TC-038 | Zero Tickets | NOT RUN | — | — |

---

## 6. User Story Test Results

### US-001 — Submit Movie Ticket Request
**Coverage:** TC-001, TC-002
**Result:** NOT RUN
**Verification Target:** The Customer should be able to create a valid Movie Ticket Request and required-field validation should prevent invalid submission.

### US-002 — Check Show Availability
**Coverage:** TC-003, TC-004
**Result:** NOT RUN
**Verification Target:** The application should correctly determine whether sufficient seats are available for the requested ticket quantity.

### US-003 — Calculate Booking Cost
**Coverage:** TC-005, TC-006
**Result:** NOT RUN
**Verification Target:** The application should calculate: `Total Cost = Ticket Price × Number of Tickets`

### US-004 — Confirm Booking Request
**Coverage:** TC-007, TC-008, TC-009, TC-030, TC-031, TC-032, TC-033
**Result:** NOT RUN
**Verification Target:** The `ConfirmBooking` Decision Table should return the configured booking outcome based on customer confirmation and seat availability.

### US-005 — Maintain Movie and Show Data
**Coverage:** TC-010, TC-011
**Result:** NOT RUN
**Verification Target:** The reusable Movie and Show data objects should contain the required information and be usable by the booking case.

### US-006 — Review Booking Details
**Coverage:** TC-012
**Result:** NOT RUN
**Verification Target:** The Customer should be able to review Movie Name, Show Date / Time, Number of Tickets, and Total Cost before confirmation.

### US-007 — Process Ticket Booking
**Coverage:** TC-013, TC-014, TC-015, TC-016
**Result:** NOT RUN
**Verification Target:** A confirmed booking should proceed through Booking Execution and maintain the required ticket and seat information.

### US-008 — Notify Booking Confirmation
**Coverage:** TC-017
**Result:** NOT RUN
**Verification Target:** A booking confirmation notification should be generated after successful booking completion.

### US-009 — Define Booking SLA
**Coverage:** TC-018, TC-019, TC-020, TC-021
**Result:** NOT RUN
**Verification Target:** The case should use Goal = 1 day, Deadline = 2 days, and apply the configured deadline behavior.

### US-010 — Route Booking Request by Show Type
**Coverage:** TC-022, TC-023
**Result:** NOT RUN
**Verification Target:** Show Type should determine the correct work queue (Premium / Special Screening -> `PremiumShowQueue`, Other Show Types -> `StandardShowQueue`).

---

## 7. Decision Table Test Results

### 7.1 ValidateSufficientSeats
**Rule:** `ValidateSufficientSeats`
**Test Coverage:** TC-003, TC-004, TC-024, TC-025, TC-026, TC-027, TC-028, TC-029
**Result:** NOT RUN

**Important Validation:**
The Decision Table must evaluate numeric ticket quantities correctly.
The following values should be tested: 1, 5, 10, 11, 25, 50, 51.
No test should produce `java.lang.NumberFormatException: For input string: "1 to 10"`.

### 7.2 ConfirmBooking
**Rule:** `ConfirmBooking`
**Test Coverage:** TC-030, TC-031, TC-032, TC-033

**Expected Decision Matrix:**
| Customer Confirmation | Seat Availability | Expected Result |
|---|---|---|
| Approve | Available | Confirmed |
| Cancel | Available | Cancelled |
| Approve | Unavailable | Rejected |
| Otherwise | Otherwise | Pending |

**Result:** NOT RUN
*The expected values above must match the final saved Decision Table configuration.*

---

## 8. Calculation Test Results
**Rule:** `CalculateTotalCost`
**Formula:** `Total Cost = Ticket Price × Number of Tickets`

**Test Case:**
- Ticket Price = 250
- Number of Tickets = 3

**Expected:** Total Cost = 750
**Result:** NOT RUN

---

## 9. Numeric Range Regression Results
The application previously encountered `java.lang.NumberFormatException: For input string: "1 to 10"`. This defect is documented as **DEF-001** (`testing/defects/number-format-exception.md`).

**Regression Matrix:**
| Test | Input | Expected | Result |
|---|---|---|---|
| TC-024 | 1 | First numeric range | NOT RUN |
| TC-025 | 10 | First numeric range | NOT RUN |
| TC-026 | 11 | Second numeric range | NOT RUN |
| TC-027 | 50 | Second numeric range | NOT RUN |
| TC-028 | 51 | Third numeric range | NOT RUN |
| TC-029 | 5 | No NumberFormatException | NOT RUN |

---

## 10. End-to-End Test Results

### TC-034 — Successful End-to-End Booking
**Scenario:**
```text
Create Request -> Availability -> Cost Calculation -> Customer Review -> Customer Approval -> Booking Execution -> Seat Allocation -> Ticket ID -> Booking Confirmation -> Notification -> Resolution
```
**Result:** NOT RUN
**Evidence:** —

---

## 11. Cancellation Test Result

### TC-035 — Customer Cancels Booking
**Scenario:**
```text
Request -> Availability -> Approval -> Customer selects Cancel -> Cancelled
```
**Expected:** Booking Status = Cancelled
**Result:** NOT RUN

---

## 12. Insufficient Seats Test Result

### TC-036 — Booking With Insufficient Seats
**Test Data:**
- Number of Tickets = 10
- Available Seats Count = 5

**Expected:** Seat Availability Status = Unavailable. The booking must not proceed as a successful confirmed booking.
**Result:** NOT RUN

---

## 13. SLA Test Results

**Required SLA Configuration:**
- Goal = 1 day
- Deadline = 2 days

**Results:**
| Test | Expected | Result |
|---|---|---|
| TC-018 | Goal = 1 day | NOT RUN |
| TC-019 | Deadline = 2 days | NOT RUN |
| TC-020 | Goal behavior occurs | NOT RUN |
| TC-021 | Deadline increases priority | NOT RUN |

---

## 14. Routing Test Results

### Premium / Special Show
**Test:** Show Type = Premium / Special Screening
**Expected:** `PremiumShowQueue`
**Result:** NOT RUN

### Standard Show
**Test:** Show Type = Standard
**Expected:** `StandardShowQueue`
**Result:** NOT RUN

---

## 15. Notification Test Result

### TC-017 — Booking Confirmation Notification
**Expected Notification Data:** Case ID, Movie Name, Show Date and Time, Number of Tickets, Seat Numbers, Total Cost
**Expected Behavior:** A booking confirmation is generated after successful booking completion.
**Result:** NOT RUN

---

## 16. Data Object Verification

### Movie
**Required information:** Movie Name, Genre
**Result:** NOT RUN

### Show
**Required information:** Movie Name, Show Date, Show Time, Seat Capacity
**Result:** NOT RUN

---

## 17. Property Verification

### Number of Tickets
**Property:** `NumberOfTickets`
**Data Type:** Integer
**Result:** VERIFIED
*The property was inspected during troubleshooting and confirmed as an Integer.*

### Available Seats Count
**Property:** `AvailableSeatsCount`
**Data Type:** Integer
**Result:** VERIFIED
*The property was inspected during troubleshooting and confirmed as an Integer.*

---

## 18. Defect Verification

### DEF-001 — NumberFormatException
**Original Error:** `java.lang.NumberFormatException: For input string: "1 to 10"`
**Affected Rule:** `ValidateSufficientSeats`
**Root Cause:** The numeric range configuration was being interpreted incorrectly.
**Resolution:** The Decision Table was configured to use numeric range/operator logic rather than treating the complete range expression as a numeric value.
**Current Regression Status:** NOT RUN
*The final regression test must be executed before marking the defect as fully verified.*

---

## 19. Known Authoring Issue
During development, a separate UI Authoring issue was encountered: `Minified React error #130`.
This occurred while configuring the Submit Request user action.
This issue is treated separately from DEF-001 because it occurred during UI Authoring rather than during Decision Table runtime processing.
**Current Status:** INVESTIGATION REQUIRED
*The issue should not be marked as resolved unless the affected configuration has been successfully tested.*

---

## 20. Defect Summary

| Defect ID | Description | Status |
|---|---|---|
| DEF-001 | NumberFormatException for "1 to 10" | Resolved / Regression Not Run |
| UI-001 | UI Authoring Minified React error #130 | Investigation Required |

---

## 21. Test Evidence Register
Evidence should be added as tests are executed.

| Evidence ID | Test | Evidence File | Status |
|---|---|---|---|
| E-001 | TC-001 | — | Pending |
| E-002 | TC-003 | — | Pending |
| E-003 | TC-005 | — | Pending |
| E-004 | TC-007 | — | Pending |
| E-005 | TC-012 | — | Pending |
| E-006 | TC-013 | — | Pending |
| E-007 | TC-017 | — | Pending |
| E-008 | TC-018 | — | Pending |
| E-009 | TC-022 | — | Pending |
| E-010 | TC-023 | — | Pending |
| E-011 | TC-029 | — | Pending |
| E-012 | TC-034 | — | Pending |

---

## 22. Recommended Evidence
For final NIP submission, capture screenshots showing:
- **Case Creation:** New Movie Ticket Request, Movie Name, Show Date, Show Time, Number of Tickets
- **Availability:** Availability stage, Available Seats Count, Seat Availability Status
- **Cost Calculation:** Ticket Price, Number of Tickets, Total Cost
- **Approval:** Booking details, Customer confirmation, Booking Status
- **Booking Execution:** Seat Numbers, Ticket ID, Booking Confirmation Status
- **Routing:** Show Type, Assigned work queue
- **SLA:** Goal, Deadline, Priority behavior
- **Notification:** Booking confirmation
- **Resolution:** Final case status

---

## 23. Test Execution Notes
When executing a test, record:
- Test ID
- Date
- Tester
- Input Data
- Expected Result
- Actual Result
- Status
- Evidence
- Defect ID
- Notes

**Example:**
- **Test ID:** TC-005
- **Input:** Ticket Price = 250, Number of Tickets = 3
- **Expected:** Total Cost = 750
- **Actual:** 750
- **Status:** PASS
- **Evidence:** E-003

---

## 24. Failure Handling
When a test fails:
1. Record the actual result.
2. Capture the error message.
3. Capture a screenshot where appropriate.
4. Identify the affected Pega component.
5. Create or update a defect record.
6. Fix the configuration.
7. Re-run the failed test.
8. Run related regression tests.
9. Update the result only after verification.

*Do not mark a failed test as PASS simply because a configuration change has been made.*

---

## 25. Test Completion Criteria
Testing is considered complete when:
- All mandatory user stories have been tested.
- The successful booking lifecycle passes.
- Cancellation passes.
- Insufficient-seat handling passes.
- Total Cost calculation passes.
- Numeric range tests pass.
- DEF-001 regression passes.
- Customer confirmation passes.
- Booking Execution passes.
- Ticket ID is verified.
- Seat Numbers are verified.
- Booking Confirmation Status is verified.
- Notification is verified.
- SLA configuration is verified.
- Show Type routing is verified.
- Required evidence has been captured.
- No unresolved blocking defect prevents final submission.

---

## 26. Final Test Summary
The final result should be summarized using:
- **Total Tests:** 38
- **Passed:** 0
- **Failed:** 0
- **Blocked:** 0
- **Verified:** 0
- **Not Run:** 38

*The final test status must be based only on tests actually executed against the Pega application.*

---

## 27. Final Sign-Off

**Functional Testing**
- Status: PENDING
- Tester: Rishvin Reddy
- Date: __________________

**Regression Testing**
- Status: PENDING
- Tester: Rishvin Reddy
- Date: __________________

**End-to-End Testing**
- Status: PENDING
- Tester: Rishvin Reddy
- Date: __________________

**Final Application Validation**
- Status: PENDING
- Tester: Rishvin Reddy
- Date: __________________

---

## 28. Final Statement
The purpose of this document is to maintain an accurate execution record for the NIP Movie Ticket Booking Management Application. Test results must reflect actual application behavior. Configuration verification, runtime testing, regression testing, and end-to-end testing should be clearly distinguished. No result should be reported as PASS without corresponding execution evidence.
