# Project Overview

## 1. Project Information
**Project Name:** Movie Ticket Booking Management Application  
**Pega Application Name:** `NIP-MovieTicket-RishvinReddy`  
**Case Type:** `Movie Ticket Request`  
**Platform:** Pega  
**Organization:** CineWave Entertainment  
**Program:** National Internship Program (NIP)  
**Developer:** Rishvin Reddy  

---

## 2. Project Description
The **Movie Ticket Booking Management Application** is a Pega-based case management application designed to manage movie ticket booking requests from submission through completion.

The application provides a structured workflow for customers to submit movie ticket requests, verify show and seat availability, calculate the booking cost, review booking details, confirm or cancel the booking, process the ticket, and receive booking confirmation.

The application also uses Pega business rules and automation to support availability validation, cost calculation, booking decisions, SLA management, notification, and routing based on Show Type.

---

## 3. Business Context
The application is designed for **CineWave Entertainment**, a movie and entertainment business that needs to manage ticket booking requests efficiently.

The booking process needs to ensure that:
- Customer requests contain the required movie and show information.
- Requested ticket quantities are validated.
- Seat availability is checked before booking.
- Total Cost is calculated automatically.
- Customers can review and confirm their booking.
- Confirmed bookings proceed to ticket processing.
- Cancelled bookings do not proceed to further booking activity.
- Seats and ticket information are maintained after confirmation.
- Customers receive a booking confirmation notification.
- Booking requests are processed within the defined SLA.
- Requests are routed according to Show Type.

---

## 4. Project Objectives
The primary objectives of the application are:
1. Create a complete Movie Ticket Request case lifecycle.
2. Capture movie and show information.
3. Validate ticket requests and seat availability.
4. Automatically calculate Total Cost.
5. Provide a Customer approval and confirmation step.
6. Process confirmed bookings.
7. Allocate and maintain seat information.
8. Generate and maintain Ticket ID.
9. Notify the Customer after successful booking.
10. Apply the required booking SLA.
11. Automatically route bookings according to Show Type.
12. Use reusable Movie and Show data objects.
13. Implement business logic using Pega rules and automation.

---

## 5. Core Case Type
The primary case type is:

```text
Movie Ticket Request
```

Each case represents one movie ticket booking request.

The case maintains the information required throughout the booking lifecycle.

**Main case information**

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

The exact property names used in the Pega application should remain synchronized with the final Pega configuration.

---

## 6. Case Lifecycle

The overall case lifecycle is:

```text
Movie Ticket Request
        |
        v
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
Booking Confirmation
        |
        v
Resolved
```

**Initial Request**
The customer provides:
- Movie Name
- Show Date
- Show Time
- Number of Tickets

**Availability**
The application checks the requested number of tickets against available seats.

The configured flow includes:

```text
Submit Request
      |
      v
Capture Seat Count
      |
      v
Validate Sufficient Seats
      |
      v
Approval
```

**Approval**
The customer reviews the booking information and chooses whether to confirm or cancel the request.

**Booking Execution**
Confirmed bookings proceed to ticket processing, including seat allocation, Ticket ID, Booking Status, and Booking Confirmation Status.

**Resolution**
After successful processing, the customer receives the booking confirmation and the case is resolved.

---

## 7. Main Features

### 7.1 Movie Ticket Request
Customers can submit a request by providing movie and show details together with the required number of tickets.

### 7.2 Show Availability
The application captures:
- Seat Availability Status
- Available Seats Count

and uses these values to determine whether the requested booking can proceed.

### 7.3 Automatic Cost Calculation
The application calculates Total Cost automatically using:

`Total Cost = Ticket Price × Number of Tickets`

This removes the need for the customer to manually calculate the booking amount.

### 7.4 Customer Confirmation
The customer reviews the booking details before confirming or cancelling the request.

### 7.5 Booking Execution
Confirmed bookings proceed to ticket processing, where seat information and ticket information are maintained.

### 7.6 Booking Notification
After successful completion, a booking confirmation notification is sent to the Customer.

### 7.7 SLA Management
The application uses a booking SLA with:
- **Goal:** 1 day
- **Deadline:** 2 days

If the deadline is missed, the case priority is automatically increased.

### 7.8 Show Type Routing
Bookings are automatically routed according to Show Type.

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

---

## 8. Data Model

The application uses reusable data objects for movie and show information.

**Movie**
The Movie data object contains:
- Movie Name
- Genre

**Show**
The Show data object contains:
- Movie Name
- Show Date
- Show Time
- Seat Capacity

These reusable objects provide consistent movie and show information that can be used by booking cases.

---

## 9. Business Rules

The application uses Pega rules to implement its business logic.

**ValidateSufficientSeats**
- **Type:** Decision Table
- Determines the appropriate seat availability outcome based on ticket quantity and available seats.

**ConfirmBooking**
- **Type:** Decision Table
- Determines the booking outcome based on:
  - Customer Confirmation
  - Seat Availability Status

Possible configured outcomes include:
- Confirmed
- Cancelled
- Rejected
- Pending

**CalculateTotalCost**
- **Type:** Declare Expression
- Calculates: `Ticket Price × Number of Tickets` and stores the resulting Total Cost in the case.

**AvailableSeatsCount**
- **Type:** Declare Expression
- Maintains the available seat count used by the availability validation logic.

**CaptureSeatCount**
- **Type:** Data Transform
- Supports capturing or preparing the available seat information during the Availability stage.

**CheckSeatStatus**
- **Type:** Data Transform
- Supports processing the seat availability status used by the booking workflow.

---

## 10. Decision Logic

The main booking decision flow is:

