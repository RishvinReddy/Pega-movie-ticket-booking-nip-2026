# Data Model

## 1. Overview
The `NIP-MovieTicket-RishvinReddy` application uses a combination of reusable data objects and case-level properties to support the Movie Ticket Booking process.

The primary reusable data objects are:
- Movie
- Show

The `Movie Ticket Request` case uses booking-specific properties for request details, availability, pricing, confirmation, and ticket processing.

The data model is designed to support the requirements of the National Internship Program Movie Ticket Booking Management Application.

---

## 2. Data Model Overview
The high-level data model is:
```text
+------------------+
|      Movie       |
+------------------+
| Movie Name       |
| Genre            |
+--------+---------+
         |
         | associated with
         v
+------------------+
|      Show        |
+------------------+
| Movie Name       |
| Show Date        |
| Show Time        |
| Seat Capacity    |
+--------+---------+
         |
         | referenced by
         v
+---------------------------+
| Movie Ticket Request      |
+---------------------------+
| Movie Name                |
| Show Date                 |
| Show Time                 |
| Number of Tickets         |
| Ticket Price              |
| Total Cost                |
| Seat Availability Status  |
| Available Seats Count     |
| Customer Confirmation     |
| Booking Status            |
| Show Type                 |
| Seat Numbers              |
| Ticket ID                 |
| Booking Confirmation      |
+---------------------------+
```

The Movie and Show objects provide reusable movie/show information, while the case stores information specific to an individual ticket booking request.

---

## 3. Movie Data Object

**Purpose**

The Movie data object represents reusable information about a movie.

It prevents movie-related information from being duplicated across individual booking cases.

**Properties**

| Property | Purpose |
|---|---|
| Movie Name | Identifies the movie |
| Genre | Identifies the movie genre |

**Logical Structure**

```text
Movie
 |
 +-- Movie Name
 |
 +-- Genre
```

**Example**

- **Movie Name**: Example Movie
- **Genre**: Action

The exact movie records used for testing can be maintained independently from the booking case.

---

## 4. Show Data Object

**Purpose**

The Show data object represents information about a movie screening/show.

A Show associates movie information with the date, time, and seating capacity for the screening.

**Properties**

| Property | Purpose |
|---|---|
| Movie Name | Identifies the movie associated with the show |
| Show Date | Identifies the date of the screening |
| Show Time | Identifies the time of the screening |
| Seat Capacity | Defines the seating capacity of the show |

**Logical Structure**

```text
Show
 |
 +-- Movie Name
 |
 +-- Show Date
 |
 +-- Show Time
 |
 +-- Seat Capacity
```

**Example**

- **Movie Name**: Example Movie
- **Show Date**: 2026-08-31
- **Show Time**: 18:30
- **Seat Capacity**: 100

The exact sample values depend on the records configured in the Pega application.

---

## 5. Movie Ticket Request Case Data

The Movie Ticket Request case contains the data required to process an individual booking.

The case data can be grouped into the following categories:

1. Request information
2. Availability information
3. Pricing information
4. Confirmation information
5. Booking execution information
6. Routing information

---

## 6. Request Information

The request section contains the information entered when the customer creates the booking request.

**Properties**

| Property | Purpose |
|---|---|
| Movie Name | Identifies the requested movie |
| Show Date | Identifies the requested show date |
| Show Time | Identifies the requested show time |
| Number of Tickets | Specifies the number of tickets requested |

**Logical Structure**

```text
Movie Ticket Request
        |
        +-- Movie Name
        |
        +-- Show Date
        |
        +-- Show Time
        |
        +-- Number of Tickets
```

These values establish the basic booking request before availability and booking decisions are performed.

---

## 7. Number of Tickets

**Property**: `NumberOfTickets`  
**Label**: Number of Tickets  
**Data Type**: Integer  

The `NumberOfTickets` property is a numeric property because it represents the number of tickets requested by the customer.

It is used by the availability validation logic and the Total Cost calculation.

**Used By**

```text
NumberOfTickets
       |
       +--> ValidateSufficientSeats
       |
       +--> CalculateTotalCost
```

---

## 8. Availability Data

The availability section contains the information required to determine whether the requested booking can proceed.

**Properties**

| Property | Purpose |
|---|---|
| Seat Availability Status | Represents the current availability result |
| Available Seats Count | Stores the number of available seats |

**Logical Structure**

```text
Availability
     |
     +-- Seat Availability Status
     |
     +-- Available Seats Count
```

The NIP project requires both Seat Availability Status and Available Seats Count to be captured during the Availability stage.

