# Defect: NumberFormatException for Numeric Range

## 1. Defect Information
| Field | Details |
|---|---|
| Defect ID | DEF-001 |
| Title | NumberFormatException when evaluating numeric range |
| Application | NIP-MovieTicket-RishvinReddy |
| Case Type | Movie Ticket Request |
| Severity | High |
| Priority | High |
| Status | Resolved |
| Component | Availability / ValidateSufficientSeats |
| Rule Type | Decision Table |
| Rule | `ValidateSufficientSeats` |
| Affected Property | Number of Tickets |
| Property Type | Integer |
| Error Type | `java.lang.NumberFormatException` |

---

## 2. Summary
The `Movie Ticket Request` case encountered a runtime `NumberFormatException` while processing the Availability logic.

The exception indicated that Pega attempted to interpret the range value:
```text
1 to 10
```
as a numeric value.

The resulting exception was:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The issue was associated with the numeric range configuration used by the `ValidateSufficientSeats` Decision Table.

---

## 3. Environment
The defect occurred in the Pega application:
`NIP-MovieTicket-RishvinReddy`

The affected case type was:
`Movie Ticket Request`

The issue occurred while processing the Availability portion of the case lifecycle.

The relevant flow was:
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

---

## 4. Expected Behavior
The application should evaluate the number of tickets as a numeric value and correctly determine the applicable Decision Table range.

For example:
`Number of Tickets = 5`

should satisfy the numeric range:
`1 <= Number of Tickets <= 10`

The case should continue through the availability process without throwing a Java numeric parsing exception.

---

## 5. Actual Behavior
Instead of evaluating the range correctly, the application produced:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The case processing failed when the decision logic attempted to evaluate the numeric range.

---

## 6. Error Message
The observed error was:
```text
java.lang.NumberFormatException:
For input string: "1 to 10"
```

The trace also identified the decision-rule invocation path, including:
`LegacyDecisionAspectInvokableRuleContainer.invoke`

This indicated that the failure occurred while the Decision Table logic was being invoked.

---

## 7. Affected Decision Table

**Rule**
`ValidateSufficientSeats`

**Rule Type**
Decision Table

**Purpose**
The rule determines seat availability based on the requested number of tickets and available seating information.

The relevant inputs are:
- `Number of Tickets`
- `Available Seats Count`

The result is used to determine:
`Seat Availability Status`

---

## 8. Original Range Configuration
The Decision Table contained ticket quantity ranges such as:
- `1 to 10`
- `11 to 50`
- `51 or more`

The intention of these values was to represent numeric ranges.

For example:
`1 to 10`
was intended to mean:
`Number of Tickets >= 1`
AND
`Number of Tickets <= 10`

However, the runtime error demonstrated that the range was being interpreted as a value that Pega attempted to parse numerically.

---

## 9. Property Investigation
The `Number of Tickets` property was inspected during troubleshooting.

- **Property:** `NumberOfTickets`
- **Display Label:** `Number of Tickets`
- **Data Type:** `Integer`

The property type was therefore appropriate for the business requirement because the number of tickets is a whole-number quantity.

The property type itself was not changed.

---

## 10. Related Property Investigation
The `Available Seats Count` property was also inspected because it participates in the same availability decision.

- **Property:** `AvailableSeatsCount`
- **Display Label:** `Available Seats Count`
- **Data Type:** `Integer`

This property was also correctly configured as a numeric value.
No unnecessary property-type conversion was performed.

---

## 11. Root Cause
The root cause was the handling of the numeric range in the `ValidateSufficientSeats` Decision Table.

The range:
`1 to 10`
was not being interpreted as a numeric range at runtime.

Instead, Pega attempted to process the complete expression as a numeric value.

Conceptually, the failing behavior was equivalent to:
`Integer("1 to 10")`

which is invalid and results in:
`NumberFormatException`

The problem therefore existed in the Decision Table range configuration, not in the `Integer` data type of the `NumberOfTickets` property.

---

## 12. Root Cause Analysis
The investigation followed this sequence:

```text
Runtime Error
     |
     v
NumberFormatException
     |
     v
Input string = "1 to 10"
     |
     v
Identify rule using numeric ranges
     |
     v
ValidateSufficientSeats
     |
     v
Inspect Number of Tickets
     |
     v
Property Type = Integer
     |
     v
Inspect Decision Table condition
     |
     v
Numeric range configuration identified
```

This established that changing the property type would not be the appropriate solution.

---

## 13. Resolution
The Decision Table range conditions were corrected so that Pega evaluates them as numeric ranges.

Instead of treating:
`1 to 10`
as one numeric value, the intended logic is represented through numeric boundaries:
`Number of Tickets >= 1`
AND
`Number of Tickets <= 10`

Similarly, the other numeric ranges should use appropriate numeric range/operator configuration.

---

## 14. Corrected Range Logic
The intended ticket quantity ranges are:

**Range 1**
`1 <= Number of Tickets <= 10`

**Range 2**
`11 <= Number of Tickets <= 50`

**Range 3**
`Number of Tickets >= 51`

These represent the business meaning of the original human-readable ranges without requiring Pega to parse the complete string as an integer.

---

## 15. Available Seats Conditions
The Decision Table also evaluates `Available Seats Count`.

The available-seat conditions should likewise be represented using numeric operators/ranges.

Conceptually:
- `Available Seats Count >= 10`
- `5 <= Available Seats Count <= 9`
- `Available Seats Count < 5`