```text
Customer Request
       |
       v
Check Available Seats
       |
       v
ValidateSufficientSeats
       |
   +---+---+
   |       |
Available  Unavailable
   |          |
   v          v
Approval    Reject/Stop
   |
   v
Customer Confirmation
   |
 +--+--+
 |     |
Approve Cancel
 |     |
 v     v
Booking Resolve
Execution
```

The ConfirmBooking Decision Table then determines the booking outcome from customer confirmation and seat availability.

---

## 11. Personas

**Customer**
The Customer:
- Submits the movie ticket request.
- Provides booking information.
- Reviews booking details.
- Confirms or cancels the booking.

**Booking Agent**
The Booking Agent supports operational activities such as availability verification and booking processing according to the configured application workflow.

---

## 12. Work Queues

The application uses Show Type based routing.

**PremiumShowQueue**
Used for Premium or Special Show bookings.

**StandardShowQueue**
Used for other show types.

The routing is intended to happen automatically based on the Show Type value.

---

## 13. SLA

The booking case uses a case-level SLA.

| SLA Component | Value |
|---|---|
| Goal | 1 day |
| Deadline | 2 days |

The SLA is measured from case creation.

The expected behavior is:

```text
Case Created
     |
     +--> Goal: 1 day
     |
     +--> Deadline: 2 days
                    |
                    v
             Increase Priority
```

The project does not require a complex escalation path.

---

## 14. Notification

The application uses a Correspondence mechanism for booking confirmation.

The notification should contain:
- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

The notification is associated with successful booking completion and case resolution.

---

## 15. NIP User Story Coverage

The application addresses the ten Movie Ticket Booking user stories as follows:

| User Story | Application Feature |
|---|---|
| US-001 | Submit Movie Ticket Request |
| US-002 | Check Show Availability |
| US-003 | Calculate Booking Cost |
| US-004 | Confirm Booking Request |
| US-005 | Maintain Movie and Show Data |
| US-006 | Review Booking Details |
| US-007 | Process Ticket Booking |
| US-008 | Notify Booking Confirmation |
| US-009 | Define Booking SLA |
| US-010 | Route Booking Request by Show Type |

---

## 16. Technology and Platform

The application is implemented using the Pega Platform.

The project uses Pega's model-driven application development capabilities, including:
- Case Types
- Stages
- Views
- Data Objects
- Properties
- Decision Tables
- Declare Expressions
- Data Transforms
- Correspondence
- SLA configuration
- Work Queues
- Personas

The application is configured through Pega App Studio and Dev Studio.

---

## 17. Development and Configuration Approach

The application was developed incrementally using the Pega development environment.

The development approach included:
1. Creating the Movie Ticket Booking application.
2. Configuring the Movie Ticket Request case type.
3. Creating the required stages.
4. Creating Movie and Show data objects.
5. Adding case properties.
6. Configuring availability processing.
7. Implementing seat validation.
8. Implementing Total Cost calculation.
9. Configuring Customer approval.
10. Implementing booking confirmation logic.
11. Configuring Booking Execution.
12. Configuring Show Type routing.
13. Configuring the booking SLA.
14. Configuring booking confirmation notification.
15. Testing the case flow and correcting configuration issues.

---

## 18. Development Issue and Resolution

During development, the application encountered the following error:

```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The issue occurred in the numeric range configuration of the `ValidateSufficientSeats` Decision Table.

The `Number of Tickets` property was confirmed to be an Integer.

The issue was resolved by configuring the Decision Table to interpret the ticket quantity conditions as numeric ranges rather than treating:

`1 to 10`

as a literal integer value.

The resulting range configuration represents the conditions using numeric boundaries such as:

`>= 1`  
`<= 10`

This allows Pega to evaluate the numeric condition correctly.

---

## 19. Architecture Summary

The application follows a case-centric architecture:

```text
Customer
   |
   v
Movie Ticket Request
   |
   v
Request Capture
   |
   v
Availability
   |
   +--> Seat Validation
   |
   +--> Cost Calculation
   |
   v
Approval
   |
   +--> Confirm
   |      |
   |      v
   |   Booking Execution
   |
   +--> Cancel
          |
          v
       Resolve
```

Confirmed bookings continue to Booking Execution, where ticket and seat information is processed.

The completed booking then produces the required confirmation notification before case resolution.

---

## 20. Repository Documentation

The project repository organizes the application documentation into separate areas:

```text
docs/
├── project-overview.md
├── requirements.md
├── architecture.md
├── case-lifecycle.md
├── data-model.md
├── business-rules.md
└── troubleshooting.md
```

The documentation is intended to provide a clear technical record of the Pega application, its data model, case lifecycle, business rules, architecture, testing, and development issues.

---

## 21. Project Success Criteria

The project is considered functionally complete when the application can:
- Create a Movie Ticket Request.
- Capture the required movie and show information.
- Validate ticket quantity and seat availability.
- Calculate Total Cost automatically.
- Allow the Customer to confirm or cancel the booking.
- Process confirmed bookings.
- Maintain Seat Numbers and Ticket ID.
- Maintain Booking Confirmation Status.
- Notify the Customer after successful completion.
- Apply the 1-day Goal and 2-day Deadline SLA.
- Increase priority when the SLA deadline is missed.
- Automatically route Premium/Special shows to `PremiumShowQueue`.
- Route other shows to `StandardShowQueue`.
- Resolve completed or cancelled requests appropriately.

---

## 22. Final Project Summary

The `NIP-MovieTicket-RishvinReddy` application provides an end-to-end Pega case management solution for movie ticket booking.

The application combines reusable Movie and Show data objects with a structured Movie Ticket Request case lifecycle.

Its business logic automates:
- Availability
- Cost Calculation
- Customer Confirmation
- Booking Execution
- Routing
- SLA
- Notification

The result is a configurable, rule-driven booking workflow that manages a movie ticket request from initial submission through booking completion and resolution.