---

## 9. Available Seats Count

**Property**: `AvailableSeatsCount`  
**Label**: Available Seats Count  
**Data Type**: Integer  

The property represents the number of seats currently available for the selected show.

It is used as an input to the seat validation decision.

**Relationship to Ticket Quantity**

```text
Number of Tickets
        +
Available Seats Count
        |
        v
ValidateSufficientSeats
        |
        v
Seat Availability Status
```

The application therefore uses numeric data for both the requested quantity and available seat count.

---

## 10. Seat Availability Status

**Property**: `SeatAvailabilityStatus`  
**Label**: Seat Availability Status  

**Purpose**

The property represents the result of the availability validation.

It is used by subsequent booking logic, including the confirmation decision.

Conceptually:
- Available
- Limited
- Unavailable

The exact allowed values should remain synchronized with the values configured in the Pega application.

**Used By**

```text
Seat Availability Status
          |
          v
     ConfirmBooking
```

---

## 11. Pricing Data

The pricing section contains the information needed to calculate the booking cost.

**Properties**

| Property | Purpose |
|---|---|
| Ticket Price | Price of one ticket |
| Number of Tickets | Quantity requested |
| Total Cost | Calculated total booking amount |

**Formula**

`Total Cost = Ticket Price × Number of Tickets`

**Logical Structure**

```text
Ticket Price
     |
     +----------+
                |
                v
        CalculateTotalCost
                ^
                |
     +----------+
     |
Number of Tickets
     |
     v
Total Cost
```

The Total Cost value is calculated automatically rather than manually entered by the customer.

---

## 12. Total Cost

**Property**: `TotalCost`  
**Label**: Total Cost  

**Purpose**

Total Cost represents the total amount payable for the requested tickets.

It is derived from:
- Ticket Price
- Number of Tickets

**Formula**

`TotalCost = TicketPrice × NumberOfTickets`

**Example**

- **Ticket Price** = 250
- **Number of Tickets** = 4
- **Total Cost** = 250 × 4 = 1000

The NIP project requires Total Cost to be automatically updated and stored in the case.

---

## 13. Customer Confirmation Data

The customer confirmation section stores the customer's decision regarding the booking.

**Properties**

| Property | Purpose |
|---|---|
| Customer Confirmation | Stores the customer's confirmation/cancellation decision |
| Booking Status | Stores the resulting booking state |

**Logical Structure**

```text
Customer Confirmation
        |
        v
   ConfirmBooking
        |
        v
Booking Status
```

The customer reviews the booking details before providing the final confirmation.

---

## 14. Customer Confirmation

**Property**: `CustomerConfirmation`  
**Label**: Customer Confirmation  

**Purpose**

The property captures the customer's decision regarding the booking.

The configured decision logic uses values corresponding to:
- Approve
- Cancel

The property is used by the `ConfirmBooking` Decision Table.

---

## 15. Booking Status

**Property**: `BookingStatus`  

**Purpose**

The Booking Status represents the outcome of the booking decision.

The configured Decision Table uses outcomes including:
- Confirmed
- Cancelled
- Rejected
- Pending

The exact final values should match the values saved in the Pega application.

**Logical Flow**

```text
Customer Confirmation
        +
Seat Availability Status
        |
        v
   ConfirmBooking
        |
        v
   Booking Status
```

---

## 16. Booking Execution Data

After the customer confirms the booking, the case proceeds to Booking Execution.

The Booking Execution stage maintains the information required to complete the ticket booking.

**Properties**

| Property | Purpose |
|---|---|
| Seat Numbers | Stores the seats allocated to the booking |
| Ticket ID | Identifies the generated ticket |
| Booking Confirmation Status | Represents the final booking confirmation state |

**Logical Structure**

```text
Booking Execution
       |
       +-- Seat Numbers
       |
       +-- Ticket ID
       |
       +-- Booking Confirmation Status
```

The NIP requirements specifically call for Seat Numbers, Ticket ID, and Booking Confirmation Status to be maintained during Booking Execution.

---

## 17. Seat Numbers

**Purpose**

Seat Numbers identifies the seats allocated to the customer after a booking is confirmed.

The value is used in the final booking information and customer notification.

**Relationship**

```text
Confirmed Booking
       |
       v
Seat Allocation
       |
       v
Seat Numbers
```

---

## 18. Ticket ID

**Purpose**

Ticket ID provides an identifier for the completed movie ticket booking.

It allows the completed booking to be referenced after the booking process has finished.

**Relationship**

