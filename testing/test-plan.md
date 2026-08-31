# Test Plan

## 1. Document Purpose
This document defines the testing strategy, scope, approach, responsibilities, test environment, entry criteria, exit criteria, and evidence requirements for the `NIP-MovieTicket-RishvinReddy` application.

The purpose of this test plan is to verify that the Pega-based Movie Ticket Booking Management Application satisfies the functional requirements defined for the National Internship Program (NIP) project.

The detailed test scenarios are maintained separately in:
`testing/test-cases.md`

Known development defects are documented separately under:
`testing/defects/`

---

## 2. Project Under Test

| Item | Details |
|---|---|
| Application | NIP-MovieTicket-RishvinReddy |
| Case Type | Movie Ticket Request |
| Platform | Pega |
| Business Organization | CineWave Entertainment |
| Project | Movie Ticket Booking Management Application |
| Primary Persona | Customer |
| Supporting Persona | Booking Agent |

---

## 3. Testing Objectives
The primary testing objectives are to verify that:
1. A Customer can create a Movie Ticket Request.
2. Required booking information is captured correctly.
3. Show availability can be evaluated.
4. Available seat information is maintained.
5. Number of Tickets is handled as a numeric value.
6. Total Cost is calculated automatically.
7. Customers can review booking details.
8. Customers can confirm or cancel bookings.
9. Confirmed bookings proceed to Booking Execution.
10. Seats are allocated appropriately.
11. Ticket ID is maintained for completed bookings.
12. Booking Status is updated correctly.
13. Booking Confirmation Status is maintained.
14. Booking confirmation notification is generated.
15. The required SLA is configured.
16. SLA deadline behavior works as configured.
17. Show Type based routing works correctly.
18. Premium/Special bookings are routed to PremiumShowQueue.
19. Other bookings are routed to StandardShowQueue.
20. The previously identified numeric range defect does not recur.
21. The complete case lifecycle works from request creation through resolution.

---

## 4. Testing Scope

### 4.1 In Scope
Testing covers the following application functionality:

**Case Management**
- Movie Ticket Request creation
- Case stages
- User actions
- Case transitions
- Case resolution

**Request Data**
- Movie Name
- Show Date
- Show Time
- Number of Tickets
- Ticket Price
- Total Cost

**Availability**
- Available Seats Count
- Seat Availability Status
- Seat availability decision logic
- ValidateSufficientSeats

**Customer Confirmation**
- Approval stage
- Customer confirmation
- Booking cancellation
- Booking rejection
- ConfirmBooking

**Booking Execution**
- Seat allocation
- Seat Numbers
- Ticket ID
- Booking Status
- Booking Confirmation Status

**Data Objects**
- Movie
- Show

**Automation**
- Total Cost calculation
- Seat availability decision
- Booking decision
- Show Type routing
- SLA
- Correspondence

**Routing**
- PremiumShowQueue
- StandardShowQueue

**Defect Regression**
- NumberFormatException involving "1 to 10"

---

## 5. Out of Scope
The following are outside the scope of the NIP functional testing unless explicitly configured in the application:
- Real payment gateway integration
- External theatre APIs
- External ticketing system integration
- Production-scale performance testing
- Load testing
- Security penetration testing
- Third-party system integration testing
- Complex escalation workflows
- Real-world payment processing

The testing scope remains focused on the functionality required by the NIP Movie Ticket Booking Management Application.

---

## 6. Requirements Coverage
The test plan covers all ten NIP user stories.

| User Story | Requirement | Primary Test Cases |
|---|---|---|
| US-001 | Submit Movie Ticket Request | TC-001, TC-002 |
| US-002 | Check Show Availability | TC-003, TC-004 |
| US-003 | Calculate Booking Cost | TC-005, TC-006 |
| US-004 | Confirm Booking Request | TC-007, TC-008, TC-009 |
| US-005 | Maintain Movie and Show Data | TC-010, TC-011 |
| US-006 | Review Booking Details | TC-012 |
| US-007 | Process Ticket Booking | TC-013–TC-016 |
| US-008 | Notify Booking Confirmation | TC-017 |
| US-009 | Define Booking SLA | TC-018–TC-021 |
| US-010 | Route by Show Type | TC-022, TC-023 |

Additional regression and end-to-end tests are defined in `testing/test-cases.md`.

---

## 7. Test Levels

### 7.1 Configuration-Level Testing
Verify individual Pega configuration components.

Examples:
- Property data types
- Decision Tables
- Data Transforms
- Declare Expressions
- Correspondence
- SLA configuration
- Routing rules

