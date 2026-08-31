# NIP Movie Ticket Booking — Submission

## 1. Project Information
| Field | Details |
|---|---|
| Project | Movie Ticket Booking Management |
| Application | `NIP-MovieTicket-RishvinReddy` |
| Case Type | `Movie Ticket Request` |
| Platform | Pega |
| Organization | CineWave Entertainment |
| Program | National Internship Program (NIP) |
| Developer | Rishvin Reddy |

---

## 2. Project Summary
The **Movie Ticket Booking Management Application** is a Pega-based case management application developed for CineWave Entertainment. The application manages the movie ticket booking lifecycle from initial request submission through availability verification, customer confirmation, booking execution, notification, and resolution. The solution uses Pega case management, reusable data objects, Decision Tables, Data Transforms, Declare Expressions, SLA configuration, work queues, and Correspondence to automate the booking process.

---

## 3. Business Objective
The application is designed to address the manual management of movie ticket bookings. The solution provides a structured workflow that enables:
- Movie ticket request submission
- Show availability verification
- Seat availability validation
- Automatic booking cost calculation
- Customer confirmation
- Booking execution
- Seat allocation
- Ticket identification
- Booking confirmation
- Customer notification
- SLA management
- Show Type based routing

---

## 4. Case Type
The primary case type is:
`Movie Ticket Request`

The case represents one movie ticket booking request.
The major lifecycle is:
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

---

## 5. Functional Coverage
The implementation addresses the ten NIP Movie Ticket Booking user stories.

| ID | Requirement | Implementation |
|---|---|---|
| US-001 | Submit Movie Ticket Request | Movie Ticket Request case type |
| US-002 | Check Show Availability | Availability stage and seat validation |
| US-003 | Calculate Booking Cost | CalculateTotalCost |
| US-004 | Confirm Booking Request | Approval stage and ConfirmBooking |
| US-005 | Maintain Movie and Show Data | Movie and Show data objects |
| US-006 | Review Booking Details | Customer review before confirmation |
| US-007 | Process Ticket Booking | Booking Execution |
| US-008 | Notify Booking Confirmation | Correspondence |
| US-009 | Define Booking SLA | Goal = 1 day, Deadline = 2 days |
| US-010 | Route Booking by Show Type | Premium/Standard work queues |

---

## 6. Key Application Components

**Case Type**
- Movie Ticket Request

**Data Objects**
- Movie
- Show

**Decision Tables**
- `ValidateSufficientSeats`
- `ConfirmBooking`

**Declare Expressions**
- `CalculateTotalCost`
- `AvailableSeatsCount`

**Data Transforms**
- `CaptureSeatCount`
- `CheckSeatStatus`

**Work Queues**
- `PremiumShowQueue`
- `StandardShowQueue`

**SLA**
- Goal: 1 day
- Deadline: 2 days

**Notification**
- Booking Confirmation Correspondence

---

## 7. Core Business Logic

**Total Cost**
The booking cost is calculated automatically:
`Total Cost = Ticket Price × Number of Tickets`

Example:
- Ticket Price = 250
- Number of Tickets = 3
- Total Cost = 750

**Seat Availability**
The application evaluates:
`Number of Tickets + Available Seats Count`
to determine whether sufficient seats are available.
The primary rule is: `ValidateSufficientSeats`

**Booking Confirmation**
The booking decision evaluates:
`Customer Confirmation + Seat Availability Status`
The configured outcomes include: Confirmed, Cancelled, Rejected, Pending.
The final behavior is determined by the saved `ConfirmBooking` Decision Table.

---

## 8. Show Type Routing
The application routes booking requests automatically according to Show Type.

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
The routing is intended to occur automatically without requiring manual queue selection.

---

## 9. SLA
The Movie Ticket Request case uses the required booking SLA:

| SLA Component | Configuration |
|---|---|
| Goal | 1 day |
| Deadline | 2 days |

When the deadline is missed, the configured behavior increases case priority. No complex escalation path is required.

---

## 10. Booking Confirmation
After successful booking execution, the application generates booking confirmation Correspondence. The confirmation is intended to contain:
- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

