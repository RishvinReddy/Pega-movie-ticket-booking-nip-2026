# Test Cases

## 1. Document Purpose
This document defines the functional test cases for the `NIP-MovieTicket-RishvinReddy` Movie Ticket Booking Management Application.

The test cases validate the end-to-end `Movie Ticket Request` case lifecycle and the ten user stories defined for the National Internship Program (NIP) project.

The test suite covers:
- Movie ticket request submission
- Show availability
- Seat availability validation
- Booking cost calculation
- Customer confirmation
- Booking cancellation
- Movie and Show data
- Booking detail review
- Ticket processing
- Seat allocation
- Ticket ID generation
- Booking confirmation
- Customer notification
- SLA behavior
- Show Type routing

---

## 2. Test Strategy
Testing is performed at the functional case level.

The primary approach is:
```text
Create Test Case
      |
      v
Enter Test Data
      |
      v
Execute Case Stage
      |
      v
Verify Business Rule
      |
      v
Verify Case Data
      |
      v
Verify Workflow Transition
      |
      v
Record Result
```

Where a business rule is involved, both the rule configuration and the resulting case behavior should be verified.

---

## 3. Test Environment

| Item | Value |
|---|---|
| Application | NIP-MovieTicket-RishvinReddy |
| Case Type | Movie Ticket Request |
| Platform | Pega |
| Primary Persona | Customer |
| Supporting Persona | Booking Agent |
| Test Area | Movie Ticket Booking |

---

## 4. Test Data
The following generic test data can be used for functional testing.

| Field | Example Value |
|---|---|
| Movie Name | Example Movie |
| Genre | Action |
| Show Date | Valid future show date |
| Show Time | 18:30 |
| Seat Capacity | 100 |
| Number of Tickets | 3 |
| Ticket Price | 250 |
| Available Seats Count | 20 |
| Show Type | Standard |
| Customer Confirmation | Approve |

The exact movie, show, date, and time values should be replaced with values available in the Pega application's configured test data.

---

## 5. Test Case Status Definitions

| Status | Meaning |
|---|---|
| PASS | Actual result matches expected result |
| FAIL | Actual result does not match expected result |
| BLOCKED | Test cannot be executed because of another defect/configuration issue |
| NOT RUN | Test has not yet been executed |

---

## 6. US-001 — Submit Movie Ticket Request

### TC-001 — Create Movie Ticket Request
**Requirement:** US-001
**Objective:** Verify that a Customer can submit a Movie Ticket Request with the required booking information.

**Preconditions**
- Application is accessible.
- Customer persona is available.
- Movie Ticket Request case type is configured.
- Valid Movie and Show information exists.

**Test Data**
- Movie Name = Example Movie
- Show Date = Valid future date
- Show Time = 18:30
- Number of Tickets = 3

**Steps**
1. Create a new Movie Ticket Request.
2. Enter Movie Name.
3. Enter Show Date.
4. Enter Show Time.
5. Enter Number of Tickets.
6. Submit the request.

**Expected Result**
- The case is created successfully.
- Required information is accepted.
- The case proceeds to the Availability stage.
- No validation error is displayed for valid input.

**Actual Result**
To be recorded during execution.

**Status**
NOT RUN

---

### TC-002 — Required Field Validation
**Requirement:** US-001
**Objective:** Verify that required booking information cannot be submitted when missing.

**Steps**
1. Create a new Movie Ticket Request.
2. Leave one or more required fields empty.
3. Attempt to submit the request.

**Expected Result**
The application prevents invalid submission and displays the appropriate validation message.

**Status**
NOT RUN

---

## 7. US-002 — Check Show Availability

### TC-003 — Availability Check With Sufficient Seats
**Requirement:** US-002
**Objective:** Verify that a booking with sufficient available seats can proceed.

**Test Data**
- Number of Tickets = 3
- Available Seats Count = 20

