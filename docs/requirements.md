# Requirements

## 1. Document Purpose
This document defines the functional requirements for the **NIP-MovieTicket-RishvinReddy** application.

The application is a Pega-based **Movie Ticket Booking Management Application** for CineWave Entertainment.

The objective is to provide an end-to-end workflow for submitting, validating, confirming, processing, and tracking movie ticket bookings.

The requirements are based on the National Internship Program (NIP) Movie Ticket Booking Management Application project specification.

---

## 2. Project Overview

**Application**
```text
NIP-MovieTicket-RishvinReddy
```

**Platform**
Pega Platform

**Case Type**
Movie Ticket Request

**Business Organization**
CineWave Entertainment

**Project**
Movie Ticket Booking Management Application

---

## 3. Business Problem

CineWave Entertainment manages movie ticket bookings across multiple theatres and locations.

The existing booking and tracking process is handled manually through emails and offline systems. This creates:
- Delays in processing bookings
- Limited visibility into booking status
- Inefficient management of seating availability
- Inefficient booking tracking
- Difficulty communicating booking confirmations to customers

The application addresses these problems by providing a structured Pega case-management workflow for movie ticket booking.

---

## 4. Business Objectives

The application must support the following objectives:
1. Allow customers to request movie ticket bookings.
2. Enable staff to manage show details and seating availability.
3. Capture customer confirmations before final booking.
4. Process booking requests and track booking status.
5. Notify customers when a booking is confirmed.
6. Provide a structured case lifecycle from request creation to ticket confirmation.
7. Model reusable Movie and Show data.
8. Implement basic business logic and automation.
9. Ensure that booking requests follow the required processing sequence.

The NIP project specifically identifies case lifecycle design, user interactions, data structures, business logic, automation, and end-to-end booking flow as the primary project objectives.

---

## 5. Scope

**In Scope**
The application includes:
- Movie ticket request creation
- Movie and show information
- Show availability verification
- Seat availability status
- Available seat count
- Ticket price
- Automatic Total Cost calculation
- Customer booking confirmation
- Booking cancellation
- Booking status
- Seat allocation
- Ticket ID
- Booking confirmation status
- Customer notification
- Booking SLA
- Show Type based routing
- PremiumShowQueue
- StandardShowQueue
- Reusable Movie data object
- Reusable Show data object

**Out of Scope**
The NIP project does not require:
- Complex payment gateway integration
- Real-world theatre reservation APIs
- External ticketing platform integration
- Complex escalation workflows
- Advanced authentication integrations
- Production-scale theatre inventory synchronization

The implementation should remain focused on the requirements specified by the NIP project.

---

## 6. Actors and Personas

### 6.1 Customer
The Customer is responsible for:
- Creating a movie ticket request
- Providing movie and show information
- Providing the number of tickets
- Reviewing booking details
- Confirming the booking
- Cancelling the booking when required

The Customer is specifically required for the Approval stage.

### 6.2 Booking Agent
The Booking Agent is responsible for operational booking activities, including verification of show availability.

The NIP project identifies the Booking Agent persona for availability verification.

---

## 7. Case Type Requirements

The application must contain the following case type:
- `Movie Ticket Request`

The case type represents the complete movie ticket booking process.

Each case should represent an individual movie ticket booking request.

The case must support the lifecycle from initial request through availability verification, customer confirmation, booking execution, and resolution.

---

## 8. Case Lifecycle Requirements

The case lifecycle must include the required processing stages.

The required major stages are:
- Initial Request
- Availability
- Approval
- Booking Execution

The exact initial-stage label must match the name configured in the Pega application.