---

## 11. Data Model

**Movie**
- Movie Name
- Genre

**Show**
- Movie Name
- Show Date
- Show Time
- Seat Capacity

**Movie Ticket Request**
The booking case maintains information including:
- Movie Name
- Show Date
- Show Time
- Number of Tickets
- Ticket Price
- Total Cost
- Seat Availability Status
- Available Seats Count
- Customer Confirmation
- Booking Status
- Show Type
- Seat Numbers
- Ticket ID
- Booking Confirmation Status

*The exact property names configured in Pega are the source of truth for the final application implementation.*

---

## 12. Repository Structure

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

## 13. Documentation Guide

- **`docs/project-overview.md`**: Provides the overall project description, objectives, architecture summary, personas, features, and completion criteria.
- **`docs/requirements.md`**: Documents the functional requirements and maps the ten NIP user stories to the Pega implementation.
- **`docs/architecture.md`**: Documents the application architecture, case flow, data relationships, automation, routing, and supporting Pega components.
- **`docs/case-lifecycle.md`**: Documents the lifecycle of the Movie Ticket Request case from creation through resolution.
- **`docs/data-model.md`**: Documents the Movie, Show, and Movie Ticket Request data model.
- **`docs/business-rules.md`**: Documents the application's Decision Tables, Declare Expressions, Data Transforms, SLA, routing, and notification rules.
- **`docs/troubleshooting.md`**: Documents significant development issues and troubleshooting approaches.
- **`testing/test-plan.md`**: Defines the overall testing strategy, scope, test levels, test types, entry criteria, exit criteria, and evidence strategy.
- **`testing/test-cases.md`**: Contains the detailed functional, regression, boundary, and end-to-end test cases.
- **`testing/test-results.md`**: Records actual test execution results.
- **`testing/defects/number-format-exception.md`**: Contains the detailed defect record for the NumberFormatException encountered during Decision Table configuration.

---

## 14. Known Development Defect
During development, the application encountered:
`java.lang.NumberFormatException: For input string: "1 to 10"`

The issue occurred while evaluating numeric range logic in: `ValidateSufficientSeats`
The Number of Tickets property was verified as an Integer. The issue was traced to the handling of numeric range conditions in the Decision Table.
The range was intended to represent: `1 <= Number of Tickets <= 10`
The Decision Table configuration was corrected so that the value is evaluated as a numeric range rather than as a literal numeric value.
Detailed documentation is available at: `testing/defects/number-format-exception.md`

---

## 15. Testing
The testing strategy covers:
- Positive testing
- Negative testing
- Boundary testing
- Decision Table testing
- Calculation testing
- Workflow testing
- Routing testing
- SLA testing
- Notification testing
- Regression testing
- End-to-end testing

The primary end-to-end scenario is:
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
Customer Confirmation
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
Test cases are maintained in: `testing/test-cases.md`
Actual execution results are maintained in: `testing/test-results.md`

---

## 16. Submission Evidence
The final NIP submission should demonstrate the implementation through screenshots or other evidence of the configured Pega application.

Recommended evidence includes:
- **Case Type:** Movie Ticket Request, Case stages, Case lifecycle
- **Request Submission:** Movie Name, Show Date, Show Time, Number of Tickets
- **Availability:** Available Seats Count, Seat Availability Status, Availability decision
- **Cost Calculation:** Ticket Price, Number of Tickets, Total Cost
- **Customer Approval:** Booking details, Customer confirmation, Booking Status
- **Booking Execution:** Seat Numbers, Ticket ID, Booking Confirmation Status
- **Routing:** Show Type, PremiumShowQueue, StandardShowQueue
- **SLA:** Goal, Deadline, Priority behavior
- **Notification:** Booking confirmation Correspondence
- **Resolution:** Final case status

---