**Steps**
1. Create a valid Movie Ticket Request.
2. Proceed to Availability.
3. Capture the Available Seats Count.
4. Execute ValidateSufficientSeats.

**Expected Result**
- Available Seats Count is captured.
- Seat Availability Status is determined.
- The booking is allowed to proceed when sufficient seats are available.
- The case can continue toward Approval.

**Status**
NOT RUN

---

### TC-004 — Availability Check With Insufficient Seats
**Requirement:** US-002
**Objective:** Verify that a booking cannot proceed when the requested number of tickets exceeds available seating.

**Test Data**
- Number of Tickets = 10
- Available Seats Count = 5

**Steps**
1. Create a Movie Ticket Request.
2. Enter the requested ticket quantity.
3. Process Availability.
4. Execute ValidateSufficientSeats.

**Expected Result**
- Available Seats Count is captured.
- The system identifies insufficient seating.
- Seat Availability Status reflects the unavailable condition.
- The booking does not proceed to final ticket processing.

**Status**
NOT RUN

---

## 8. US-003 — Calculate Booking Cost

### TC-005 — Calculate Total Cost
**Requirement:** US-003
**Objective:** Verify automatic Total Cost calculation.

**Test Data**
- Ticket Price = 250
- Number of Tickets = 3

**Steps**
1. Create a Movie Ticket Request.
2. Enter Ticket Price.
3. Enter Number of Tickets.
4. Allow the calculation rule to execute.
5. Inspect Total Cost.

**Expected Result**
The application calculates:
`Total Cost = Ticket Price × Number of Tickets`

Therefore:
`Total Cost = 250 × 3 = 750`

Total Cost is automatically populated/stored in the case.

**Status**
NOT RUN

---

### TC-006 — Recalculate Total Cost When Ticket Quantity Changes
**Requirement:** US-003
**Objective:** Verify that Total Cost reflects the current number of tickets.

**Test Data**
*Initial:*
- Ticket Price = 250
- Number of Tickets = 2

*Updated:*
- Number of Tickets = 4

**Expected Result**
*Initial:*
Total Cost = 500

*After changing the quantity:*
Total Cost = 1000

**Status**
NOT RUN

---

## 9. US-004 — Confirm Booking Request

### TC-007 — Confirm Available Booking
**Requirement:** US-004
**Objective:** Verify that a Customer can confirm a booking when seats are available.

**Test Data**
- Customer Confirmation = Approve
- Seat Availability Status = Available

**Steps**
1. Reach the Approval stage.
2. Review the booking details.
3. Select Approve/Confirm.
4. Submit the confirmation.

**Expected Result**
The `ConfirmBooking` decision logic determines a successful booking outcome.

Expected status:
`Confirmed`

The case proceeds to Booking Execution.

**Status**
NOT RUN

---

### TC-008 — Cancel Booking
**Requirement:** US-004
**Objective:** Verify that the Customer can cancel a booking.

**Test Data**
- Customer Confirmation = Cancel
- Seat Availability Status = Available

**Steps**
1. Reach the Approval stage.
2. Review the booking.
3. Select Cancel.
4. Submit the decision.

**Expected Result**
The booking status becomes:
`Cancelled`

The case does not proceed to final booking execution and follows the appropriate resolution path.

**Status**
NOT RUN

---

### TC-009 — Reject Booking With Unavailable Seats
**Requirement:** US-004
**Objective:** Verify the booking decision when the customer attempts to confirm a booking that is unavailable.

**Test Data**
- Customer Confirmation = Approve
- Seat Availability Status = Unavailable

**Expected Result**
The `ConfirmBooking` Decision Table returns the configured rejection outcome.

Expected configured result:
`Rejected`

The case does not proceed as a successfully confirmed booking.

**Status**
NOT RUN

---

## 10. US-005 — Maintain Movie and Show Data

### TC-010 — Verify Movie Data Object
**Requirement:** US-005
**Objective:** Verify that Movie data can be maintained and reused.