**Lifecycle Flow**

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
Resolution
```

The case must enforce the appropriate sequence so that booking processing does not occur before availability verification and customer confirmation.

---

## 9. US-001 — Submit Movie Ticket Request

**User Story**
A customer should be able to submit a movie ticket request in order to initiate the booking process.

**Functional Requirements**
The application must:
1. Provide a case type named Movie Ticket Request.
2. Provide an initial stage for booking request details.
3. Allow the Customer persona to enter:
    - Movie Name
    - Show Date
    - Show Time
    - Number of Tickets
4. Validate the required booking information.
5. Prevent incomplete or invalid requests from being submitted.
6. Associate the case with reusable Movie and Show data objects.

**Acceptance Criteria**
A booking request is successfully created when the required movie, show, and ticket information is provided and passes validation.

---

## 10. US-002 — Check Show Availability

**User Story**
The system or Booking Agent should be able to verify show availability in order to ensure seats are available for booking.

**Functional Requirements**
The application must:
1. Provide an Availability stage.
2. Allow availability to be verified.
3. Capture:
    - Seat Availability Status
    - Available Seats Count
4. Compare the requested ticket quantity against available seating.
5. Prevent booking from proceeding when sufficient seats are unavailable.

**Acceptance Criteria**
The case proceeds toward customer confirmation only when the availability requirements are satisfied.

---

## 11. US-003 — Calculate Booking Cost

**User Story**
The system should calculate the total booking cost in order to provide pricing details to the customer.

**Functional Requirements**
The application must:
1. Capture Ticket Price.
2. Capture Number of Tickets.
3. Calculate Total Cost automatically.
4. Store the calculated Total Cost in the case.
5. Make Total Cost available for customer review.

**Formula**
`Total Cost = Ticket Price × Number of Tickets`

**Acceptance Criteria**
When Ticket Price or Number of Tickets changes, the Total Cost should reflect the current calculation.

---

## 12. US-004 — Confirm Booking Request

**User Story**
A customer should be able to confirm or cancel the booking request in order to proceed with or stop the booking.

**Functional Requirements**
The application must:
1. Provide an Approval stage.
2. Assign the approval step to the Customer persona.
3. Capture the customer's confirmation decision.
4. Maintain Booking Status.
5. Allow confirmed bookings to proceed to ticket processing.
6. Resolve cancelled requests appropriately without further booking processing.

**Decision Outcomes**
The booking decision may result in states such as:
- Confirmed
- Cancelled
- Rejected
- Pending

The exact final values must match the configured Pega application.

**Acceptance Criteria**
A confirmed request proceeds to Booking Execution.
A cancelled request does not proceed to final booking processing.

---

## 13. US-005 — Maintain Movie and Show Data

**User Story**
The system should maintain movie and show details in order to enable reuse and manage show schedules effectively.

**Functional Requirements**
The application must provide two reusable data objects:
- Movie
- Show

**Movie Data Object**
The Movie object must contain:
- Movie Name
- Genre

**Show Data Object**
The Show object must contain:
- Movie Name
- Show Date
- Show Time
- Seat Capacity

**Association**
The Movie and Show data objects must be associated with the Movie Ticket Request case type.

**Acceptance Criteria**
Movie and Show information can be reused across multiple Movie Ticket Request cases.

---

## 14. US-006 — Review Booking Details

**User Story**
A customer should be able to review booking details in order to make an informed confirmation decision.

**Functional Requirements**
The confirmation step must clearly display:
- Movie Name
- Show Timing
- Number of Tickets
- Total Cost

The information should be presented in a structured and readable format to the Customer persona.

**Acceptance Criteria**
Before confirming the booking, the Customer can clearly review the important booking and pricing information.

---

## 15. US-007 — Process Ticket Booking

**User Story**
The system should process ticket bookings in order to finalise seat allocation and ticket generation.

**Functional Requirements**
The application must provide a:
`Booking Execution`
stage.

The stage must support:
- Seat allocation
- Booking status update
- Booking Confirmation Status
- Seat Numbers
- Ticket ID

**Acceptance Criteria**
After a booking is confirmed, the application processes the booking and maintains the required ticket and seat information.

---

## 16. US-008 — Notify Booking Confirmation

**User Story**
A customer should receive a notification when the ticket booking is confirmed in order to stay informed.

**Functional Requirements**
The application must provide a Correspondence mechanism.

The notification should be triggered upon successful booking completion and case resolution.

The notification must include:
- Case ID
- Movie Name
- Show Date and Time
- Seat Numbers
- Total Cost

**Example Notification Structure**
```text
Subject: Movie Ticket Booking Confirmed – [Case ID]
Dear [Customer Name],
Your movie ticket booking has been successfully confirmed.
Booking Details:
- Case ID
- Movie Name
- Show Date & Time
- Number of Tickets
- Seat Numbers
- Total Cost
Regards,
CineWave Entertainment – Booking Support Team
```

The final notification content should reflect the values available in the configured case.

---

## 17. US-009 — Define Booking SLA

**User Story**
The system should define a service-level agreement for each booking request so that bookings are confirmed within an expected timeframe.

**Functional Requirements**
The SLA must be configured on the `Movie Ticket Request` case type.

**SLA Targets**

| SLA Component | Requirement |
|---|---|
| Goal | 1 day |
| Deadline | 2 days |

**Goal Behavior**
When the Goal is missed: Case is flagged as approaching deadline

**Deadline Behavior**
When the Deadline is missed: Case priority is automatically increased

**Additional Requirement**
No complex escalation path is required.

The implementation should use the appropriate urgency settings for the Goal and Deadline.

---

## 18. US-010 — Route Booking Request by Show Type

**User Story**
The system should automatically route booking requests to the correct work queue based on Show Type so that the appropriate team processes each booking.

**Functional Requirements**
The application must use the Show Type property to determine routing.

**Required Queues**
- PremiumShowQueue
- StandardShowQueue

**Routing Logic**

```text
Show Type
    |
    +-----------------------+
    |                       |
