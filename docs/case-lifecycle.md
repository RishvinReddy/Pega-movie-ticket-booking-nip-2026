# Case Lifecycle

## 1. Overview
The `Movie Ticket Request` case represents the complete movie ticket booking process for the `NIP-MovieTicket-RishvinReddy` application.

The case lifecycle is designed to take a customer from submitting a movie ticket request through availability verification, booking cost calculation, customer confirmation, ticket processing, notification, and final case resolution.

The lifecycle follows the requirements defined for the Movie Ticket Booking Management Application in the National Internship Program.

---

## 2. Case Type
**Case Type:** `Movie Ticket Request`

The Movie Ticket Request case represents one complete movie ticket booking request.

The case stores the information required throughout the booking process, including movie details, show information, ticket quantity, seat availability, pricing, booking confirmation, and ticket information.

---

## 3. Lifecycle Overview

The intended case flow is:

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

The lifecycle is controlled by the case stages and the business rules configured in Pega.

---

## 4. Stage 1 — Initial Request

**Purpose**

The initial stage starts the movie ticket booking process and captures the customer's booking request.

The Customer provides the information required to initiate the case.

**Information Captured**

* Movie Name
* Show Date
* Show Time
* Number of Tickets

**Processing**

The customer enters the booking information and submits the request.

Required information is validated before the case proceeds to availability processing.

**Flow**

```text
Customer
   |
   v
Enter Movie Details
   |
   +--> Movie Name
   +--> Show Date
   +--> Show Time
   +--> Number of Tickets
   |
   v
Submit Request
   |
   v
Availability
```

The NIP project requires an initial stage for capturing these booking details and validating the request before further processing. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 5. Stage 2 — Availability

**Purpose**

The Availability stage determines whether sufficient seats are available for the requested show.

This stage ensures that the booking does not proceed when the requested number of tickets cannot be accommodated.

**Main Steps**

The configured availability flow is:

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

The application currently contains the following configured rules/components:

* `CaptureSeatCount`
* `CheckSeatStatus`
* `AvailableSeatsCount`
* `ValidateSufficientSeats`

**Information Used**

The availability process uses:

* Number of Tickets
* Available Seats Count
* Seat Availability Status

**Decision**

The `ValidateSufficientSeats` decision table evaluates the requested ticket quantity against available seating information.

Conceptually:

```text
Requested Tickets
        |
        v
Available Seats Count
        |
        v
ValidateSufficientSeats
        |
     +--+--+
     |     |
 Available  Unavailable
     |          |
     v          v
 Continue    Reject/Stop
     |
     v
 Approval
```

The NIP specification requires booking to proceed only when seats are available. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 6. Stage 3 — Booking Cost Calculation

The booking cost calculation occurs as part of the availability processing.

**Purpose**

The system automatically calculates the total cost of the requested tickets.

**Inputs**

- Ticket Price
- Number of Tickets

**Formula**

`Total Cost = Ticket Price × Number of Tickets`

**Example**

```text
Ticket Price = 200
Number of Tickets = 3
Total Cost = 200 × 3 = 600
```

The calculated Total Cost is then available for the customer to review before confirming the booking.

The NIP project requires this calculation to be performed automatically through a business rule and stored in the case. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 7. Stage 4 — Approval

**Purpose**

The Approval stage gives the Customer an opportunity to review the booking and confirm or cancel it.

The approval process configured in the application is:

```text
Review Booking Details
          |
          v
Confirm Booking
```

**Customer Responsibility**

The Customer reviews:

* Movie Name
* Show Timing
* Number of Tickets
* Total Cost

The Customer then chooses whether to confirm or cancel the booking.

The NIP specification requires the Approval stage to be assigned to the Customer persona. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 8. Confirm Booking Decision

The application uses the `ConfirmBooking` Decision Table to determine the booking outcome.

**Conditions**

The decision logic evaluates:

- Customer Confirmation
- Seat Availability Status

**Outcomes**

The configured decision outcomes include:

- Confirmed
- Cancelled
- Rejected
- Pending

Conceptually:

```text
Customer Decision
        +
Seat Availability
        |
        v
   ConfirmBooking
        |
   +----+----+
   |         |
Confirm     Cancel
   |         |
   v         v
Booking    Resolve
Execution
```

