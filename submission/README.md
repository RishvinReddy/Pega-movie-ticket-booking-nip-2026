# NIP Movie Ticket Booking Management
## National Internship Program — Pega Project Submission
### Project Information
| Field | Details |
|---|---|
| Project | Movie Ticket Booking Management |
| Application Name | `NIP-MovieTicket-RishvinReddy` |
| Case Type | `Movie Ticket Request` |
| Platform | Pega |
| Developer | Rishvin Reddy |
| Organization | CineWave Entertainment |
---
## 1. Project Overview
The **Movie Ticket Booking Management Application** is a Pega-based case management solution developed as part of the National Internship Program (NIP).
The application manages the movie ticket booking process from request submission through availability validation, customer confirmation, booking execution, notification, and case resolution.
The solution uses Pega case management capabilities, data objects, Decision Tables, Data Transforms, Declare Expressions, SLA configuration, work queues, and Correspondence.
---
## 2. Business Objective
The application provides a structured workflow for managing movie ticket booking requests.
The solution supports:
- Movie ticket request submission
- Movie and Show information
- Show availability checking
- Seat availability validation
- Automatic booking cost calculation
- Booking summary and review
- Customer confirmation
- Booking cancellation
- Seat allocation
- Ticket ID management
- Booking status management
- Booking confirmation
- Customer notification
- SLA management
- Show Type based routing
---
## 3. Application
### Pega Application Name
```text
NIP-MovieTicket-RishvinReddy
```
### Case Type
```text
Movie Ticket Request
```
The Movie Ticket Request case represents an individual movie ticket booking request.

---

## 4. Case Lifecycle
The primary booking lifecycle is:

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
Customer Notification
      |
      v
Resolution
```

**Initial Request**
The customer provides the required movie and booking information.

**Availability**
The application checks and captures available seat information and validates whether sufficient seats are available.

**Approval**
The customer reviews the booking summary and confirms or cancels the request.

**Booking Execution**
Confirmed bookings proceed through seat allocation and ticket processing.

**Customer Notification**
Booking details are sent to the customer through the configured Correspondence flow.

**Resolution**
The Movie Ticket Request reaches the appropriate final case state.

---

## 5. Implemented Requirements
The project covers the ten NIP Movie Ticket Booking user stories.

| ID | Requirement | Status |
|---|---|---|
| US-001 | Submit Movie Ticket Request | Configured |
| US-002 | Check Show Availability | Configured |
| US-003 | Calculate Booking Cost | Configured |
| US-004 | Confirm Booking Request | Configured |
| US-005 | Maintain Movie and Show Data | Configured |
| US-006 | Review Booking Details | Configured |
| US-007 | Process Ticket Booking | Configured |
| US-008 | Notify Booking Confirmation | Configured |
| US-009 | Define Booking SLA | Configured |
| US-010 | Route Booking by Show Type | Configured |

---

## 6. Key Pega Components

**Data Objects**
- Movie
- Show

**Decision Tables**
- ValidateSufficientSeats
- ConfirmBooking

**Data Transforms**
- CaptureSeatCount
- CheckSeatStatus
- PrepareBookingSummary

**Calculations**
- CalculateTotalCost

**Work Queues**
- PremiumShowQueue
- StandardShowQueue

**SLA**
- Goal: 1 day
- Deadline: 2 days

**Notification**
```text
Booking Notification
    |
    v
Customer Notification
    |
    v
Send Booking Details
```

---

## 7. Data Model

**Movie**
The Movie data object maintains movie information including:
- Movie Name
- Genre

**Show**
The Show data object maintains show information including:
- Movie
- Show Date
- Show Time
- Seat Capacity

**Movie Ticket Request**
The booking case maintains information including:
- Movie Data Reference
- Show Data Reference
- Number of Tickets
- Ticket Price
- Total Cost
- Available Seats Count
- Seat Availability Status
- Booking Confirmation Status
- Booking Status
- Seat Numbers
- Ticket ID
- Show Type

---

## 8. Business Rules

**Total Cost**
The booking cost is calculated using:
`Total Cost = Ticket Price × Number of Tickets`

Example:
Ticket Price = 250
Number of Tickets = 3
Total Cost = 750

**Seat Availability**
The application evaluates the requested ticket quantity against available seating.
The primary decision rule is:
`ValidateSufficientSeats`

**Booking Confirmation**
The `ConfirmBooking` Decision Table evaluates customer confirmation and seat availability.
Configured outcomes include:
- Confirmed
- Cancelled
- Rejected
- Pending

---

## 9. Show Type Routing
The application routes cases based on Show Type.

**Premium**
```text
ShowType = Premium
        |
        v
PremiumShowQueue
```

**Special Screening**
```text
ShowType = Special Screening
        |
        v
PremiumShowQueue
```

**Standard**
```text
ShowType = Standard
        |
        v
StandardShowQueue
```

**Other Show Types**
```text
Otherwise
        |
        v
StandardShowQueue
```

---

## 10. SLA
The Movie Ticket Request uses the configured SLA:

| SLA | Configuration |
|---|---|
| Goal | 1 day |
| Deadline | 2 days |

The configured SLA behavior flags the case when the goal is approaching and automatically increases case priority when the deadline is missed.

---

## 11. Customer Notification
After successful booking completion, the application uses the configured customer notification flow:

```text
Booking Notification
        |
        v
Customer Notification
        |
        v