Premium / Special         Other
    |                       |
    v                       v
PremiumShowQueue      StandardShowQueue
```

**PremiumShowQueue**
Used for premium or special screenings.

**StandardShowQueue**
Used for all other shows.

**Implementation Options**
The routing may be implemented using:
- A When rule
- A Decision Table

The routing must occur automatically without manual intervention.

---

## 19. Data Requirements

The following data must be supported by the application.

**Movie**

| Property | Requirement |
|---|---|
| Movie Name | Required |
| Genre | Required |

**Show**

| Property | Requirement |
|---|---|
| Movie Name | Required |
| Show Date | Required |
| Show Time | Required |
| Seat Capacity | Required |

**Movie Ticket Request**

| Property | Purpose |
|---|---|
| Movie Name | Requested movie |
| Show Date | Requested show date |
| Show Time | Requested show time |
| Number of Tickets | Requested ticket quantity |
| Ticket Price | Price per ticket |
| Total Cost | Calculated booking cost |
| Seat Availability Status | Availability result |
| Available Seats Count | Available seating |
| Customer Confirmation | Customer decision |
| Booking Status | Booking outcome |
| Show Type | Routing decision |
| Seat Numbers | Allocated seats |
| Ticket ID | Booking identifier |
| Booking Confirmation Status | Final booking state |

---

## 20. Business Logic Requirements

### 20.1 Seat Availability
The application must evaluate whether sufficient seats are available.

```text
Requested Tickets
        |
        v
Available Seats Count
        |
        v
Seat Availability Decision
```

The booking must not proceed to final ticket processing when sufficient seats are unavailable.

### 20.2 Total Cost
The application must calculate:
`Total Cost = Ticket Price × Number of Tickets`
The calculation must be automatic.

### 20.3 Booking Confirmation
The application must evaluate:
`Customer Confirmation` + `Seat Availability Status`
to determine the booking outcome.

### 20.4 Show Type Routing
The application must evaluate:
`Show Type`
and route the case to the appropriate work queue.

---

## 21. Validation Requirements

The application must validate the information necessary to create and process a booking request.

At minimum:
- Movie Name must be provided.
- Show Date must be provided.
- Show Time must be provided.
- Number of Tickets must be provided.
- Number of Tickets must be treated as a numeric value.
- Availability information must be available before final booking.
- Booking should not proceed when sufficient seats are unavailable.

Numeric ranges used in decision logic must be configured as numeric ranges rather than literal text values.

For example: `1 to 10` must be interpreted as a range with numeric boundaries rather than as a single integer value.

---

## 22. Workflow Requirements

The workflow must enforce the following sequence:

```text
Request Creation
      |
      v
Availability Verification
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

A booking cannot reach final processing without passing the required availability and confirmation decisions.

---

## 23. Non-Functional Requirements

The NIP project is primarily focused on functional Pega configuration. The following non-functional expectations apply to the implementation.

**Usability**
The booking interface should clearly present the required information to the Customer.

**Maintainability**
Business logic should be implemented using configurable Pega rules where appropriate.

**Reusability**
Movie and Show should be modeled as reusable data objects.

**Traceability**
The case should maintain important booking information such as:
- Booking Status
- Ticket ID
- Seat Numbers
- Total Cost
- Booking Confirmation Status

**Automation**
The application should automate:
- Cost calculation
- Availability decisions
- Booking outcomes
- Show Type routing
- SLA behavior
- Booking confirmation notification

