# Troubleshooting

## 1. Overview
This document records the significant configuration and testing issues encountered while developing the `NIP-MovieTicket-RishvinReddy` Movie Ticket Booking application in Pega.

The purpose of this document is to provide:
- A record of observed problems
- The symptoms of each problem
- The investigation performed
- The identified cause
- The corrective action
- The validation performed after the correction

The troubleshooting process focuses on issues encountered during Decision Table configuration, numeric range handling, case processing, and Pega UI authoring.

---

## 2. Troubleshooting Approach
The general troubleshooting process used during development was:

```text
Observe Error
     |
     v
Identify Failing Case Step
     |
     v
Inspect Pega Rule Configuration
     |
     v
Check Related Properties
     |
     v
Identify Root Cause
     |
     v
Apply Minimal Configuration Change
     |
     v
Save Rule
     |
     v
Run Test Case
     |
     v
Verify Result
```

The goal was to change only the configuration responsible for the observed behavior rather than modifying unrelated properties or rules.

---

## 3. Issue 1 — NumberFormatException for "1 to 10"

**Status**
Resolved

**Error**
During Movie Ticket Request processing, the application produced:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```
The error occurred while a decision rule was being invoked.

The trace/error information included:
`LegacyDecisionAspectInvokableRuleContainer.invoke`
and:
`java.lang.NumberFormatException`

### 3.1 Symptoms
The case failed during processing even though the `Number of Tickets` property was configured as an Integer.

The problematic value shown in the exception was:
`1 to 10`

This indicated that Pega was attempting to process the complete range expression as a numeric value rather than evaluating it as a numeric range.

---

## 4. Initial Investigation
Several possible sources were investigated.

### 4.1 Number of Tickets Property
The `Number of Tickets` property was inspected.

The property was confirmed as:
- **Property:** `NumberOfTickets`
- **Type:** `Integer`

Therefore, changing the property type was not appropriate.
The property itself was not the source of the `"1 to 10"` text.

### 4.2 Available Seats Count Property
The `Available Seats Count` property was also inspected.

It was confirmed as:
- **Property:** `AvailableSeatsCount`
- **Type:** `Integer`

No `"1 to 10"` value was found in the property configuration.
Therefore, this property was also left unchanged.

---

## 5. Decision Table Investigation
The investigation then focused on the Decision Tables used by the Availability process.

The relevant decision logic was:

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

The `ValidateSufficientSeats` Decision Table contained range-based conditions for `Number of Tickets` and `Available Seats Count`.

The relevant conditions included:
- `1 to 10`
- `11 to 50`
- `51 or more`

and available-seat ranges such as:
- `10 or more`
- `5 to 9`
- `Less than 5`

---

## 6. Root Cause
The root cause was the configuration of numeric range conditions inside the `ValidateSufficientSeats` Decision Table.

The values were being interpreted incorrectly when the Decision Table attempted to evaluate a numeric property.

For example:
`1 to 10`
was being treated as a value rather than as:
`Minimum = 1`
`Maximum = 10`

This resulted in Pega attempting to parse:
`"1 to 10"`
as an integer.

That produced:
`NumberFormatException`

---

## 7. Resolution
The `Number of Tickets` condition in the Decision Table was configured to use the Decision Table's numeric range functionality.

Instead of treating the range as a literal text value:
`1 to 10`
the condition was configured using numeric range boundaries.

Conceptually:
`Number of Tickets >= 1`
AND
`Number of Tickets <= 10`

The same range-based configuration approach was applied to the available-seat conditions where required.

---

## 8. Result After Correction
After the correction, Pega displayed the numeric range conditions using numeric boundaries.

The first condition was represented conceptually as:
`>= 1`
`<= 10`
rather than attempting to parse the complete string:
`1 to 10`

The Decision Table could therefore evaluate the numeric property correctly.

---

## 9. Important Configuration Lesson
When an Integer property is used in a Pega Decision Table, a range condition should be configured as a numeric range.

For example:

**Correct:**
`Minimum = 1`
`Maximum = 10`

rather than relying on a literal string representation:

**Incorrect:**
`"1 to 10"`

This is particularly important for properties such as:
- Number of Tickets
- Available Seats Count

because both are numeric values.

---

## 10. Issue 2 — Total Cost Used as Decision Table Condition

**Status**
Resolved

During the development of the `ConfirmBooking` Decision Table, `Total Cost` was initially present as a condition column.

The table contained a condition similar to:
```text
Customer    Seat Availability    Total Cost    Return
```
with `Total Cost` containing a value of:
`0`

### 10.1 Investigation
Total Cost is a calculated booking value derived from:
`Ticket Price × Number of Tickets`

It is therefore a calculated case value rather than a required condition for the current booking confirmation logic.

The confirmation decision only required:
`Customer Confirmation` + `Seat Availability Status`

### 10.2 Resolution
The unnecessary `Total Cost` condition column was removed from the `ConfirmBooking` Decision Table.

The resulting decision structure uses:
```text
Customer    Seat Availability    Return
```

The intended logic is:
- Approve + Available → Confirmed
- Cancel + Available → Cancelled
- Approve + Unavailable → Rejected
- Otherwise → Pending

---

## 11. Issue 3 — Decision Table Logic Conflicts

**Status**
Configuration corrected

After modifying the `ConfirmBooking` Decision Table, multiple duplicate decision rows were visible.

The table contained repeated combinations such as:
- Approve + Available
- Approve + Unavailable
- Cancel + Available
- Cancel + Unavailable

The application reported multiple logic conflicts.

### 11.1 Cause
The Decision Table contained duplicate condition combinations.

For example:
`Approve + Available`
appeared more than once.

Multiple rows matching the same input combination can create ambiguous decision logic.

### 11.2 Intended Configuration
The table should contain one row for each required combination:
- Approve + Available
- Cancel + Available
- Approve + Unavailable
- Otherwise

The duplicate combinations should not be retained in the final Decision Table configuration.

### 11.3 Result
The Decision Table was simplified so that each required combination has a single intended outcome.

This makes the decision logic deterministic and easier to maintain.

---

## 12. Issue 4 — Unable to Delete Decision Table Rows

**Status**
Investigated

During configuration, duplicate Decision Table rows could not be removed using normal keyboard deletion.

The table UI did not respond as expected when attempting to delete rows directly.

### 12.1 Investigation
The issue was treated as a Decision Table UI/configuration problem rather than a property-definition problem.

The correct approach is to select the row/cell using the Decision Table selection controls and then use the available row operation.

If the row controls remain disabled, the Decision Table should be reviewed for its current editing state and conflicts before attempting further changes.

### 12.2 Lesson
Do not repeatedly modify unrelated properties when a Decision Table editing operation is not working.

First confirm:
1. The correct row is selected.
2. The Decision Table is in edit mode.
3. The relevant row operation is enabled.
4. The table does not have an editing/conflict state preventing the operation.

---

## 13. Issue 5 — UI Authoring Minified React Error #130

**Status**
Investigated

While configuring the `Submit Request` user action, Pega displayed:
```text
Error in UI Authoring
Minified React error #130
```
This occurred inside the UI authoring/configuration experience.

### 13.1 Important Distinction
This error is different from:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```
The `NumberFormatException` occurred during decision-rule processing.