**Required Data**
- Movie Name
- Genre

**Steps**
1. Open the Movie data object.
2. Verify Movie Name.
3. Verify Genre.
4. Use the Movie information in a booking request.

**Expected Result**
Movie information is available for use by the Movie Ticket Request case.

**Status**
NOT RUN

---

### TC-011 — Verify Show Data Object
**Requirement:** US-005
**Objective:** Verify that Show data can be maintained and reused.

**Required Data**
- Movie Name
- Show Date
- Show Time
- Seat Capacity

**Steps**
1. Open the Show data object.
2. Verify the required show information.
3. Use the Show information in a booking request.

**Expected Result**
Show information is available for the Movie Ticket Request process.

**Status**
NOT RUN

---

## 11. US-006 — Review Booking Details

### TC-012 — Review Booking Details Before Confirmation
**Requirement:** US-006
**Objective:** Verify that the Customer can review the required booking information before confirming.

**Required Information**
- Movie Name
- Show Timing
- Number of Tickets
- Total Cost

**Steps**
1. Create a valid booking request.
2. Complete availability processing.
3. Reach the Approval stage.
4. Review the displayed booking details.

**Expected Result**
The Customer can clearly review:
- Movie Name
- Show Date/Time
- Number of Tickets
- Total Cost

before confirming or cancelling the booking.

**Status**
NOT RUN

---

## 12. US-007 — Process Ticket Booking

### TC-013 — Process Confirmed Booking
**Requirement:** US-007
**Objective:** Verify that a confirmed booking proceeds to Booking Execution.

**Preconditions**
- Booking Status = Confirmed

**Steps**
1. Confirm an available booking.
2. Proceed to Booking Execution.
3. Execute the booking process.

**Expected Result**
The application processes the booking and maintains:
- Seat Numbers
- Ticket ID
- Booking Confirmation Status
- Booking Status

**Status**
NOT RUN

---

### TC-014 — Allocate Seats
**Requirement:** US-007
**Objective:** Verify that seats are allocated for a confirmed booking.

**Steps**
1. Complete a valid booking.
2. Confirm the booking.
3. Execute seat allocation.
4. Inspect Seat Numbers.

**Expected Result**
Seat Numbers are populated for the confirmed booking.
The allocated seats correspond to the requested number of tickets.

**Status**
NOT RUN

---

### TC-015 — Generate Ticket ID
**Requirement:** US-007
**Objective:** Verify that the completed booking contains a Ticket ID.

**Steps**
1. Complete a confirmed booking.
2. Execute Booking Execution.
3. Inspect Ticket ID.

**Expected Result**
A Ticket ID is generated or maintained for the completed booking.

**Status**
NOT RUN

---

### TC-016 — Maintain Booking Confirmation Status
**Requirement:** US-007
**Objective:** Verify that Booking Confirmation Status is updated after successful ticket processing.

**Steps**
1. Confirm a valid booking.
2. Complete Booking Execution.
3. Inspect Booking Confirmation Status.

**Expected Result**
The Booking Confirmation Status reflects the successful booking outcome.

**Status**
NOT RUN

---

## 13. US-008 — Notify Booking Confirmation

### TC-017 — Send Booking Confirmation
**Requirement:** US-008
**Objective:** Verify that the Customer receives a booking confirmation notification after successful completion.

**Preconditions**
- A booking has been successfully processed.

**Expected Notification Information**
- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

**Steps**
1. Complete a confirmed booking.
2. Complete Booking Execution.
3. Resolve the case.
4. Verify the configured Correspondence behavior.

**Expected Result**
A booking confirmation notification is generated for the Customer.
The notification contains the required booking information.

**Status**
NOT RUN

---

## 14. US-009 — Define Booking SLA

### TC-018 — Verify SLA Goal
**Requirement:** US-009
**Objective:** Verify that the booking case has the required Goal.

**Expected Configuration**
Goal = 1 day

