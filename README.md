# Movie Ticket Booking Management Application

A Pega Platform application developed as part of the **National Internship Program (NIP) 2026** by **Pega Academy**.

The application is designed for **CineWave Entertainment** to manage the movie ticket booking lifecycle, from customer request submission through show availability, booking confirmation, ticket processing, notification, SLA management, and show-type based routing.

---

## Project Information

| Field | Details |
|---|---|
| Program | National Internship Program (NIP) 2026 |
| Organization | Pega Academy |
| Platform | Pega Platform |
| Project | Movie Ticket Booking Management Application |
| Application | Movie Ticket Booking Management Application |
| Case Type | Movie Ticket Request |
| Business Context | CineWave Entertainment |
| Developer | Rishvin Reddy |
| Repository | `pega-movie-ticket-booking-nip-2026` |
| Status | In Development |

---

## Overview

CineWave Entertainment manages movie ticket bookings across multiple theatres and locations.

The existing booking process relies on manual communication through emails and offline systems, which can lead to:

- Delays in processing bookings
- Limited visibility into booking status
- Inefficient management of show and seating information
- Manual tracking of customer confirmations
- Delays in customer communication

This project addresses these challenges by implementing a structured booking workflow using the **Pega Platform**.

The application provides a centralized case lifecycle for handling movie ticket requests and automating important booking activities.

---

## Project Objectives

The project focuses on:

- Designing a complete case lifecycle for movie ticket booking
- Defining and implementing customer and staff interactions
- Modeling reusable movie and show data
- Configuring business logic and automation
- Calculating booking costs automatically
- Managing customer confirmation
- Processing ticket bookings and seat allocation
- Sending booking confirmation notifications
- Defining booking SLAs
- Automatically routing requests based on show type
- Providing a smooth flow from booking request to ticket confirmation

---

## Business Scenario

The application supports the following overall process:

```text
Customer
   |
   v
Submit Movie Ticket Request
   |
   v
Check Show Availability
   |
   v
Calculate Booking Cost
   |
   v
Customer Reviews Booking Details
   |
   v
Customer Confirms / Cancels
   |
   +--------------------+
   |                    |
 Cancel              Confirm
   |                    |
   v                    v
Resolve Case       Booking Execution
                        |
                        v
                 Show-Type Routing
                        |
              +---------+---------+
              |                   |
        Premium Show        Standard Show
              |                   |
              v                   v
      PremiumShowQueue     StandardShowQueue
              |                   |
              +---------+---------+
                        |
                        v
                Ticket Confirmation
                        |
                        v
                 Customer Notification
                        |
                        v
                  Case Resolution
```

---

# Case Type

## Movie Ticket Request

The **Movie Ticket Request** case type represents the complete end-to-end movie booking process.

The case captures customer, movie, show, ticket, availability, pricing, booking, and notification information throughout its lifecycle.

---

# Case Lifecycle

The application contains the following major stages:

```text
Initial Stage
      ↓
Availability
      ↓
Approval
      ↓
Booking Execution
      ↓
SLA Compliance
      ↓
Booking Notification
```

Each stage contributes to a specific part of the movie ticket booking process.

---

# User Stories

The application is built around the ten user stories defined for the National Internship Program.

## US-001 — Submit Movie Ticket Request

A customer should be able to submit a movie ticket request to initiate the booking process.

The initial stage captures the required booking information, including:

- Movie
- Show Date
- Show Time
- Number of Tickets
- Customer

The request is validated before continuing through the booking process.

---

## US-002 — Check Show Availability

The system or Booking Agent should verify whether the selected show has enough seats available.

The application maintains:

- Seat Availability Status
- Available Seats Count

The booking process should continue only when seats are available.

---

## US-003 — Calculate Booking Cost

The application automatically calculates the total booking cost.

The calculation is based on:

```text
Total Cost = Ticket Price × Number of Tickets
```

The **Total Cost** field is configured as a calculated, read-only Currency field.

Example:

```text
Ticket Price    = ₹200
Number of Tickets = 3

Total Cost      = ₹600
```

---

## US-004 — Confirm Booking Request

The customer can confirm or cancel the booking request.

The Approval stage captures the customer's decision.

Possible outcomes include:

```text
Confirm
   ↓
Continue to Ticket Processing

Cancel
   ↓
Resolve Booking Request
```

---

## US-005 — Maintain Movie and Show Data

The application uses reusable data objects for movie and show information.

### Movie

Movie-related information can include:

- Movie Name
- Genre

### Show

Show-related information can include:

- Show Date
- Show Time
- Seat Capacity
- Show Type
- Ticket Price

These reusable data objects support consistency and reuse across booking requests.

---

