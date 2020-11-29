---
sort: 2
---
# General definitions

## Numerical values in finance

Because precision in finance matters very much the **Float type must not be used**, it's an approximate data type which mean values are round up. **Decimal or String representation** should be preferred. A common practice is to use Decimal type inside independent systems and String for communication between systems. This is why every numerical values which matters in this protocol is stored in a string.

## Units of time definition

|   unit | symbol |
| -----: | :----- |
| second | s      |
| minute | m      |
|   hour | h      |
|    day | d      |
|   week | w      |
|  month | M      |
|   year | y      |

The biggest unit of time possible must be chosen to represent a time period.

For example `5m` must be chosen instead of `300s`.