A confirmed booking continues to ticket processing.

A cancelled request is resolved without further booking activity, as required by the NIP project. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 9. Stage 5 — Booking Execution

**Purpose**

The Booking Execution stage handles the final ticket-booking activities.

This stage is entered after the customer confirms the booking.

**Main Activities**

The stage is responsible for:

* Allocating seats
* Updating booking status
* Maintaining Booking Confirmation Status
* Maintaining Seat Numbers
* Generating or maintaining Ticket ID
* Routing the booking according to Show Type

**Flow**

```text
Confirmed Booking
       |
       v
Booking Execution
       |
       +--> Allocate Seats
       |
       +--> Generate Ticket ID
       |
       +--> Update Booking Status
       |
       +--> Determine Show Type
       |
       v
Booking Completed
```

The NIP specification explicitly requires a Booking Execution stage with Seat Numbers, Ticket ID, and Booking Confirmation Status maintained in the case. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 10. Show Type Routing

During Booking Execution, the case is routed automatically according to the value of Show Type.

The required work queues are:

- `PremiumShowQueue`
- `StandardShowQueue`

**Routing Logic**

```text
Show Type
    |
    +----------------------+
    |                      |
Premium / Special        Other
    |                      |
    v                      v
PremiumShowQueue     StandardShowQueue
```

- **PremiumShowQueue**: Used for premium or special screenings.
- **StandardShowQueue**: Used for all other show types.

The NIP project allows this routing to be implemented using either a When rule or a Decision Table. The routing should happen automatically without manual intervention. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 11. Booking Completion

After the Booking Execution activities are completed, the booking contains the information necessary to track the completed ticket.

The case maintains:

- Booking Confirmation Status
- Seat Numbers
- Ticket ID
- Booking Status

The successful booking then moves toward case resolution.

---

## 12. Booking Confirmation Notification

After successful booking completion, the Customer should receive a booking confirmation notification.

The notification is associated with case resolution.

**Notification Information**

The notification should contain:

* Case ID
* Movie Name
* Show Date and Time
* Number of Tickets
* Seat Numbers
* Total Cost

The required notification mechanism is a Correspondence rule triggered when the booking is successfully completed and the case is resolved. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 13. Case Resolution

The case reaches its final state after the appropriate booking outcome has been completed.

**Successful Booking**

```text
Customer Confirms
       |
       v
Booking Execution
       |
       v
Seat Allocation
       |
       v
Ticket Generated
       |
       v
Booking Confirmation
       |
       v
Case Resolved
```

**Cancelled Booking**

```text
Customer Cancels
       |
       v
Booking Not Processed
       |
       v
Case Resolved
```

**Insufficient Seats**

```text
Seat Validation
       |
       v
Insufficient Seats
       |
       v
Booking Does Not Proceed
       |
       v
Appropriate Resolution
```

The NIP requirements specify that confirmed bookings proceed to ticket processing, while cancelled requests are resolved without further action. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 14. Complete End-to-End Flow

The complete intended lifecycle is:

```text
+-------------------------+
| Movie Ticket Request    |
+------------+------------+
             |
             v
+-------------------------+
| Initial Request         |
|                         |
| Movie Name              |
| Show Date               |
| Show Time               |
| Number of Tickets       |
+------------+------------+
             |
             v
+-------------------------+
| Availability            |
|                         |
| Capture Seat Count      |
| Validate Seats          |
| Calculate Total Cost    |
+------------+------------+
             |
             v
       Seats Available?
          /       \
        No         Yes
        |           |
        v           v
   Reject/Stop   Approval
                    |
                    v
          Review Booking Details
                    |
                    v
             Customer Decision
                /         \
             Cancel       Confirm
               |             |
               v             v
            Resolve    Booking Execution
                           |
                           +--> Allocate Seats
                           |
                           +--> Ticket ID
                           |
                           +--> Booking Status
                           |
                           +--> Show Type Routing
                           |
                           v
                    Booking Completed
                           |
                           v
                    Confirmation Notice
                           |
                           v
                       Resolved
```

---

## 15. Stage Responsibilities