```text
Booking Execution
       |
       v
Ticket ID
       |
       v
Booking Confirmation
```

---

## 19. Booking Confirmation Status

**Purpose**

Booking Confirmation Status represents the final confirmation state of the ticket booking.

It is maintained during Booking Execution and contributes to the final booking outcome.

---

## 20. Show Type

**Property**: `ShowType`  

**Purpose**

Show Type determines how the booking is routed during Booking Execution.

The required routing is:

```text
Premium / Special Screening
        |
        v
PremiumShowQueue
```

and:

```text
Other Show Types
        |
        v
StandardShowQueue
```

**Logical Structure**

```text
Show Type
    |
    +--------------------+
    |                    |
Premium / Special      Other
    |                    |
    v                    v
PremiumShowQueue   StandardShowQueue
```

The NIP specification requires this routing to occur automatically.

---

## 21. Data Relationships

The main logical relationships are:

```text
Movie
  |
  | Movie Name
  v
Show
  |
  | Movie Name
  | Show Date
  | Show Time
  | Seat Capacity
  v
Movie Ticket Request
```

The case then adds booking-specific information:

```text
Movie Ticket Request
        |
        +-- Number of Tickets
        |
        +-- Ticket Price
        |
        +-- Total Cost
        |
        +-- Available Seats Count
        |
        +-- Seat Availability Status
        |
        +-- Customer Confirmation
        |
        +-- Booking Status
        |
        +-- Show Type
        |
        +-- Seat Numbers
        |
        +-- Ticket ID
        |
        +-- Booking Confirmation Status
```

---

## 22. Data Flow Through the Case

The data flows through the lifecycle as follows:

```text
Movie / Show Data
       |
       v
Movie Ticket Request
       |
       +--> Movie Name
       +--> Show Date
       +--> Show Time
       +--> Number of Tickets
       |
       v
Availability
       |
       +--> Available Seats Count
       +--> Seat Availability Status
       |
       v
Cost Calculation
       |
       +--> Ticket Price
       +--> Total Cost
       |
       v
Customer Review
       |
       +--> Customer Confirmation
       |
       v
Booking Decision
       |
       +--> Booking Status
       |
       v
Booking Execution
       |
       +--> Seat Numbers
       +--> Ticket ID
       +--> Booking Confirmation Status
       |
       v
Notification
```

---

## 23. Rule Dependencies

The main properties participate in the following rules.

**ValidateSufficientSeats**

```text
Number of Tickets
+
Available Seats Count
        |
        v
ValidateSufficientSeats
        |
        v
Seat Availability Status
```

**CalculateTotalCost**

```text
Ticket Price
+
Number of Tickets
        |
        v
CalculateTotalCost
        |
        v
Total Cost
```

**ConfirmBooking**

```text
Customer Confirmation
+
Seat Availability Status
        |
        v
ConfirmBooking
        |
        v
Booking Status
```

**Show Type Routing**

```text
Show Type
        |
        v
Routing Decision
        |
   +----+----+
   |         |
Premium    Other
   |         |
   v         v
Premium    Standard
Queue      Queue
```

---

## 24. Data Ownership

The data model separates reusable reference information from transaction-specific information.

**Reusable Data**

The following information belongs to reusable data objects:

```text
Movie
  ├── Movie Name
  └── Genre

Show
  ├── Movie Name
  ├── Show Date
  ├── Show Time
  └── Seat Capacity
```

**Case Data**

The following information belongs to the individual booking case:

```text
Movie Ticket Request
  ├── Movie Name
  ├── Show Date
  ├── Show Time
  ├── Number of Tickets
  ├── Ticket Price
  ├── Total Cost
  ├── Seat Availability Status
  ├── Available Seats Count
  ├── Customer Confirmation
  ├── Booking Status
  ├── Show Type
  ├── Seat Numbers
  ├── Ticket ID
  └── Booking Confirmation Status
```

This separation makes the application easier to maintain and allows Movie and Show information to be reused across multiple booking cases.

---

## 25. Data Validation

The data model uses appropriate data types for values that participate in numeric calculations.

In particular:

- **Number of Tickets** -> Integer
- **Available Seats Count** -> Integer

Numeric properties are important because they participate in the following calculations and decisions:

```text
Number of Tickets
        |
        +--> Seat availability validation
        |
        +--> Total Cost calculation

Available Seats Count
        |
        +--> Seat availability validation
```

The application should use numeric range configuration when numeric properties are evaluated using ranges in Decision Tables.

For example, a range such as `1 to 10` must be represented as a numeric range condition rather than treated as a literal integer string.