The React error appeared while configuring the user action UI.

Therefore, the two errors should not be treated as the same defect.

### 13.2 Troubleshooting Approach
The first action was to exit the error dialog and return to the `Submit Request` configuration.

The relevant configuration areas to inspect are:
- User Action configuration
- Pre/Post Processing
- Conditions
- View/UI configuration
- Referenced fields or actions

The error should be investigated within the user-action configuration rather than by changing unrelated business rules.

---

## 14. Issue 6 — Decision Table Range Configuration

**Status**
Resolved

The `ValidateSufficientSeats` Decision Table originally used human-readable range expressions.

Examples:
- 1 to 10
- 11 to 50
- 51 or more

These expressions are useful from a business perspective, but the Decision Table must know that the underlying property is numeric.

The corrected configuration uses numeric range functionality.

**Corrected Logic**

*Number of Tickets*
- `1 <= Number of Tickets <= 10`
- `11 <= Number of Tickets <= 50`
- `Number of Tickets >= 51`

*Available Seats*
Conceptually:
- `Available Seats Count >= 10`
- `5 <= Available Seats Count <= 9`
- `Available Seats Count < 5`

The exact boundaries shown in the saved Pega Decision Table should remain the source of truth.

---

## 15. Issue 7 — Verifying Integer Property Types