**Steps**
1. Create a Movie Ticket Request.
2. Inspect the case SLA.
3. Verify the Goal value.

**Expected Result**
The SLA Goal is configured as:
`1 day`

**Status**
NOT RUN

---

### TC-019 — Verify SLA Deadline
**Requirement:** US-009
**Objective:** Verify that the booking case has the required Deadline.

**Expected Configuration**
Deadline = 2 days

**Steps**
1. Create a Movie Ticket Request.
2. Inspect the case SLA.
3. Verify the Deadline value.

**Expected Result**
The SLA Deadline is configured as:
`2 days`

**Status**
NOT RUN

---

### TC-020 — Verify SLA Goal Breach Behavior
**Requirement:** US-009
**Objective:** Verify the configured behavior when the SLA Goal is missed.

**Expected Result**
The case is flagged as approaching its deadline according to the configured SLA behavior.

**Status**
NOT RUN

---

### TC-021 — Verify SLA Deadline Breach
**Requirement:** US-009
**Objective:** Verify the configured behavior when the SLA Deadline is missed.

**Expected Result**
Case priority is automatically increased according to the configured SLA behavior.
No complex escalation path is required.

**Status**
NOT RUN

---

## 15. US-010 — Route Booking by Show Type

### TC-022 — Route Premium/Special Show
**Requirement:** US-010
**Objective:** Verify that Premium or Special Show bookings are routed to the PremiumShowQueue.

**Test Data**
- Show Type = Premium / Special Screening

**Steps**
1. Create a valid booking request.
2. Confirm the booking.
3. Reach the routing step.
4. Inspect the assigned work queue.

**Expected Result**
The case is routed to:
`PremiumShowQueue`

**Status**
NOT RUN

---

### TC-023 — Route Standard Show
**Requirement:** US-010
**Objective:** Verify that non-premium shows are routed to the StandardShowQueue.

**Test Data**
- Show Type = Standard

**Steps**
1. Create a valid booking request.
2. Confirm the booking.
3. Reach the routing step.
4. Inspect the assigned work queue.

**Expected Result**
The case is routed to:
`StandardShowQueue`

**Status**
NOT RUN

---

## 16. Numeric Range Regression Tests

These tests specifically protect against the previously encountered `NumberFormatException`.

- **The affected rule is:** `ValidateSufficientSeats`
- **The affected property is:** `Number of Tickets`
- **The property type is:** `Integer`

---

### TC-024 — Number of Tickets Lower Boundary
**Objective:** Verify that the lower boundary of the first numeric range is evaluated correctly.

**Test Data**
- Number of Tickets = 1

**Expected Result**
The Decision Table evaluates the value successfully.
No `NumberFormatException` is generated.

**Status**
NOT RUN

---

### TC-025 — Number of Tickets Upper Boundary
**Objective:** Verify that the upper boundary of the first numeric range is evaluated correctly.

**Test Data**
- Number of Tickets = 10

**Expected Result**
The value is evaluated as part of the configured first range.
No numeric parsing exception occurs.

**Status**
NOT RUN

---

### TC-026 — Number of Tickets Second Range
**Objective:** Verify the second configured numeric range.

**Test Data**
- Number of Tickets = 11

**Expected Result**
The value is evaluated correctly within the configured second range.
No numeric parsing exception occurs.

**Status**
NOT RUN

---

### TC-027 — Number of Tickets Second Range Upper Boundary
**Objective:** Verify the upper boundary of the second configured range.

**Test Data**
- Number of Tickets = 50

**Expected Result**
The value is evaluated correctly.
No numeric parsing exception occurs.

**Status**
NOT RUN

---

### TC-028 — Number of Tickets Third Range
**Objective:** Verify the third numeric range.

**Test Data**
- Number of Tickets = 51

**Expected Result**
The value is evaluated as part of the configured 51-or-more range.
No numeric parsing exception occurs.

**Status**
NOT RUN

---