---

## 24. Acceptance Criteria Summary

The application is functionally aligned with the project requirements when all ten user stories are implemented.

| ID | Requirement | Expected Result |
|---|---|---|
| US-001 | Submit Movie Ticket Request | Customer can create a validated booking request |
| US-002 | Check Show Availability | Availability and seat count are captured and evaluated |
| US-003 | Calculate Booking Cost | Total Cost is calculated automatically |
| US-004 | Confirm Booking Request | Customer can confirm/cancel and Booking Status is maintained |
| US-005 | Maintain Movie and Show Data | Reusable Movie and Show objects are available |
| US-006 | Review Booking Details | Customer can review key booking information |
| US-007 | Process Ticket Booking | Seats and ticket information are maintained |
| US-008 | Notify Booking Confirmation | Customer receives booking confirmation |
| US-009 | Define Booking SLA | Goal = 1 day and Deadline = 2 days |
| US-010 | Route by Show Type | Cases are automatically routed to the correct queue |

---

## 25. Requirements Traceability

The relationship between requirements and implementation components is summarized below.

| Requirement Area | Pega Component |
|---|---|
| Request submission | Movie Ticket Request case type |
| Request validation | Case properties and validation |
| Availability | Availability stage |
| Seat validation | ValidateSufficientSeats Decision Table |
| Available seat count | AvailableSeatsCount |
| Cost calculation | CalculateTotalCost |
| Customer confirmation | Approval stage |
| Booking outcome | ConfirmBooking Decision Table |
| Movie information | Movie data object |
| Show information | Show data object |
| Booking review | Review Booking Details |
| Ticket processing | Booking Execution stage |
| Seat allocation | Booking Execution |
| Ticket identification | Ticket ID |
| Booking confirmation | Booking Confirmation Status |
| Customer notification | Correspondence |
| SLA | Case-level SLA |
| Show Type routing | When rule or Decision Table |
| Premium processing | PremiumShowQueue |
| Standard processing | StandardShowQueue |

---

## 26. Requirement Dependencies

The major dependencies between requirements are:

```text
US-001
  |
  v
US-002
  |
  +----------------+
  |                |
  v                v
US-003          Availability
  |                |
  +-------+--------+
          |
          v
       US-006
          |
          v
       US-004
          |
     +----+----+
     |         |
 Cancel      Confirm
     |         |
     v         v
 Resolve   US-007
              |
              +--> US-010
              |
              v
           US-008
              |
              v
           Resolve
```

US-009 applies to the Movie Ticket Request case throughout the lifecycle.
US-005 provides reusable Movie and Show data used by the booking case.

---

## 27. Project Completion Definition

The Movie Ticket Booking application is considered complete when:

1. The Movie Ticket Request case type exists.
2. The required case lifecycle is configured.
3. Movie and Show data objects exist and are associated with the case.
4. Customers can submit valid movie ticket requests.
5. Show availability can be checked.
6. Available Seats Count and Seat Availability Status are maintained.
7. Total Cost is calculated automatically.
8. Customers can review booking details.
9. Customers can confirm or cancel bookings.
10. Confirmed bookings proceed to Booking Execution.
11. Seat Numbers, Ticket ID, and Booking Confirmation Status are maintained.
12. Booking confirmation notification is configured.
13. The SLA has a 1-day Goal and 2-day Deadline.
14. SLA deadline breach increases case priority.
15. Show Type automatically routes cases to the appropriate work queue.
16. All ten NIP Movie Ticket Booking user stories are addressed.
17. The final implementation can be demonstrated through the Pega case lifecycle and configuration evidence.

---

## 28. Requirements Summary

The application provides an end-to-end movie ticket booking process:

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
Review Booking Details
   |
   v
Confirm / Cancel
   |
   +------------------+
   |                  |
 Cancel             Confirm
   |                  |
   v                  v
Resolve        Booking Execution
                      |
                      +--> Seat Allocation
                      |
                      +--> Ticket ID
                      |
                      +--> Booking Status
                      |
                      +--> Show Type Routing
                              |
                       +------+------+
                       |             |
                       v             v
                PremiumShowQueue  StandardShowQueue
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

The requirements establish the functional scope for the `NIP-MovieTicket-RishvinReddy` application and provide the basis for implementation, testing, documentation, and final NIP submission.
