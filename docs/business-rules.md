# Business Rules

## 1. Overview

The `NIP-MovieTicket-RishvinReddy` application uses Pega business rules to automate movie ticket booking decisions and calculations.

The primary business rules support:

* Seat availability validation
* Available seat calculation/capture
* Booking cost calculation
* Customer booking confirmation
* Booking status determination
* SLA management
* Show Type based routing
* Booking confirmation notification

The business rules are designed around the Movie Ticket Request case and its associated Movie and Show data.

The NIP requirements specifically require the application to calculate Total Cost automatically, validate seat availability before booking, capture customer confirmation, allocate seats, notify the customer, apply an SLA, and route bookings according to Show Type. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 2. Rule Inventory

| Rule / Component | Rule Type | Purpose |
|---|---|---|
| `ValidateSufficientSeats` | Decision Table | Determines whether requested tickets can be supported by available seats |
| `ConfirmBooking` | Decision Table | Determines booking outcome from customer confirmation and seat availability |
| `CalculateTotalCost` | Declare Expression | Calculates Total Cost from Ticket Price and Number of Tickets |
| `AvailableSeatsCount` | Declare Expression | Maintains/calculates the available seat count |
| `CaptureSeatCount` | Data Transform | Captures or prepares available seat information |
| `CheckSeatStatus` | Data Transform | Processes seat availability status |
| Booking SLA | SLA configuration | Defines booking Goal and Deadline |
| Show Type routing rule | When rule or Decision Table | Routes bookings to the appropriate work queue |
| Booking confirmation correspondence | Correspondence | Notifies the customer after successful booking |

The NIP specification permits either a When rule or a Decision Table for Show Type routing. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 3. ValidateSufficientSeats

**Rule Name:** `ValidateSufficientSeats`  
**Rule Type:** Decision Table  

**Purpose**

The `ValidateSufficientSeats` decision table validates whether the number of tickets requested by the customer can be supported by the available seats.

The rule is executed as part of the Availability processing before the booking proceeds to customer confirmation.

The NIP requirement states that booking should proceed only when seats are available and that the application must capture both Seat Availability Status and Available Seats Count. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

**Input Properties**

- Number of Tickets
- Available Seats Count

**Output / Decision Property**

- Seat Availability Status

**Current Decision Ranges**

The configured decision table uses numeric ranges for Number of Tickets and Available Seats Count.

The current configuration shown in Pega includes:

Number of Tickets:
- 1 to 10
- 11 to 50
- 51 or more

and corresponding available-seat conditions such as:
- 10 or more
- 5 to 9
- 4

with the resulting Seat Availability Status values.

The exact final output values should match the values saved in the Pega decision table.

**Business Purpose**

The rule prevents the booking process from proceeding when there are insufficient seats.

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
   +----+----+
   |         |
Available  Insufficient
   |         |
   v         v
Proceed    Do not proceed
```

---

## 4. Important Numeric Range Configuration

The `NumberFormatException` encountered during testing was associated with the numeric range values used in the decision-table configuration.

The error displayed in the running case was:

```
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The important configuration detail is that Number of Tickets is an Integer property.

The current Pega configuration shows:

- **Number of Tickets** -> Type: Integer

Therefore, when the Decision Table uses ranges, the range must be configured using Pega's range/operator functionality rather than being treated as a literal numeric value.

For example:

- Minimum = 1
- Maximum = 10

rather than treating `"1 to 10"` as a single integer value.

This is especially important because the NIP project requires Number of Tickets to participate in the seat-validation logic.

---

## 5. AvailableSeatsCount

**Rule Name:** `AvailableSeatsCount`  
**Rule Type:** Declare Expression  

**Purpose**

The `AvailableSeatsCount` rule is used to maintain the number of seats currently available for the selected show.

The application uses this value when evaluating seat availability.

**Related Property:** Available Seats Count  
**Property Type:** Integer  

**Business Purpose**

The available seat count provides the numeric input required by the seat-validation decision logic.

Conceptually:

```text
Show / Seat Allocation Data
          |
          v
Available Seats Count
          |
          v
ValidateSufficientSeats
```

The NIP specification explicitly requires Available Seats Count to be captured as part of the Availability stage. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 6. CalculateTotalCost

**Rule Name:** `CalculateTotalCost`  
**Rule Type:** Declare Expression  

**Purpose**

The `CalculateTotalCost` rule automatically calculates the total price of the requested movie tickets.

The calculation uses:
- Ticket Price
- Number of Tickets

**Formula**

`Total Cost = Ticket Price × Number of Tickets`

**Example**

If:
- Ticket Price = 200
- Number of Tickets = 3

then:
`Total Cost = 200 × 3 = 600`

**Related Properties**

- Ticket Price
- Number of Tickets
- Total Cost

The NIP requirement explicitly states that Ticket Price and Number of Tickets should be used to derive the calculated Total Cost, and that the value should be automatically updated and stored in the case. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 7. Total Cost Business Rule