The exact final boundaries must match the saved Decision Table configuration in the Pega application.

---

## 16. Why the Property Type Was Not Changed
An initial hypothesis was that the error might be caused by the `Number of Tickets` property being configured as Text.

After inspection, the property was confirmed to be:
`Integer`

Changing it to another type would therefore have been unnecessary and could have introduced additional problems.

The correct approach was:
`Correct Property Type` + `Correct Decision Table Range Configuration`

rather than changing the property's data type.

---

## 17. Corrective Action
The following corrective actions were performed:

1. Identified the exact exception.
2. Identified `"1 to 10"` as the input causing numeric parsing failure.
3. Located the `ValidateSufficientSeats` Decision Table.
4. Inspected the `Number of Tickets` property.
5. Confirmed `Number of Tickets` is an `Integer`.
6. Inspected the Decision Table range configuration.
7. Corrected the numeric range configuration.
8. Reviewed the related `Available Seats Count` conditions.
9. Saved the updated Decision Table.
10. Retested the affected case flow.

---

## 18. Verification
After applying the correction, the Decision Table was expected to evaluate ticket quantities as numeric values rather than attempting to parse the complete range expression.

The following boundary values should be used for regression testing:

| Test | Number of Tickets | Expected Range |
|---|---|---|
| Lower boundary | 1 | 1–10 |
| Normal value | 5 | 1–10 |
| Upper boundary | 10 | 1–10 |
| Second range lower boundary | 11 | 11–50 |
| Normal second-range value | 25 | 11–50 |
| Second range upper boundary | 50 | 11–50 |
| Third range lower boundary | 51 | 51+ |

The case should process the Decision Table without producing a `NumberFormatException`.

---

## 19. Regression Testing
The following scenarios should be verified after the fix.

**Test 1 — One Ticket**
- `Number of Tickets = 1`
- **Expected:** Decision Table evaluates successfully.

**Test 2 — Ten Tickets**
- `Number of Tickets = 10`
- **Expected:** Value is evaluated within the 1–10 range.

**Test 3 — Eleven Tickets**
- `Number of Tickets = 11`
- **Expected:** Value is evaluated within the 11–50 range.

**Test 4 — Fifty Tickets**
- `Number of Tickets = 50`
- **Expected:** Value is evaluated within the 11–50 range.

**Test 5 — Fifty-One Tickets**
- `Number of Tickets = 51`
- **Expected:** Value is evaluated within the 51-or-more range.

---

## 20. Related Booking Logic
The corrected availability decision feeds into the rest of the booking lifecycle.

```text
Number of Tickets
        |
        v
ValidateSufficientSeats
        |
        v
Seat Availability Status
        |
        v
Customer Review
        |
        v
ConfirmBooking
        |
        v
Booking Execution
```

Therefore, correcting the Decision Table is important because availability validation occurs before customer confirmation and final booking processing.

---

## 21. Impact

**Before Fix**
The defect prevented the case from successfully completing the availability decision.

```text
Movie Ticket Request
        |
        v
Availability
        |
        v
ValidateSufficientSeats
        |
        X
NumberFormatException
```

**After Fix**
The expected flow is:

```text
Movie Ticket Request
        |
        v
Availability
        |
        v
ValidateSufficientSeats
        |
        v
Seat Availability Status
        |
        v
Approval
```

The defect therefore affected the Availability stage and indirectly blocked subsequent booking stages.

---

## 22. What Was Not Changed
The following were intentionally not changed as part of this defect:

- `NumberOfTickets` data type
- `AvailableSeatsCount` data type
- `Movie` data object
- `Show` data object
- Total Cost calculation formula
- Customer confirmation logic
- Booking Execution logic
- SLA configuration
- Show Type routing

The correction was kept isolated to the numeric Decision Table condition configuration.

---

## 23. Lessons Learned

**Use the exact exception as the starting point**
The value `"1 to 10"` provided a strong indication that a range expression was being processed incorrectly.

**Verify the data type before changing it**
`NumberOfTickets` was already correctly configured as an Integer.

**Distinguish property configuration from rule configuration**
A correctly typed property can still be used incorrectly inside a Decision Table.

**Configure numeric ranges as numeric ranges**
Human-readable expressions such as `1 to 10` should not be treated as literal numeric values.

**Test range boundaries**
Decision Tables using ranges should be tested at:
- Lower boundary
- Upper boundary
- Just below boundary
- Just above boundary

This helps identify overlapping or incorrectly configured conditions.

---

## 24. Final Status

| Field | Detail |
|---|---|
| **Defect ID** | DEF-001 |
| **Status** | RESOLVED |
| **Severity** | HIGH |
| **Component** | ValidateSufficientSeats |
| **Error** | NumberFormatException |
| **Input** | `"1 to 10"` |
| **Root Cause** | Incorrect numeric range interpretation |
| **Resolution** | Correct numeric range configuration |

---

## 25. Final Summary
The `NumberFormatException` was caused by incorrect interpretation of the numeric range used by the `ValidateSufficientSeats` Decision Table.

The affected range was:
`1 to 10`

The `Number of Tickets` property was verified as an Integer, so the property definition itself did not require modification.

The corrective action was to configure the Decision Table to evaluate the range using numeric boundaries rather than treating the complete range expression as a numeric value.

The corrected conceptual condition is:
`1 <= Number of Tickets <= 10`

The defect was therefore resolved at the Decision Table configuration level while preserving the existing data model and business logic.
