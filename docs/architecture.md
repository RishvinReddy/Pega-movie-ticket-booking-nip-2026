# Architecture

## 1. Application Overview

**Application:** `NIP-MovieTicket-RishvinReddy`  
**Platform:** Pega Platform  
**Case Type:** `Movie Ticket Request`  
**Business Context:** CineWave Entertainment

The application manages movie ticket booking requests from request creation through show availability validation, customer confirmation, ticket processing, and booking completion.

The architecture follows a Pega case-management and model-driven approach. The case type contains the end-to-end booking workflow, while reusable data objects hold movie and show information. Business rules automate availability validation, booking cost calculation, confirmation outcomes, SLA handling, and routing.

The NIP project requirements explicitly call for a case lifecycle, reusable Movie and Show data objects, business logic and automation, customer confirmation, seat allocation, notification, SLA tracking, and routing by Show Type.

---

## 2. High-Level Architecture

```text
Customer
   |
   v
+---------------------------+
| Movie Ticket Request      |
| Case Type                 |
+-------------+-------------+
              |
              v
+---------------------------+
| Initial Request Capture   |
| Movie Name                |
| Show Date                |
| Show Time                |
| Number of Tickets        |
+-------------+-------------+
              |
              v
+---------------------------+
| Availability              |
|                           |
| Submit Request            |
| Capture Seat Count        |
| Validate Sufficient Seats |
| Calculate Booking Cost    |
+-------------+-------------+
              |
       Seats available?
        /            \
      No              Yes
      |                |
      v                v
  Stop/Reject     +------------------+
                  | Approval         |
                  |                  |
                  | Review Booking   |
                  | Details          |
                  | Confirm Booking  |
                  +--------+---------+
                           |
                    Confirmed?
                     /       \
                   No         Yes
                   |           |
                   v           v
                Resolve   +------------------+
                          | Booking Execution |
                          |                  |
                          | Allocate Seats   |
                          | Ticket ID        |
                          | Booking Status   |
                          | Route by Show    |
                          +--------+---------+
                                   |
                                   v
                         Booking confirmation
                         and case resolution
```

The initial request capture stage is the entry point for the case. The exact stage label in the Pega application should be kept synchronized with the configured case lifecycle.

---

## 3. Case Management Layer

The central unit of work is the **Movie Ticket Request** case.

The case is responsible for maintaining the booking information and controlling the sequence of work.

### Main case responsibilities

- Capture the customer's movie booking request.
- Validate required booking information.
- Check show and seat availability.
- Calculate the total booking cost.
- Obtain customer confirmation.
- Allocate seats and generate ticket information.
- Route booking execution based on Show Type.
- Track booking status.
- Apply the booking SLA.
- Trigger booking confirmation notification when the case is resolved.

The project requirements specify that booking should proceed only when seats are available and that cancelled requests should be resolved without further booking activity.

---

## 4. Process and Stage Architecture

### Initial Request Capture

The customer initiates the **Movie Ticket Request** case and provides:

- Movie Name
- Show Date
- Show Time
- Number of Tickets

The request information is validated before the case continues.

### Availability

The **Availability** stage handles show and seat verification.

The configured process includes:

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

The project requires:

- Seat Availability Status
- Available Seats Count
- Ticket Price
- Number of Tickets
- Total Cost

The application contains decision logic for validating whether the requested booking can proceed.

### Approval

The **Approval** stage gives the Customer an opportunity to review and confirm or cancel the booking.

The process is:

```text
Review Booking Details
          |
          v
Confirm Booking
```

The application uses the booking status to determine whether the request proceeds to ticket processing or is resolved as cancelled.

### Booking Execution

The **Booking Execution** stage handles final booking activities.

It maintains:

- Booking Confirmation Status
- Seat Numbers
- Ticket ID
- Booking status

The stage also contains the routing logic required by the NIP project for Show Type.

---

## 5. Data Architecture

The application uses reusable data objects so movie and show information is maintained independently from an individual booking case.