## 17. Submission Checklist
Before submitting the NIP project, verify:
- [ ] Application name is NIP-MovieTicket-RishvinReddy
- [ ] Case Type is exactly Movie Ticket Request
- [ ] All required case stages are configured
- [ ] Movie data object is configured
- [ ] Show data object is configured
- [ ] Availability logic is configured
- [ ] Total Cost calculation is configured
- [ ] Customer Approval is configured
- [ ] Booking Execution is configured
- [ ] Seat Numbers are maintained
- [ ] Ticket ID is maintained
- [ ] Booking Confirmation Status is maintained
- [ ] Correspondence is configured
- [ ] SLA Goal is 1 day
- [ ] SLA Deadline is 2 days
- [ ] Show Type routing is configured
- [ ] PremiumShowQueue is configured
- [ ] StandardShowQueue is configured
- [ ] Numeric range defect has been corrected
- [ ] Required functional tests have been executed
- [ ] Test evidence has been captured
- [ ] Submission document is completed
- [ ] Submission document is saved as .docx
- [ ] Final submission details match the Pega application exactly

---

## 18. Important Submission Information
The submission form requires the following application information:
- Full Name
- Email ID registered on NIP
- Phone Number
- College Name
- State
- Project Chosen
- Pega Instance URL
- Pega Application Name
- Case Type Name
- Operator Name
- Completed Submission Document (.docx)
- Declaration

The values entered into the submission form must match the actual Pega application configuration.

In particular:
- **Project Chosen:** Movie Ticket Booking
- **Pega Application Name:** NIP-MovieTicket-RishvinReddy
- **Case Type Name:** Movie Ticket Request

The Pega Instance URL and Operator Name should be copied from the actual Pega environment rather than inferred from documentation.

---

## 19. Final Verification
Before final submission, verify the following three identifiers directly in Pega:
- Application Name
- Case Type Name
- Operator Name

These values should not be modified in the submission form based solely on repository documentation. The exact values displayed by the Pega application are the source of truth.

---

## 20. Project Completion
The project is considered ready for submission when:
1. The Movie Ticket Request case can be created.
2. The request information can be captured.
3. Availability can be evaluated.
4. Total Cost is calculated automatically.
5. Booking details can be reviewed.
6. Customer confirmation works.
7. Cancellation works.
8. Confirmed bookings proceed to Booking Execution.
9. Seat information is maintained.
10. Ticket ID is maintained.
11. Booking Confirmation Status is maintained.
12. Booking confirmation is generated.
13. SLA configuration is present.
14. Show Type routing works.
15. Numeric range handling works without the previous NumberFormatException.
16. Required testing has been completed.
17. Required evidence has been captured.
18. The final .docx submission document is complete.
19. The submission form values match the Pega application exactly.

---

## 21. Final Project Flow

```text
                    +----------------------+
                    |  Movie Ticket        |
                    |  Request             |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |  Initial Request     |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |  Availability        |
                    |                      |
                    |  Seat Validation     |
                    |  Cost Calculation    |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |  Customer Approval   |
                    +----------+-----------+
                               |
                    +----------+----------+
                    |                     |
                 Cancel                 Approve
                    |                     |
                    v                     v
                 Resolve        +----------------------+
                                | Booking Execution    |
                                |                      |
                                | Seat Allocation      |
                                | Ticket ID            |
                                | Booking Status       |
                                +----------+-----------+
                                           |
                                           v
                                +----------------------+
                                | Show Type Routing    |
                                +----------+-----------+
                                           |
                                  +--------+--------+
                                  |                 |
                                  v                 v
                         PremiumShowQueue   StandardShowQueue
                                  |                 |
                                  +--------+--------+
                                           |
                                           v
                                +----------------------+
                                | Booking Confirmation |
                                +----------+-----------+
                                           |
                                           v
                                +----------------------+
                                | Customer Notification|
                                +----------+-----------+
                                           |
                                           v
                                      Resolved
```

---

## 22. Repository Purpose
This repository serves as the technical documentation and testing record for the NIP Movie Ticket Booking Management Application.

It provides a structured record of:
```text
Requirements + Architecture + Case Lifecycle + Data Model + Business Rules + Troubleshooting + Test Plan + Test Cases + Test Results + Defects + Submission Information
```

The repository should be kept synchronized with the actual Pega application throughout the final validation and submission process.