### 7.2 Functional Testing
Verify that individual application features behave according to their requirements.

Examples:
- Availability validation
- Cost calculation
- Customer confirmation
- Seat allocation

### 7.3 Integration-of-Features Testing
Verify that multiple Pega components work together correctly.

Example:
```text
Number of Tickets
       |
       v
ValidateSufficientSeats
       |
       v
Seat Availability Status
       |
       v
Customer Approval
       |
       v
Booking Execution
```

### 7.4 End-to-End Testing
Verify the complete Movie Ticket Request lifecycle.

```text
Create Request
      |
      v
Availability
      |
      v
Cost Calculation
      |
      v
Customer Review
      |
      v
Approval
      |
      v
Booking Execution
      |
      v
Notification
      |
      v
Resolution
```

### 7.5 Regression Testing
Regression testing verifies that changes to Pega rules do not reintroduce previously fixed defects or break existing functionality.

The primary regression target is:
DEF-001 - NumberFormatException for "1 to 10"

---

## 8. Test Types

### 8.1 Positive Testing
Positive tests verify that valid inputs produce the expected behavior.

Examples:
- Valid movie request
- Sufficient seats
- Valid ticket quantity
- Customer approval
- Successful booking

### 8.2 Negative Testing
Negative tests verify that invalid or unsupported inputs are handled appropriately.

Examples:
- Missing required information
- Insufficient seats
- Invalid ticket quantity
- Customer cancellation
- Unsupported decision combinations

### 8.3 Boundary Testing
Boundary testing is important for the numeric ranges used by `ValidateSufficientSeats`.

Required boundary values include:
- `1`
- `10`
- `11`
- `50`
- `51`

The objective is to verify that values at range boundaries are evaluated correctly.

### 8.4 Decision Table Testing
The application's Decision Tables must be tested using both normal values and boundary values.

Primary Decision Tables:
- `ValidateSufficientSeats`
- `ConfirmBooking`

Each expected input combination must produce the intended result.

### 8.5 Calculation Testing
The Total Cost calculation must be verified using multiple ticket quantities and ticket prices.

Formula:
`Total Cost = Ticket Price × Number of Tickets`

Example:
- Ticket Price = 250
- Number of Tickets = 3
- Total Cost = 750

### 8.6 Workflow Testing
Workflow testing verifies that the case follows the correct stage sequence.

Expected sequence:
```text
Initial Request
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
Resolution
```

### 8.7 Routing Testing
Routing tests verify that Show Type determines the correct work queue.

Expected routing:
```text
Premium / Special Screening
            |
            v
    PremiumShowQueue

Other Show Types
            |
            v
    StandardShowQueue
```

### 8.8 SLA Testing
SLA testing verifies:
- Goal = 1 day
- Deadline = 2 days

The configured deadline behavior must increase case priority when the deadline is missed.

### 8.9 Notification Testing
Notification testing verifies that a booking confirmation is generated after successful booking completion.
The notification should contain the required booking information.

---

## 9. Test Environment
Testing should be performed in the configured Pega development environment containing:

- Application: `NIP-MovieTicket-RishvinReddy`
- Case Type: `Movie Ticket Request`

The environment must contain the current saved versions of:
- Case Type
- Data Objects
- Properties
- Decision Tables
- Data Transforms
- Declare Expressions
- SLA
- Routing configuration
- Correspondence configuration

---

## 10. Required Test Personas

**Customer**
Used to test:
- Request creation
- Request submission
- Booking review
- Booking confirmation
- Booking cancellation

**Booking Agent**
Used where the configured workflow requires operational availability verification or booking processing.

---

## 11. Test Data Strategy
Testing should use controlled data so that expected outcomes can be determined before executing the case.

**Standard Successful Booking**
- Movie Name = Example Movie
- Show Date = Valid future date
- Show Time = 18:30
- Number of Tickets = 3
- Ticket Price = 250
- Available Seats Count = 20
- Customer Confirmation = Approve
- Show Type = Standard

Expected:
- Total Cost = 750
- Seat Availability = Available
- Booking Status = Confirmed

**Insufficient Seats**
- Number of Tickets = 10
- Available Seats Count = 5

Expected:
- Seat Availability = Unavailable
- The case must not proceed as a successfully confirmed booking.

**Cancellation**
- Customer Confirmation = Cancel
- Seat Availability Status = Available

Expected:
- Booking Status = Cancelled

---

## 12. Numeric Range Test Data
The following values must be tested against the `ValidateSufficientSeats` Decision Table.