## 17. Defect Regression Test

### TC-029 — Regression Test for DEF-001
**Defect:** DEF-001 — NumberFormatException for Numeric Range
**Objective:** Verify that the original `"1 to 10"` parsing defect does not recur.

**Test Data**
- Number of Tickets = 5
- Available Seats Count = 20

**Steps**
1. Create a Movie Ticket Request.
2. Enter Number of Tickets = 5.
3. Proceed to Availability.
4. Execute ValidateSufficientSeats.
5. Observe the case processing.
6. Check for runtime errors.

**Expected Result**
The Decision Table evaluates successfully.

The application must not produce:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The case continues through the Availability process.

**Status**
NOT RUN

---

## 18. ConfirmBooking Decision Table Tests

### TC-030 — Approve + Available
**Input**
- Customer Confirmation = Approve
- Seat Availability Status = Available

**Expected Output**
Confirmed

**Status**
NOT RUN

---

### TC-031 — Cancel + Available
**Input**
- Customer Confirmation = Cancel
- Seat Availability Status = Available

**Expected Output**
Cancelled

**Status**
NOT RUN

---

### TC-032 — Approve + Unavailable
**Input**
- Customer Confirmation = Approve
- Seat Availability Status = Unavailable

**Expected Output**
Rejected

**Status**
NOT RUN

---

### TC-033 — Otherwise Condition
**Input**
Any combination not explicitly covered by the configured Decision Table conditions.

**Expected Output**
Pending

The exact final output must match the saved Pega Decision Table.

**Status**
NOT RUN

---

## 19. End-to-End Test Cases

### TC-034 — Successful End-to-End Booking
**Objective:** Verify the complete successful booking lifecycle.

**Test Data**
- Movie Name = Example Movie
- Show Date = Valid future date
- Show Time = 18:30
- Number of Tickets = 3
- Ticket Price = 250
- Available Seats Count = 20
- Customer Confirmation = Approve
- Show Type = Standard

**Steps**
1. Create a Movie Ticket Request.
2. Enter movie and show information.
3. Enter Number of Tickets.
4. Submit the request.
5. Capture Available Seats Count.
6. Validate seat availability.
7. Calculate Total Cost.
8. Review booking details.
9. Approve the booking.
10. Execute Booking Execution.
11. Allocate seats.
12. Generate/maintain Ticket ID.
13. Update Booking Confirmation Status.
14. Route the case according to Show Type.
15. Generate booking confirmation.
16. Resolve the case.

**Expected Result**
The complete booking process succeeds.

Expected lifecycle:
```text
Request
   |
   v
Availability
   |
   v
Approval
   |
   v
Booking Execution
   |
   v
Booking Confirmation
   |
   v
Resolved
```

**Status**
NOT RUN

---

## 20. End-to-End Cancellation Test

### TC-035 — Customer Cancels Booking
**Objective:** Verify the complete cancellation path.

**Test Data**
- Customer Confirmation = Cancel

**Steps**
1. Create a Movie Ticket Request.
2. Complete the required request information.
3. Complete Availability.
4. Reach Approval.
5. Review booking details.
6. Select Cancel.

**Expected Result**
- Booking Status = Cancelled
- The case does not proceed to final ticket processing.
- The case follows the configured resolution path.

**Status**
NOT RUN

---

## 21. End-to-End Insufficient Seat Test

### TC-036 — Booking With Insufficient Seats
**Objective:** Verify that insufficient seating prevents successful booking.

**Test Data**
- Number of Tickets = 10
- Available Seats Count = 5

**Steps**
1. Create a Movie Ticket Request.
2. Enter the required booking information.
3. Submit the request.
4. Capture Available Seats Count.
5. Execute ValidateSufficientSeats.

**Expected Result**
- The system identifies that sufficient seats are unavailable.
- The booking does not proceed as a successfully confirmed booking.

**Status**
NOT RUN

---

## 22. Data Validation Test Cases