The Total Cost calculation is intentionally implemented as a calculated value rather than manually entered by the customer.

**Input:** Ticket Price, Number of Tickets  
**Calculation:** Ticket Price × Number of Tickets  
**Output:** Total Cost  

**Flow**

```text
Ticket Price
     |
     +------+
            |
            v
       CalculateTotalCost
            ^
            |
     +------+
     |
Number of Tickets
     |
     v
Total Cost
```

This reduces manual calculation errors and ensures that the amount displayed during booking review reflects the current ticket quantity and ticket price.

---

## 8. CaptureSeatCount

**Rule Name:** `CaptureSeatCount`  
**Rule Type:** Data Transform  

**Purpose**

The `CaptureSeatCount` data transform supports the Availability stage by capturing or preparing the available seat count used by the booking process.

**Related Property:** Available Seats Count  

**Processing Context**

The configured Availability flow uses:

```text
Submit Request
      |
      v
Capture Seat Count
      |
      v
Validate Sufficient Seats
```

This establishes the available-seat information before the decision table evaluates whether the requested booking can continue.

---

## 9. CheckSeatStatus

**Rule Name:** `CheckSeatStatus`  
**Rule Type:** Data Transform  

**Purpose**

The `CheckSeatStatus` data transform supports the determination of the seat availability state used by the booking workflow.

**Related Property:** Seat Availability Status  

The resulting status is used by subsequent booking logic to determine whether the booking can proceed.

---

## 10. ConfirmBooking

**Rule Name:** `ConfirmBooking`  
**Rule Type:** Decision Table  

**Purpose**

The `ConfirmBooking` decision table determines the booking outcome after the customer has reviewed the booking details and provided a confirmation decision.

The rule evaluates:
- Customer Confirmation
- Seat Availability Status

and produces the appropriate booking result.

The NIP requirement specifies that the Approval stage must be assigned to the Customer persona, capture a Booking Status, and branch on confirmation or cancellation. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

**Current Configuration**

The Decision Table configuration shown in Pega contains conditions corresponding to:
- Customer Confirmation
- Seat Availability Status

and the action:
- Return

The configured outcomes visible during development include:
- Confirmed
- Cancelled
- Rejected
- Pending

**Decision Logic**

Conceptually:

```text
Customer Confirmation
        +
Seat Availability Status
        |
        v
   ConfirmBooking
        |
        v
Booking Outcome
```

Examples:
- Approve + Available → Confirmed
- Cancel + Available → Cancelled
- Approve + Unavailable → Rejected
- Otherwise → Pending

These outcomes reflect the Decision Table configuration observed during development.

---

## 11. Booking Status

The booking confirmation logic uses a booking status to represent the customer's decision and the resulting booking state.

Relevant outcomes include:
- Confirmed
- Cancelled
- Rejected
- Pending

The purpose of this property is to make the booking decision visible to the case and allow the workflow to branch appropriately.

The NIP specification requires confirmed bookings to continue to ticket processing while cancelled requests should be resolved without further booking action. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 12. Seat Availability Business Logic

The overall seat-validation process is:

```text
Customer enters Number of Tickets
        |
        v
Show information
        |
        v
Available Seats Count
        |
        v
ValidateSufficientSeats
        |
   +----+----+
   |         |
Enough     Not enough
seats       seats
   |         |
   v         v
Continue    Reject /
to Approval Stop
```

The key business rule is that final ticket processing must not occur when sufficient seats are unavailable.

---

## 13. Booking Confirmation Business Logic

After seat availability has been established, the customer reviews the booking.

```text
Availability Confirmed
        |
        v
Review Booking Details
        |
        v
Customer Confirmation
        |
        v
ConfirmBooking
        |
   +----+----+
   |         |
Confirm    Cancel
   |         |
   v         v
Booking    Resolve
Execution
```

This separates availability validation from the customer's final confirmation decision.

---

## 14. Show Type Routing

**Business Requirement**

The application must automatically route booking requests according to Show Type.

The required queues are:
- `PremiumShowQueue`
- `StandardShowQueue`

The NIP requirement specifies:
- Premium / special screening → `PremiumShowQueue`
- All other shows → `StandardShowQueue`

*(Reference: Movie_Ticket_Booking_NIP.pdf)*

**Decision Logic**

```text
                 Show Type
                    |
          +---------+---------+
          |                   |
       Premium / Special    Other
          |                   |
          v                   v
   PremiumShowQueue   StandardShowQueue
```

The NIP specification allows this logic to be implemented using either a When rule or a Decision Table.

The final documentation should use the exact rule name once the saved routing rule is confirmed in Dev Studio.

---

## 15. Booking SLA

**Rule / Configuration:** Movie Ticket Request SLA  

**Goal:** 1 day  
**Deadline:** 2 days  

**Behavior**

When the goal is missed:
- Case is flagged as approaching deadline

When the deadline is missed:
- Case priority is automatically increased

The NIP requirements explicitly define a one-day Goal and two-day Deadline from case creation and state that no complex escalation path is required. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 16. Booking Confirmation Notification