Send Booking Details
```

The correspondence dynamically includes booking information such as:
* Case ID
* Movie Name
* Show Date and Time
* Number of Tickets
* Seat Numbers
* Total Cost

---

## 12. Testing
Functional verification has been completed for the implemented Movie Ticket Request functionality.

**Test Summary**

| Metric | Result |
|---|---|
| Test Cases | 33 |
| Configured / Verified | 33 |
| Failed | 0 |
| Blocked | 0 |
| Overall Result | PASS |

Testing covers:
* Data objects
* Case lifecycle
* Request submission
* Availability
* Seat validation
* Booking summary
* Confirmation
* Cancellation
* Booking execution
* Seat allocation
* Ticket management
* Queue routing
* SLA
* Customer notification
* Correspondence
* Case completion
* Regression handling

Detailed results are maintained in:
`testing/test-results.md`

Detailed test scenarios are maintained in:
`testing/test-cases.md`

Testing strategy is documented in:
`testing/test-plan.md`

---

## 13. Defect Resolution
During development, a numeric range configuration issue produced:
`java.lang.NumberFormatException: For input string: "1 to 10"`

The issue was associated with the numeric range handling in:
`ValidateSufficientSeats`

The Number of Tickets property was verified as an Integer.
The Decision Table configuration was corrected so that numeric ranges are evaluated using the appropriate numeric range/operator logic.

The defect is documented in:
`testing/defects/number-format-exception.md`

**Regression Status:** PASS — Configuration Corrected

---

## 14. Repository Structure

```text
NIP-MovieTicket-RishvinReddy/
│
├── README.md
│
├── docs/
│   ├── project-overview.md
│   ├── requirements.md
│   ├── architecture.md
│   ├── case-lifecycle.md
│   ├── data-model.md
│   ├── business-rules.md
│   └── troubleshooting.md
│
├── testing/
│   ├── test-plan.md
│   ├── test-cases.md
│   ├── test-results.md
│   │
│   └── defects/
│       └── number-format-exception.md
│
└── submission/
    └── README.md
```

---

## 15. Documentation

**Project Documentation**

| Document | Purpose |
|---|---|
| `docs/project-overview.md` | Project purpose, scope and overview |
| `docs/requirements.md` | Functional requirements and user stories |
| `docs/architecture.md` | Application architecture |
| `docs/case-lifecycle.md` | Movie Ticket Request lifecycle |
| `docs/data-model.md` | Data objects and case data |
| `docs/business-rules.md` | Business rules and automation |
| `docs/troubleshooting.md` | Development troubleshooting |

**Testing Documentation**

| Document | Purpose |
|---|---|
| `testing/test-plan.md` | Testing strategy and scope |
| `testing/test-cases.md` | Detailed test scenarios |
| `testing/test-results.md` | Completed test verification |
| `testing/defects/number-format-exception.md` | Detailed defect record |

---

## 16. Submission Checklist
Before submitting the NIP project, verify:

- [ ] Movie data object configured
- [ ] Show data object configured
- [ ] Movie reference available in the case
- [ ] Show reference available in the case
- [ ] Movie Ticket Request case configured
- [ ] Request submission configured
- [ ] Seat availability configured
- [ ] Seat count capture configured
- [ ] Sufficient seat validation configured
- [ ] Unavailable-seat path configured
- [ ] Booking summary configured
- [ ] Booking confirmation status configured
- [ ] Confirm Booking configured
- [ ] Seat allocation configured
- [ ] Booking status configured
- [ ] Seat Numbers maintained
- [ ] Ticket ID maintained
- [ ] Premium routing configured
- [ ] Special Screening routing configured
- [ ] Standard routing configured
- [ ] SLA Goal configured
- [ ] SLA Deadline configured
- [ ] Goal-missed behavior configured
- [ ] Deadline-missed behavior configured
- [ ] Customer notification configured
- [ ] Correspondence configured
- [ ] Dynamic booking information configured
- [ ] Case completion configured
- [ ] Numeric range defect addressed
- [ ] Functional verification completed

---

## 17. Final Application Status

```text
==================================================
       NIP MOVIE TICKET BOOKING MANAGEMENT
==================================================
Application: NIP-MovieTicket-RishvinReddy
Case Type: Movie Ticket Request
Project: Movie Ticket Booking Management
Platform: Pega
Functional Verification: PASS
Test Cases: 33
Configured / Verified: 33
Failed: 0
Blocked: 0
Overall Status: READY FOR SUBMISSION
==================================================
```

---

## 18. Submission Information
The NIP submission form requires the following information:

* Full Name
* Email ID registered on NIP
* Phone Number
* College Name
* State
* Project Chosen
* Pega Instance URL
* Pega Application Name
* Case Type Name
* Operator Name
* Completed Submission Document (.docx)
* Declaration

The following application identifiers should match the Pega environment exactly:

**Project Chosen:** Movie Ticket Booking
**Pega Application Name:** NIP-MovieTicket-RishvinReddy
**Case Type Name:** Movie Ticket Request

The Pega Instance URL and Operator Name must be taken directly from the configured Pega environment.

---

## 19. Final Verification
Before submitting the NIP form, verify:

1. The application name matches the Pega application.
2. The case type name matches the Pega case type exactly.
3. The operator name matches the operator created in Pega.
4. The NIP-registered email address is used.
5. The completed submission document is saved as `.docx`.
6. The submission document contains the required project evidence.
7. The submitted project details match the actual application.

---

## 20. Final Project Statement

The `NIP-MovieTicket-RishvinReddy` application provides an end-to-end Pega workflow for managing movie ticket booking requests.

The implemented solution covers:

```text
Request Submission
        +
Availability Validation
        +
Cost Calculation
        +
Booking Review
        +
Customer Confirmation
        +
Booking Execution
        +
Seat Allocation
        +
Ticket Management
        +
Show Type Routing
        +
SLA Management
        +
Customer Notification
        +
Case Resolution
```

The core Movie Ticket Request functionality has been configured and verified and the project is prepared for final NIP submission.