### TC-037 — Invalid Ticket Quantity
**Objective:** Verify validation of the Number of Tickets field.

**Test Data**
- Number of Tickets = Invalid/non-numeric value

**Expected Result**
The Integer property validation prevents invalid numeric input.

**Status**
NOT RUN

---

### TC-038 — Zero Tickets
**Objective:** Verify behavior when zero tickets are requested.

**Test Data**
- Number of Tickets = 0

**Expected Result**
The application applies the configured validation/business rule for an invalid or unsupported ticket quantity.

**Status**
NOT RUN

---

## 23. Boundary Testing Matrix

| Test Case | Number of Tickets | Purpose |
|---|---|---|
| TC-024 | 1 | First range lower boundary |
| TC-025 | 10 | First range upper boundary |
| TC-026 | 11 | Second range lower boundary |
| TC-027 | 50 | Second range upper boundary |
| TC-028 | 51 | Third range lower boundary |

Boundary testing is particularly important because the Availability logic uses numeric ranges.

---

## 24. Business Rule Test Matrix

| Rule | Test Cases |
|---|---|
| ValidateSufficientSeats | TC-003, TC-004, TC-024–TC-029 |
| CalculateTotalCost | TC-005, TC-006 |
| ConfirmBooking | TC-007–TC-009, TC-030–TC-033 |
| AvailableSeatsCount | TC-003, TC-004 |
| CaptureSeatCount | TC-003, TC-004 |
| CheckSeatStatus | TC-003, TC-004 |
| Show Type routing | TC-022, TC-023 |
| Booking SLA | TC-018–TC-021 |
| Correspondence | TC-017 |

---

## 25. User Story Coverage Matrix

| User Story | Test Cases | Coverage |
|---|---|---|
| US-001 — Submit Movie Ticket Request | TC-001, TC-002 | Request creation and validation |
| US-002 — Check Show Availability | TC-003, TC-004 | Availability and seat validation |
| US-003 — Calculate Booking Cost | TC-005, TC-006 | Automatic cost calculation |
| US-004 — Confirm Booking Request | TC-007, TC-008, TC-009 | Confirm/cancel/reject |
| US-005 — Maintain Movie and Show Data | TC-010, TC-011 | Data objects |
| US-006 — Review Booking Details | TC-012 | Booking review |
| US-007 — Process Ticket Booking | TC-013–TC-016 | Ticket processing |
| US-008 — Notify Booking Confirmation | TC-017 | Customer notification |
| US-009 — Define Booking SLA | TC-018–TC-021 | Goal/deadline |
| US-010 — Route by Show Type | TC-022, TC-023 | Queue routing |

---

## 26. Defect Coverage Matrix

| Defect | Regression Test |
|---|---|
| DEF-001 — NumberFormatException for "1 to 10" | TC-024–TC-029 |

The defect regression suite verifies both normal values and boundary values for numeric Decision Table ranges.

---

## 27. Expected End-to-End State

A successful booking should ultimately contain the required transaction information:
- Movie Name
- Show Date
- Show Time
- Number of Tickets
- Ticket Price
- Total Cost
- Available Seats Count
- Seat Availability Status
- Customer Confirmation
- Booking Status
- Show Type
- Seat Numbers
- Ticket ID
- Booking Confirmation Status

The final case should reach the appropriate resolved state.

---

## 28. Test Execution Record

The following table should be updated as the test cases are executed in Pega.