**Status**
Verified

Two important numeric properties were inspected.

**Number of Tickets**
- Property: `NumberOfTickets`
- Type: `Integer`

**Available Seats Count**
- Property: `AvailableSeatsCount`
- Type: `Integer`

These types are appropriate because the values represent counts.
No property-type conversion was required.

---

## 16. Issue 8 — Avoiding Incorrect Property Changes

During troubleshooting, it was important to distinguish between:
`Property Type`
and:
`Decision Table Condition Configuration`

An Integer property can be correctly defined while a Decision Table still has an incorrectly configured range condition.

Therefore, changing:
`Integer`
to:
`Text`
or changing the property definition unnecessarily would not be an appropriate solution.

The correct fix was applied at the Decision Table range configuration level.

---

## 17. Troubleshooting Decision Tree

When a Decision Table produces a numeric parsing error, use the following process:

```text
Decision Table Error
        |
        v
Read Exact Exception
        |
        v
Is it NumberFormatException?
        |
       Yes
        |
        v
Identify Input String
        |
        v
Is it a range such as "1 to 10"?
        |
       Yes
        |
        v
Check Property Data Type
        |
        v
Is Property Numeric?
        |
       Yes
        |
        v
Check Decision Table Range Configuration
        |
        v
Enable / Configure Numeric Range
        |
        v
Save
        |
        v
Retest
```

---

## 18. Troubleshooting Checklist

**Decision Tables**
- Confirm the correct Decision Table is being executed.
- Check all condition columns.
- Check for duplicate condition combinations.
- Check for overlapping ranges.
- Check numeric range configuration.
- Check the property type used by each condition.
- Check the expected return values.
- Save the Decision Table after changes.
- Retest using a new case where appropriate.

**Numeric Properties**
- Confirm `Number of Tickets` is `Integer`.
- Confirm `Available Seats Count` is `Integer`.
- Do not use numeric range strings as literal integer values.
- Use Pega's numeric range/operator configuration.

**Case Processing**
- Confirm the correct stage is being executed.
- Check the user action configuration.
- Check conditions and transitions.
- Check data transforms.
- Check decision rules invoked by the stage.

**UI Authoring**
- Determine whether the error occurs during configuration or runtime.
- If it occurs in UI Authoring, inspect the user action/view configuration.
- Do not assume a UI Authoring error is a business-rule error.

---

## 19. Testing Strategy After Fixes

After correcting a business rule, the application should be tested from a clean `Movie Ticket Request` case.

A basic test should include:
- **Movie Name:** Valid movie
- **Show Date:** Valid date
- **Show Time:** Valid show
- **Number of Tickets:** 2

The test should verify:
1. The case can be created.
2. The Availability stage is reached.
3. Available Seats Count is available.
4. Seat Availability Status is calculated correctly.
5. Total Cost is calculated.
6. Booking details can be reviewed.
7. Customer confirmation can be submitted.
8. Confirmed bookings proceed to Booking Execution.
9. Ticket information is maintained.
10. The case can reach the expected resolution.

---

## 20. Regression Test Cases