**Rule Type:** Correspondence  

**Trigger**

The notification is associated with successful booking completion and case resolution.

**Information Included**

The notification should contain:
- Case ID
- Movie Name
- Show Date and Time
- Number of Tickets
- Seat Numbers
- Total Cost

The NIP project specifically requires a Correspondence mechanism to notify the Customer when the booking is successfully completed. *(Reference: Movie_Ticket_Booking_NIP.pdf)*

---

## 17. Rule Dependency Flow

The major business rules interact as follows:

```text
                   Movie Ticket Request
                           |
                           v
                   Request Details
                           |
                           v
                    CaptureSeatCount
                           |
                           v
                  AvailableSeatsCount
                           |
                           v
                ValidateSufficientSeats
                           |
                    +------+------+
                    |             |
                 Available     Unavailable
                    |             |
                    v             v
             CalculateTotalCost   Reject/Stop
                    |
                    v
             Review Booking Details
                    |
                    v
               ConfirmBooking
                    |
              +-----+-----+
              |           |
           Confirm       Cancel
              |           |
              v           v
      Booking Execution  Resolve
              |
              v
        Show Type Routing
          /           \
         /             \
PremiumShowQueue   StandardShowQueue
         |
         v
   Booking Completion
         |
         v
   Correspondence
         |
         v
      Resolved
```

---

## 18. Business Rule Design Principles

The application follows these business-rule principles:

1. **Automatic calculation:** Values that can be derived from existing case information are calculated automatically. Example: `Total Cost = Ticket Price × Number of Tickets`
2. **Validation before progression:** Seat availability is evaluated before the booking proceeds to final confirmation.
3. **Explicit customer decision:** The customer must confirm or cancel the booking before final ticket processing.
4. **Decision-table driven outcomes:** Complex combinations of customer confirmation and availability status are handled using a Decision Table rather than hard-coded logic.
5. **Automatic routing:** Show Type determines the appropriate work queue without requiring manual routing.
6. **SLA-based case management:** The case has explicit Goal and Deadline thresholds to support timely booking processing.
7. **Traceable booking state:** Booking status, seat numbers, ticket ID, and confirmation status are maintained within the case.

---

## 19. Rule-to-User-Story Mapping

| User Story | Business Rule / Configuration |
|---|---|
| US-001 — Submit Movie Ticket Request | Case validation and request properties |
| US-002 — Check Show Availability | CaptureSeatCount, AvailableSeatsCount, ValidateSufficientSeats |
| US-003 — Calculate Booking Cost | CalculateTotalCost |
| US-004 — Confirm Booking Request | ConfirmBooking + Booking Status |
| US-005 — Maintain Movie and Show Data | Movie and Show data objects |
| US-006 — Review Booking Details | Review/confirmation UI |
| US-007 — Process Ticket Booking | Seat allocation + Ticket ID + Booking Confirmation Status |
| US-008 — Notify Booking Confirmation | Correspondence rule |
| US-009 — Define Booking SLA | Goal = 1 day, Deadline = 2 days |
| US-010 — Route Booking Request by Show Type | Show Type routing rule / Decision Table |

The mapping follows the NIP project requirements for all ten Movie Ticket Booking user stories.  

---

## 20. Testing Considerations

The business rules should be tested using representative combinations of inputs.

**Total Cost**

| Ticket Price | Number of Tickets | Expected Total Cost |
|---|---|---|
| 200 | 1 | 200 |
| 200 | 3 | 600 |
| 250 | 4 | 1000 |

**Seat Availability**

Test cases should include:
- Requested tickets <= available seats
- Requested tickets > available seats
- Low seat availability
- Multiple ticket quantities

**Confirmation**

Test:
- Approve + Available
- Cancel + Available
- Approve + Unavailable
- Other combinations

**Routing**

Test:
- Premium / Special Show
- Other Show Type

The expected result is automatic routing to the appropriate work queue.

---

## 21. Known Development Issue — NumberFormatException

During development, the application produced:

```
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The issue occurred while processing the numeric range used in the seat-validation Decision Table.

The relevant property, Number of Tickets, was confirmed as an Integer property in Pega.

The correction was to configure the Decision Table range using its numeric range/operator configuration rather than treating the string: `"1 to 10"` as a literal integer.

This is an important configuration consideration for numeric Decision Table conditions.

---

## 22. Final Business Rule Summary

The application's business logic can be summarized as:

1. Capture booking request
          ↓
2. Capture available seats
          ↓
3. Validate requested quantity
          ↓
4. Calculate total cost
          ↓
5. Review booking details
          ↓
6. Customer confirms/cancels
          ↓
7. Determine booking outcome
          ↓
8. Allocate seats and generate ticket
          ↓
9. Route according to Show Type
          ↓
10. Notify customer
          ↓
11. Resolve case

The overall design keeps business decisions configurable in Pega rules and separates calculation, validation, confirmation, routing, SLA, and notification responsibilities.