| Test ID | Result | Evidence | Notes |
|---|---|---|---|
| TC-001 | NOT RUN | — | — |
| TC-002 | NOT RUN | — | — |
| TC-003 | NOT RUN | — | — |
| TC-004 | NOT RUN | — | — |
| TC-005 | NOT RUN | — | — |
| TC-006 | NOT RUN | — | — |
| TC-007 | NOT RUN | — | — |
| TC-008 | NOT RUN | — | — |
| TC-009 | NOT RUN | — | — |
| TC-010 | NOT RUN | — | — |
| TC-011 | NOT RUN | — | — |
| TC-012 | NOT RUN | — | — |
| TC-013 | NOT RUN | — | — |
| TC-014 | NOT RUN | — | — |
| TC-015 | NOT RUN | — | — |
| TC-016 | NOT RUN | — | — |
| TC-017 | NOT RUN | — | — |
| TC-018 | NOT RUN | — | — |
| TC-019 | NOT RUN | — | — |
| TC-020 | NOT RUN | — | — |
| TC-021 | NOT RUN | — | — |
| TC-022 | NOT RUN | — | — |
| TC-023 | NOT RUN | — | — |
| TC-024 | NOT RUN | — | — |
| TC-025 | NOT RUN | — | — |
| TC-026 | NOT RUN | — | — |
| TC-027 | NOT RUN | — | — |
| TC-028 | NOT RUN | — | — |
| TC-029 | NOT RUN | — | — |
| TC-030 | NOT RUN | — | — |
| TC-031 | NOT RUN | — | — |
| TC-032 | NOT RUN | — | — |
| TC-033 | NOT RUN | — | — |
| TC-034 | NOT RUN | — | — |
| TC-035 | NOT RUN | — | — |
| TC-036 | NOT RUN | — | — |
| TC-037 | NOT RUN | — | — |
| TC-038 | NOT RUN | — | — |

---

## 29. Test Evidence Guidelines

For each important test, capture evidence from the Pega application.

Recommended evidence includes:

**Case Creation**
- Movie Ticket Request case
- Entered request details
- Submitted case

**Availability**
- Available Seats Count
- Seat Availability Status
- ValidateSufficientSeats result

**Cost Calculation**
- Ticket Price
- Number of Tickets
- Calculated Total Cost

**Approval**
- Booking details
- Customer confirmation
- Booking Status

**Booking Execution**
- Seat Numbers
- Ticket ID
- Booking Confirmation Status

**Routing**
- Show Type
- Assigned work queue

**SLA**
- Goal
- Deadline
- Priority after deadline breach

**Notification**
- Booking confirmation correspondence

**Resolution**
- Final case status

---

## 30. Test Completion Criteria

The test suite is considered complete when:
1. All mandatory user stories have been tested.
2. The successful booking flow has been tested end-to-end.
3. The cancellation flow has been tested.
4. The insufficient-seat scenario has been tested.
5. Total Cost calculation has been verified.
6. Numeric Decision Table boundaries have been tested.
7. The NumberFormatException regression test passes.
8. Customer confirmation has been tested.
9. Booking Execution has been tested.
10. Ticket ID and Seat Numbers have been verified.
11. Booking confirmation notification has been verified.
12. SLA Goal and Deadline have been verified.
13. Show Type routing has been verified.
14. Test evidence has been captured for the final submission.

---

## 31. Final Test Summary

The test suite validates the complete Movie Ticket Request lifecycle:

```text
+-----------------------+
| Create Request        |
+-----------+-----------+
            |
            v
+-----------------------+
| Check Availability    |
+-----------+-----------+
            |
            v
+-----------------------+
| Calculate Total Cost  |
+-----------+-----------+
            |
            v
+-----------------------+
| Review Booking        |
+-----------+-----------+
            |
            v
+-----------------------+
| Customer Confirmation |
+-----------+-----------+
            |
       +----+----+
       |         |
    Cancel     Confirm
       |         |
       v         v
    Resolve  Booking Execution
                  |
                  +--> Seat Allocation
                  |
                  +--> Ticket ID
                  |
                  +--> Booking Status
                  |
                  +--> Show Type Routing
                  |
                  v
            Booking Confirmation
                  |
                  v
              Notification
                  |
                  v
               Resolved
```

The test suite provides functional coverage for the NIP Movie Ticket Booking requirements and includes dedicated regression coverage for the NumberFormatException defect encountered during development.