| Stage | Primary Responsibility | Main Data / Actions |
|---|---|---|
| Initial Request | Capture customer request | Movie Name, Show Date, Show Time, Number of Tickets |
| Availability | Verify seats and calculate cost | Seat Availability Status, Available Seats Count, Ticket Price, Total Cost |
| Approval | Obtain customer decision | Booking Status, confirmation/cancellation |
| Booking Execution | Finalize booking | Seat Numbers, Ticket ID, Booking Confirmation Status |
| Resolution | Complete the case | Booking confirmation and notification |

The NIP specification explicitly identifies Availability, Approval, and Booking Execution as required lifecycle stages.

---

## 16. Personas and Responsibilities

**Customer**

The Customer is responsible for:

1. Submitting the movie ticket request.
2. Providing the required booking information.
3. Reviewing booking details.
4. Confirming or cancelling the booking.

**Booking Agent**

The Booking Agent supports availability verification and booking processing activities as configured in the application.

The NIP specification identifies the Booking Agent as the persona that can verify show availability. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 17. Lifecycle Business Rules

The case lifecycle is controlled by several business rules.

**Availability**
```text
Number of Tickets
        +
Available Seats Count
        ↓
ValidateSufficientSeats
        ↓
Seat Availability Status
```

**Pricing**
```text
Ticket Price
      ×
Number of Tickets
      ↓
Total Cost
```

**Confirmation**
```text
Customer Confirmation
        +
Seat Availability Status
        ↓
ConfirmBooking
        ↓
Booking Status
```

**Routing**
```text
Show Type
    ↓
PremiumShowQueue
      OR
StandardShowQueue
```

**SLA**
```text
Case Creation
     |
     +--> Goal: 1 day
     |
     +--> Deadline: 2 days
```

---

## 18. SLA During the Lifecycle

The Movie Ticket Request case has an SLA beginning from case creation.

| SLA | Value |
|---|---|
| Goal | 1 day |
| Deadline | 2 days |

- If the Goal is missed, the case is flagged as approaching its deadline.
- If the Deadline is missed, the case priority is automatically increased.

The NIP project does not require a complex escalation path. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 19. Lifecycle Design Principles

The case lifecycle follows these principles:

- **Sequential processing**: The booking moves through defined stages rather than allowing final booking before availability and customer confirmation.
- **Availability before booking**: The system checks seat availability before allowing the booking to proceed.
- **Customer confirmation**: The customer receives visibility into the booking details before making the final confirmation decision.
- **Automated calculation**: Total Cost is calculated automatically rather than manually entered.
- **Automated routing**: Booking Execution is routed according to Show Type.
- **Traceable case state**: Booking Status, Seat Numbers, Ticket ID, and Booking Confirmation Status are maintained for tracking.
- **Resolution after completion**: The case reaches resolution after the appropriate booking outcome is completed.

---

## 20. User Story Mapping

| User Story | Lifecycle Component |
|---|---|
| US-001 — Submit Movie Ticket Request | Initial Request |
| US-002 — Check Show Availability | Availability |
| US-003 — Calculate Booking Cost | Availability / Cost Calculation |
| US-004 — Confirm Booking Request | Approval |
| US-005 — Maintain Movie and Show Data | Movie and Show data objects |
| US-006 — Review Booking Details | Approval / Review Booking Details |
| US-007 — Process Ticket Booking | Booking Execution |
| US-008 — Notify Booking Confirmation | Resolution / Correspondence |
| US-009 — Define Booking SLA | Case-level SLA |
| US-010 — Route Booking Request by Show Type | Booking Execution / Routing |

The mapping follows the ten Movie Ticket Booking user stories defined by the NIP project.

---

## 21. Final Lifecycle Summary

The Movie Ticket Request case follows this overall process:

```text
Request
  ↓
Availability
  ↓
Cost Calculation
  ↓
Review
  ↓
Customer Confirmation
  ↓
Booking Execution
  ↓
Seat Allocation
  ↓
Ticket Generation
  ↓
Show Type Routing
  ↓
Booking Confirmation
  ↓
Notification
  ↓
Resolved
```

If the customer cancels or sufficient seats are unavailable, the booking does not continue to final ticket processing and the case follows the appropriate resolution path.

The lifecycle therefore provides a controlled end-to-end process for moving a movie ticket request from creation to completion while applying availability validation, pricing, customer confirmation, ticket processing, routing, SLA tracking, and notification.