## US-006 — Review Booking Details

Before confirming the booking, the customer should be able to clearly review the booking information.

The confirmation interface should provide visibility into:

- Movie Name
- Show Timing
- Number of Tickets
- Total Cost

This allows the customer to make an informed confirmation decision.

---

## US-007 — Process Ticket Booking

The Booking Execution stage handles final ticket booking activities.

The system maintains booking information such as:

- Booking Confirmation Status
- Seat Numbers
- Ticket ID

The booking process finalizes seat allocation and ticket generation.

---

## US-008 — Notify Booking Confirmation

After a successful booking, the customer receives a booking confirmation notification.

The notification contains relevant booking information such as:

- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

The notification is triggered when the booking is successfully completed.

---

## US-009 — Define Booking SLA

A Service-Level Agreement is configured for the Movie Ticket Request case.

### SLA configuration

| SLA Property | Target |
|---|---:|
| Goal | 1 day |
| Deadline | 2 days |

The application is configured so that:

- Missing the goal flags the case as approaching its deadline
- Missing the deadline increases case priority
- No complex escalation path is required

---

## US-010 — Route Booking Request by Show Type

Booking requests are automatically routed based on the **Show Type**.

### Routing logic

```text
Show Type
   |
   +-----------------------------+
   |                             |
Premium / Special             Other Shows
   |                             |
   v                             v
PremiumShowQueue          StandardShowQueue
```

The routing is designed to occur automatically without manual intervention.

The routing logic can be implemented using a **When rule** or **Decision Table**.

---

# Data Model

The application contains case properties supporting the booking lifecycle.

Important properties include:

| Property | Purpose |
|---|---|
| Movie | Selected movie |
| Show | Selected show |
| Customer | Customer associated with the request |
| NumberOfTickets | Number of tickets requested |
| TicketPrice | Price of an individual ticket |
| TotalCost | Calculated total booking cost |
| AvailableSeatsCount | Number of available seats |
| SeatAvailabilityStatus | Availability result |
| BookingConfirmationStatus | Booking confirmation state |
| SeatNumbers | Allocated seats |
| TicketID | Generated ticket identifier |
| ShowType | Type of movie screening |
| SLA Goal Date | Booking SLA goal |
| SLA Deadline Date | Booking SLA deadline |
| SLA Flag Status | SLA status |

---

# Reusable Data Objects

The application uses reusable data objects for:

## Movie

Used to maintain movie-related information independently from individual booking cases.

## Show

Used to maintain show schedules, availability-related information, pricing, and show-specific details.

Using reusable data objects helps maintain consistency across multiple booking requests.

---

# Business Logic

## Total Cost Calculation

The application calculates the booking cost automatically:

```text
.TotalCost = .TicketPrice × .NumberOfTickets
```

The Total Cost field is:

- Currency
- Calculated
- Read-only

---

## Show Availability

The booking process checks:

```text
Available Seats >= Requested Tickets
```

The request should proceed when sufficient seats are available.

---

## Show-Type Routing

The booking execution process determines the destination queue using Show Type.

```text
IF Show Type = Premium / Special
    → PremiumShowQueue

ELSE
    → StandardShowQueue
```

---

# Workflow Automation

The Pega case lifecycle automates the progression of a booking request.

Major workflow capabilities include:

- Case creation
- Data capture
- Validation
- Availability checking
- Cost calculation
- Customer confirmation
- Booking execution
- Seat allocation
- Ticket generation
- Work queue routing
- Customer notification
- SLA tracking
- Case resolution

---

# Technology

## Pega Platform

The project is implemented using the Pega Platform and its low-code application development capabilities.

Key Pega concepts used in the project include:

- App Studio
- Case Management
- Case Types
- Stages
- Steps
- Data Modeling
- Data Objects
- Business Rules
- Calculated Fields
- Approvals
- Work Queues
- Correspondence
- Service-Level Agreements
- Workflow Automation

---

# Application Architecture

```text
                    ┌─────────────────────┐
                    │      Customer       │
                    └──────────┬──────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Movie Ticket Request│
                    │      Case Type      │
                    └──────────┬──────────┘
                               │
             ┌─────────────────┼─────────────────┐
             │                 │                 │
             v                 v                 v
        Movie Data        Show Data         Customer Data
             │                 │                 │
             └─────────────────┼─────────────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Show Availability   │
                    └──────────┬──────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Booking Cost        │
                    │ Calculation         │
                    └──────────┬──────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Customer Approval   │
                    └──────────┬──────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Booking Execution   │
                    └──────────┬──────────┘
                               │
                         Show Type
                               │
                 ┌─────────────┴─────────────┐
                 │                           │
                 v                           v
        PremiumShowQueue            StandardShowQueue
                 │                           │
                 └─────────────┬─────────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Booking Confirmation│
                    └──────────┬──────────┘
                               │
                               v
                    ┌─────────────────────┐
                    │ Customer Notification│
                    └─────────────────────┘
```