**Test Case 1 — Low Ticket Quantity**
- Number of Tickets = 1
- Expected: Numeric range is evaluated correctly.

**Test Case 2 — Range Boundary**
- Number of Tickets = 10
- Expected: Value belongs to the 1–10 range.

**Test Case 3 — Second Range**
- Number of Tickets = 11
- Expected: Value belongs to the 11–50 range.

**Test Case 4 — Second Range Upper Boundary**
- Number of Tickets = 50
- Expected: Value belongs to the 11–50 range.

**Test Case 5 — Third Range**
- Number of Tickets = 51
- Expected: Value belongs to the 51-or-more range.

**Test Case 6 — Booking Confirmation**
- Customer Confirmation = Approve
- Seat Availability Status = Available
- Expected: Booking Status = Confirmed

**Test Case 7 — Booking Cancellation**
- Customer Confirmation = Cancel
- Seat Availability Status = Available
- Expected: Booking Status = Cancelled

**Test Case 8 — Insufficient Seats**
- Customer Confirmation = Approve
- Seat Availability Status = Unavailable
- Expected: Booking Status = Rejected

The exact expected values must match the final saved Decision Table.

---

## 21. Evidence and Verification

Troubleshooting changes should be verified using both configuration evidence and runtime behavior.

Recommended evidence includes:

**Configuration Evidence**
- Decision Table configuration
- Numeric range configuration
- Property type configuration
- Case stage configuration
- User action configuration

**Runtime Evidence**
- Successful case creation
- Availability result
- Calculated Total Cost
- Customer confirmation
- Booking execution
- Ticket ID
- Seat Numbers
- Booking status
- Final notification
- Case resolution

---

## 22. Lessons Learned

The development process produced several important configuration lessons.

**1. Read the exact exception**
The value `"1 to 10"` was a strong indicator that a range was being interpreted incorrectly.

**2. Verify the property type before changing it**
`NumberOfTickets` and `AvailableSeatsCount` were correctly configured as Integer properties.

**3. Fix the rule at the correct layer**
The numeric range issue belonged to the Decision Table condition configuration rather than the property definition.

**4. Avoid unnecessary rule changes**
Removing unrelated properties or changing data types can introduce additional problems.

**5. Keep Decision Tables deterministic**
Duplicate or overlapping conditions can create logic conflicts.

**6. Separate runtime errors from authoring errors**
The NumberFormatException and UI Authoring React error occurred in different contexts and should be investigated independently.

---

## 23. Final Troubleshooting Status

| Issue | Status | Resolution |
|---|---|---|
| NumberFormatException for "1 to 10" | Resolved | Configured numeric ranges correctly |
| Total Cost as unnecessary Decision Table condition | Resolved | Removed condition column |
| Duplicate Decision Table rows | Corrected | Reduced to intended decision combinations |
| Decision Table logic conflicts | Corrected | Removed duplicate/ambiguous conditions |
| Unable to delete rows | Investigated | Used Decision Table editing/selection controls |
| UI Authoring React error #130 | Investigated | Separated from runtime NumberFormatException |
| Number of Tickets property type | Verified | Confirmed Integer |
| Available Seats Count property type | Verified | Confirmed Integer |

---

## 24. Final Troubleshooting Summary

The most significant development issue was the numeric range parsing error in the `ValidateSufficientSeats` Decision Table.

The problem was identified through the exact exception:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The related properties were inspected and confirmed as Integer types.
The investigation then moved to the Decision Table configuration, where numeric ranges were being interpreted incorrectly.

The correction was to configure the ranges using Pega's numeric range functionality.

The final troubleshooting approach is:

```text
Exact Error
    ↓
Failing Rule
    ↓
Related Property
    ↓
Data Type
    ↓
Rule Configuration
    ↓
Minimal Fix
    ↓
Save
    ↓
Regression Test
```

This approach provides a repeatable method for diagnosing similar configuration issues in the application.