| Value | Purpose |
|---|---|
| 1 | Lower boundary |
| 5 | Normal first-range value |
| 10 | Upper boundary |
| 11 | Second-range lower boundary |
| 25 | Normal second-range value |
| 50 | Second-range upper boundary |
| 51 | Third-range lower boundary |

These tests are specifically intended to ensure that numeric values are evaluated as numbers rather than parsed from range strings.

---

## 13. Test Execution Process
Each test should follow the following process:

```text
1. Select Test Case
        |
        v
2. Prepare Test Data
        |
        v
3. Create / Open Case
        |
        v
4. Execute Required Action
        |
        v
5. Observe Actual Result
        |
        v
6. Compare With Expected Result
        |
        v
7. Capture Evidence
        |
        v
8. Mark PASS / FAIL / BLOCKED
        |
        v
9. Record Defect if Required
```

---

## 14. Test Case Documentation
Detailed test cases are maintained in:
`testing/test-cases.md`

Each test case should contain:
- Test Case ID
- Requirement
- Objective
- Preconditions
- Test Data
- Steps
- Expected Result
- Actual Result
- Status

Example:
```text
TC-005
Calculate Total Cost
Input:
Ticket Price = 250
Number of Tickets = 3
Expected:
Total Cost = 750
```

---

## 15. Defect Management
Any test failure must be recorded as a defect when the observed behavior differs from the expected behavior.

Defect records should contain:
- Defect ID
- Title
- Severity
- Priority
- Component
- Environment
- Preconditions
- Steps to Reproduce
- Expected Result
- Actual Result
- Root Cause
- Corrective Action
- Verification
- Status

Defect records are stored under:
`testing/defects/`

---

## 16. Known Defect

**DEF-001 — NumberFormatException**

**Description:**
The application previously produced:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```
while evaluating numeric range logic.

**Affected Rule:** `ValidateSufficientSeats`
**Affected Property:** `NumberOfTickets`
**Property Type:** `Integer`

**Root Cause:**
The numeric range was being interpreted incorrectly by the Decision Table configuration.

**Resolution:**
The range was configured using numeric range/operator logic rather than treating:
`1 to 10`
as a literal numeric value.

**Regression Requirement:**
The defect must be retested using normal values and range boundaries.
Detailed defect documentation:
`testing/defects/number-format-exception.md`

---

## 17. Entry Criteria
Testing may begin when:
- The Pega application is accessible.
- Movie Ticket Request exists.
- Required case stages are configured.
- Required properties exist.
- Movie and Show data objects exist.
- Relevant Decision Tables are saved.
- Test data is available.
- The application is in a testable state.
- Known blocking configuration errors have been identified.

---

## 18. Exit Criteria
Testing is considered complete when:
- All ten NIP user stories have test coverage.
- Successful booking flow has been tested.
- Cancellation flow has been tested.
- Insufficient-seat behavior has been tested.
- Total Cost calculation has been verified.
- Decision Table ranges have been tested.
- DEF-001 regression testing passes.
- Customer confirmation has been tested.
- Booking Execution has been tested.
- Seat Numbers have been verified.
- Ticket ID has been verified.
- Booking Confirmation Status has been verified.
- Notification behavior has been verified.
- SLA configuration has been verified.
- Show Type routing has been verified.
- Required evidence has been captured.
- No unresolved critical/blocking defects remain for submission.

---

## 19. Pass / Fail Criteria

**PASS**
A test is marked PASS when:
- The actual result matches the expected result.
- The case behaves according to the configured business rule.
- No unexpected error occurs.

**FAIL**
A test is marked FAIL when:
- The actual result differs from the expected result.
- A required workflow transition does not occur.
- A business rule produces an incorrect result.
- A runtime error prevents the expected behavior.

**BLOCKED**
A test is marked BLOCKED when:
- Another defect prevents execution.
- Required configuration is unavailable.
- Required test data cannot be prepared.
- The Pega environment is unavailable.

---

## 20. Evidence Strategy
The final NIP submission requires evidence demonstrating the implemented functionality.

The following evidence should be captured where applicable.

**Request Creation**
- Movie Ticket Request case
- Request details
- Submitted case

**Availability**
- Available Seats Count
- Seat Availability Status
- Decision Table result

**Cost**
- Ticket Price
- Number of Tickets
- Total Cost

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
- Priority behavior

**Notification**
- Booking confirmation correspondence

**Resolution**
- Final case state

---

## 21. Evidence Naming Convention
Evidence files should use descriptive names.

Recommended format:
- `TC-001-submit-request.png`
- `TC-003-seat-availability.png`
- `TC-005-total-cost.png`
- `TC-007-booking-confirmation.png`
- `TC-013-booking-execution.png`
- `TC-017-booking-notification.png`
- `TC-018-sla-goal.png`
- `TC-022-premium-routing.png`
- `TC-023-standard-routing.png`
- `TC-029-number-format-regression.png`

The actual file format may vary depending on the final submission requirements.

---

## 22. End-to-End Test Scenario

The primary end-to-end scenario is a successful booking.

```text
Customer
   |
   v