---

# Project Screenshots

Screenshots demonstrating the implementation of each user story will be added as the project progresses.

```text
docs/
└── user-stories/
    ├── US-001.png
    ├── US-002.png
    ├── US-003.png
    ├── US-004.png
    ├── US-005.png
    ├── US-006.png
    ├── US-007.png
    ├── US-008.png
    ├── US-009.png
    └── US-010.png
```

Each screenshot will correspond to the relevant implemented user story.

---

# Testing

The application will be tested through a complete end-to-end booking scenario.

The test process will verify:

- Request creation
- Required-field validation
- Movie and show selection
- Number of tickets
- Show availability
- Ticket price
- Total cost calculation
- Customer confirmation
- Booking execution
- Seat allocation
- Ticket ID generation
- Show-type routing
- SLA behavior
- Booking notification
- Final case resolution

---

# End-to-End Test Scenario

Example test scenario:

```text
Customer
   ↓
Select Movie
   ↓
Select Show
   ↓
Enter Number of Tickets
   ↓
Submit Request
   ↓
Check Availability
   ↓
Calculate Total Cost
   ↓
Review Booking
   ↓
Confirm Booking
   ↓
Route to Correct Work Queue
   ↓
Allocate Seats
   ↓
Generate Ticket ID
   ↓
Send Confirmation
   ↓
Resolve Case
```

The final test case will be run through the complete lifecycle before project submission.

---

# Project Status

The project is currently under development.

### Completed / Configured

- [x] Pega application created
- [x] Movie Ticket Request case type created
- [x] Movie and Show data references established
- [x] Number of Tickets configured as Integer
- [x] Ticket Price configured as Currency
- [x] Total Cost configured as calculated Currency field
- [x] Total Cost calculation configured
- [x] Initial test case created

### In Progress

- [ ] Complete US-001
- [ ] Complete US-002
- [ ] Complete US-003 end-to-end validation
- [ ] Complete US-004
- [ ] Complete US-005 validation
- [ ] Complete US-006
- [ ] Complete US-007
- [ ] Complete US-008
- [ ] Complete US-009
- [ ] Complete US-010
- [ ] Run complete end-to-end test
- [ ] Capture final user-story screenshots
- [ ] Complete NIP submission document

---

# Submission Evidence

The final project evidence will include:

1. Pega application implementation
2. End-to-end working case
3. One screenshot for each user story
4. Completed NIP submission document
5. Project documentation in this repository

The repository will be updated as implementation and testing are completed.

---

# Project Structure

The repository will contain project documentation and evidence.

```text
pega-movie-ticket-booking-nip-2026/
│
├── README.md
│
├── docs/
│   ├── user-stories/
│   │   ├── US-001.png
│   │   ├── US-002.png
│   │   ├── US-003.png
│   │   ├── US-004.png
│   │   ├── US-005.png
│   │   ├── US-006.png
│   │   ├── US-007.png
│   │   ├── US-008.png
│   │   ├── US-009.png
│   │   └── US-010.png
│   │
│   └── submission/
│       └── NIP-Submission-Document.docx
│
├── screenshots/
│   ├── application.png
│   ├── workflow.png
│   └── final-case.png
│
└── blueprint/
    └── blueprint-file
```

---

# Learning Outcomes

Through this project, the following Pega and application development concepts are being practiced:

- Low-code application development
- Case lifecycle design
- Workflow automation
- Data modeling
- Reusable data objects
- Business rules
- Calculated fields
- Customer interaction design
- Approval workflows
- Work queue routing
- SLA configuration
- Correspondence and notifications
- End-to-end case testing

---

# Future Improvements

The application is being developed according to the NIP requirements first.

Potential improvements beyond the required scope may include:

- Enhanced seat selection
- Theatre management
- Multiple payment options
- Advanced reporting
- Booking history
- Cancellation workflows
- Enhanced customer notifications
- Dashboard improvements
- Additional automation

These enhancements are secondary to completing and validating the required NIP user stories.

---

# Acknowledgment

This project is developed as part of the:

**Pega National Internship Program 2026**

The project provides practical experience with Pega Platform, low-code application development, case management, workflow automation, data modeling, and business process automation.

---

# Author

**Rishvin Reddy**

National Internship Program 2026  
Pega Platform

---

## Repository

**GitHub:**  
https://github.com/RishvinReddy/Pega-movie-ticket-booking-nip-2026