---

## 26. Data Model and NIP Requirements

| NIP Requirement | Data Model Component |
|---|---|
| Movie data object | Movie |
| Movie Name | Movie / Show / Case |
| Genre | Movie |
| Show data object | Show |
| Show Date | Show / Case |
| Show Time | Show / Case |
| Seat Capacity | Show |
| Number of Tickets | Case |
| Seat Availability Status | Case |
| Available Seats Count | Case |
| Ticket Price | Case |
| Total Cost | Case |
| Customer Confirmation | Case |
| Booking Status | Case |
| Seat Numbers | Case |
| Ticket ID | Case |
| Booking Confirmation Status | Case |
| Show Type | Case / Show-related booking information |

---

## 27. Data Model to User Story Mapping

| User Story | Data Model Components |
|---|---|
| US-001 — Submit Movie Ticket Request | Movie Name, Show Date, Show Time, Number of Tickets |
| US-002 — Check Show Availability | Seat Availability Status, Available Seats Count |
| US-003 — Calculate Booking Cost | Ticket Price, Number of Tickets, Total Cost |
| US-004 — Confirm Booking Request | Customer Confirmation, Booking Status |
| US-005 — Maintain Movie and Show Data | Movie, Show |
| US-006 — Review Booking Details | Movie Name, Show Timing, Number of Tickets, Total Cost |
| US-007 — Process Ticket Booking | Seat Numbers, Ticket ID, Booking Confirmation Status |
| US-008 — Notify Booking Confirmation | Case ID and booking data |
| US-009 — Define Booking SLA | Case-level SLA metadata |
| US-010 — Route Booking Request by Show Type | Show Type |

---

## 28. Example Booking Record

A completed booking case can conceptually contain:

- **Movie Name:** Example Movie
- **Show Date:** 2026-08-31
- **Show Time:** 18:30
- **Number of Tickets:** 3
- **Ticket Price:** 250
- **Total Cost:** 750
- **Available Seats Count:** 20
- **Seat Availability Status:** Available
- **Customer Confirmation:** Approve
- **Booking Status:** Confirmed
- **Show Type:** Standard
- **Seat Numbers:** A10, A11, A12
- **Ticket ID:** Generated Ticket ID
- **Booking Confirmation Status:** Confirmed

The actual values depend on the test data entered into the Pega application.

---

## 29. Data Model Design Principles

The application follows these data-modeling principles:

1. **Reusability:** Movie and Show information is modeled as reusable data rather than being treated only as temporary case information.
2. **Correct data types:** Numeric values such as Number of Tickets and Available Seats Count use Integer properties.
3. **Derived data:** Total Cost is calculated from existing case data instead of being manually entered.
4. **Separation of reference and transaction data:** Movie and Show represent reusable information, while the Movie Ticket Request contains booking-specific information.
5. **Traceability:** Ticket ID, Seat Numbers, Booking Status, and Booking Confirmation Status provide traceability for completed bookings.
6. **Rule-driven data processing:** Case properties are used as inputs and outputs for Pega business rules such as decision tables, declare expressions, and data transforms.

---

## 30. Final Data Model Summary

The Movie Ticket Booking application can be represented as:

```text
                         +---------+
                         |  Movie  |
                         +----+----+
                              |
                              | Movie Name
                              v
                         +---------+
                         |  Show   |
                         +----+----+
                              |
                    +---------+---------+
                    |                   |
              Show Details        Seat Capacity
                    |
                    v
             Movie Ticket Request
                    |
       +------------+------------+
       |            |            |
       v            v            v
   Request      Availability   Pricing
    Data           Data          Data
       |            |            |
       |            |            +--> Ticket Price
       |            |            +--> Total Cost
       |            |
       |            +--> Available Seats Count
       |            +--> Seat Availability Status
       |
       +--> Movie Name
       +--> Show Date
       +--> Show Time
       +--> Number of Tickets
                    |
                    v
                Approval
                    |
          +---------+---------+
          |                   |
       Customer           Booking Status
      Confirmation
          |
          v
       Booking Execution
          |
       +--+-----------+-------------+
       |              |             |
       v              v             v
 Seat Numbers      Ticket ID   Confirmation
                                 Status
                    |
                    v
              Show Type Routing
                 /         \
                v           v
        Premium Queue   Standard Queue
                    |
                    v
               Notification
                    |
                    v
                 Resolved
```

The resulting data model supports the complete Movie Ticket Request lifecycle while keeping reusable movie/show information separate from booking transaction data and providing the properties required by the NIP project specification.