Create Movie Ticket Request
   |
   v
Enter Movie / Show Details
   |
   v
Enter Number of Tickets
   |
   v
Submit Request
   |
   v
Availability
   |
   v
Validate Sufficient Seats
   |
   v
Calculate Total Cost
   |
   v
Review Booking Details
   |
   v
Customer Approves
   |
   v
Booking Execution
   |
   +--> Seat Allocation
   |
   +--> Ticket ID
   |
   +--> Booking Status
   |
   +--> Booking Confirmation Status
   |
   v
Show Type Routing
   |
   v
Booking Confirmation
   |
   v
Customer Notification
   |
   v
Resolved
```

The complete scenario should execute without runtime errors.

---

## 23. Negative End-to-End Scenarios

**Cancellation**

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
Customer Cancels
   |
   v
Cancelled
   |
   v
Resolve
```

**Insufficient Seats**

```text
Request
   |
   v
Availability
   |
   v
Insufficient Seats
   |
   v
Unavailable / Rejected
   |
   v
Do Not Complete Booking
```

These scenarios verify that the application does not incorrectly process unsuccessful bookings.

---

## 24. Regression Strategy
Regression testing must be performed after changes to:
- Decision Tables
- Properties
- Data Transforms
- Declare Expressions
- Case stages
- Routing rules
- SLA
- Correspondence

At minimum, the following must be retested after relevant changes:
- TC-005 — Total Cost
- TC-007 — Confirm Booking
- TC-008 — Cancel Booking
- TC-003 — Availability
- TC-004 — Insufficient Seats
- TC-024–TC-029 — Numeric Range Tests
- TC-034 — Successful End-to-End Booking

---

## 25. Test Execution Tracking
The execution status should be maintained in:
`testing/test-cases.md`

The master execution summary can be maintained separately in:
`testing/test-results.md`

Recommended status values:
- NOT RUN
- PASS
- FAIL
- BLOCKED

No test should be marked PASS until it has been executed against the Pega application.

---

## 26. Traceability
The testing hierarchy is:

```text
NIP Requirement
      |
      v
User Story
      |
      v
Functional Requirement
      |
      v
Pega Configuration
      |
      v
Test Case
      |
      v
Test Evidence
      |
      v
Test Result
```

This provides traceability between the NIP project requirements and the actual Pega implementation.

---

## 27. Test Deliverables
The testing deliverables are:

```text
testing/
├── test-plan.md
├── test-cases.md
├── test-results.md
└── defects/
    └── number-format-exception.md
```

**Test Plan:** Defines the testing strategy and scope.
**Test Cases:** Contains detailed functional test scenarios.
**Test Results:** Contains actual execution results.
**Defect Records:** Contains detailed records of identified defects and their resolution.

---

## 28. Quality Goals
The testing process should demonstrate that the application:
- Correctly manages the Movie Ticket Request lifecycle.
- Applies business rules consistently.
- Calculates booking costs correctly.
- Handles availability correctly.
- Handles confirmation and cancellation correctly.
- Processes confirmed bookings correctly.
- Maintains required booking information.
- Routes cases correctly.
- Applies the required SLA.
- Generates booking confirmation.
- Does not reproduce the previously identified numeric range error.

---

## 29. Final Test Plan Summary
The test plan validates the complete Movie Ticket Booking Management Application against its NIP requirements.

The testing approach combines:

```text
Configuration Testing
        +
Functional Testing
        +
Decision Table Testing
        +
Boundary Testing
        +
Workflow Testing
        +
Routing Testing
        +
SLA Testing
        +
Notification Testing
        +
Regression Testing
        +
End-to-End Testing
```

The most important final validation is the successful execution of the complete booking lifecycle without runtime errors:

```text
Movie Ticket Request
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
Notification
        |
        v
Resolution
```

All test results must be based on actual execution in the Pega application. No test should be reported as passed without execution evidence.