### Movie

The required reusable **Movie** data object contains:

| Property | Purpose |
|---|---|
| Movie Name | Identifies the movie |
| Genre | Identifies the movie genre |

### Show

The required reusable **Show** data object contains:

| Property | Purpose |
|---|---|
| Movie Name | Associates the show with a movie |
| Show Date | Date of the screening |
| Show Time | Time of the screening |
| Seat Capacity | Total seating capacity |

The Movie and Show data objects are associated with the **Movie Ticket Request** case so that booking requests can reuse consistent movie and show information.

---

## 6. Case Data

In addition to reusable Movie and Show data, the case maintains booking-specific information.

### Request data

- Movie Name
- Show Date
- Show Time
- Number of Tickets

### Availability and pricing data

- Seat Availability Status
- Available Seats Count
- Ticket Price
- Total Cost

### Confirmation data

- Booking Status

### Execution data

- Booking Confirmation Status
- Seat Numbers
- Ticket ID

### Routing data

- Show Type

The exact property names should remain synchronized with the properties configured in the Pega application because the NIP evaluation checks application configuration against the project requirements.

---

## 7. Business Rules Layer

The application uses Pega rules to implement business logic without requiring traditional application-code implementation.

### Seat availability validation

The availability logic evaluates the requested number of tickets against available seats.

The current implementation includes the **ValidateSufficientSeats** decision table.

Its purpose is to determine the appropriate seat-availability outcome before booking proceeds.

### Booking confirmation

The current implementation includes the **ConfirmBooking** decision table.

It evaluates:

- Customer confirmation decision
- Seat availability status

and returns the corresponding booking outcome.

The configured outcomes visible in the application include:

- Confirmed
- Cancelled
- Rejected
- Pending

The exact decision-table rows should be maintained according to the final Pega configuration.

### Booking cost calculation

The project requires:

```text
Total Cost = Ticket Price × Number of Tickets
```

The calculated value must be maintained automatically in the case.

The exact rule name and rule type used in the final application should be recorded in `docs/business-rules.md` after confirming the saved rule in Pega.

---

## 8. Routing Architecture

Booking execution is routed according to **Show Type**.

The required routing model is:

```text
                 Show Type
                    |
          +---------+---------+
          |                   |
       Premium              Other
          |                   |
          v                   v
PremiumShowQueue      StandardShowQueue
```

### PremiumShowQueue

Used for premium or special screenings.

### StandardShowQueue

Used for all other shows.

The NIP project allows either a **When rule** or a **Decision Table** for this routing logic.

Routing should occur automatically without manual intervention.

---

## 9. SLA Architecture

The **Movie Ticket Request** case has an SLA based on case creation.

| SLA element | Requirement |
|---|---:|
| Goal | 1 day |
| Deadline | 2 days |

Expected behavior:

- At the goal threshold, the case is flagged as approaching the deadline.
- At the deadline, case priority is automatically increased.

No complex escalation path is required by the project specification.

---

## 10. Notification Architecture

After a successful booking reaches resolution, the application should notify the Customer.

The notification is expected to include:

- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

The NIP specification identifies a Correspondence rule as the mechanism for generating this notification.

---

## 11. Persona Architecture

The application primarily separates responsibilities between the customer-facing activities and booking-processing activities.

### Customer

Responsible for:

- Submitting the movie ticket request.
- Reviewing booking details.
- Confirming or cancelling the booking.

### Booking Agent

Responsible for:

- Verifying show availability.
- Capturing available seat information.
- Supporting booking processing where assigned by the application.

The project specification explicitly identifies the Customer persona and refers to the Booking Agent for availability verification.

---

## 12. Rule Interaction Model

The main automation can be viewed as:

```text
Case Data
   |
   +--> Availability Rules
   |       |
   |       +--> Seat Availability Status
   |       +--> Available Seats Count
   |
   +--> Pricing Rule
   |       |
   |       +--> Total Cost
   |
   +--> Confirmation Rule
   |       |
   |       +--> Booking Status
   |
   +--> Routing Rule
   |       |
   |       +--> PremiumShowQueue
   |       +--> StandardShowQueue
   |
   +--> SLA
   |
   +--> Correspondence
           |
           +--> Booking Confirmation
```

This separates workflow orchestration from individual business rules and keeps the application maintainable.

---

## 13. Validation and Decision Flow

The critical booking decision is the seat availability check.

```text
Requested Tickets
        |
        v
Available Seats Count
        |
        v
Validate Sufficient Seats
        |
   +----+----+
   |         |
Available  Unavailable
   |         |
   v         v
Approval   Reject/Stop
   |
   v
Customer Confirmation
   |
 +--+--+
 |     |
Confirm Cancel
 |     |
 v     v
Booking Resolve
Execution
```

This prevents the workflow from continuing to final booking when sufficient seats are not available.

---

## 14. Architecture Principles

The implementation follows these principles:

1. **Case-centric design** — the Movie Ticket Request case represents the complete booking transaction.
2. **Reusable data** — Movie and Show are modeled as reusable data objects rather than duplicating all reference information inside every process step.
3. **Rule-based automation** — availability, confirmation, pricing, SLA, and routing are handled through configurable Pega rules.
4. **Persona-based work** — customer confirmation is separated from operational booking activities.
5. **Controlled progression** — the case moves forward only when the required business conditions are satisfied.
6. **Automatic routing** — Show Type determines the appropriate work queue.
7. **Traceability** — booking status, ticket ID, seat numbers, and total cost remain part of the case record.
8. **Eventual notification** — successful resolution produces a customer-facing booking confirmation.

---

## 15. Current Implementation Notes

The application was created as:

`NIP-MovieTicket-RishvinReddy`

The Pega App Studio overview describes it as a CineWave Entertainment application for managing movie ticket booking requests, availability, booking cost, customer confirmation, seat allocation, booking status, notifications, SLA tracking, and routing.

The current Dev Studio configuration screenshots confirm decision-table work for:

- `ConfirmBooking`
- `ValidateSufficientSeats`

The availability process was also configured with the sequence:

`Submit Request → Capture Seat Count → Validate Sufficient Seats → Approval`

The approval process was configured with:

`Review Booking Details → Confirm Booking`

These process connectors were reviewed and corrected so that each step has a valid target and the required transition context.

---

## 16. Architecture-to-User-Story Mapping

| User Story | Architectural component |
|---|---|
| US-001 | Movie Ticket Request case + request capture |
| US-002 | Availability stage + seat availability data |
| US-003 | Pricing business rule + Total Cost |
| US-004 | Approval stage + Booking Status |
| US-005 | Movie and Show reusable data objects |
| US-006 | Review Booking Details process |
| US-007 | Booking Execution + seat/ticket data |
| US-008 | Correspondence notification |
| US-009 | Case SLA |
| US-010 | Show Type routing + work queues |

---

## 17. End-to-End Architecture Summary

```text
Customer
   |
   v
Movie Ticket Request
   |
   +---- Request Details
   |
   v
Availability
   |
   +---- Check Seats
   +---- Calculate Cost
   |
   v
Sufficient Seats?
   |
   +---- No ------> Resolve / Reject
   |
   +---- Yes
            |
            v
         Approval
            |
       Customer Decision
         /          \
     Cancel        Confirm
       |              |
       v              v
    Resolve     Booking Execution
                     |
             +-------+-------+
             |       |       |
          Allocate Ticket  Route
           Seats    ID    by Show Type
                     |
             +-------+-------+
             |               |
      PremiumShowQueue  StandardShowQueue
             |
             v
      Booking Completion
             |
             v
      Confirmation Notice
             |
             v
          Resolved
```

The architecture therefore provides a single case-driven flow from movie ticket request to confirmed booking, while reusable data objects and configurable Pega rules provide the supporting data and automation layers.
