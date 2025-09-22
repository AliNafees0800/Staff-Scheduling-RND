# HR System - Complete Features Documentation

## Table of Contents
1. [System Overview](#system-overview)
2. [Feature Categories](#feature-categories)
3. [Entity Details](#entity-details)
4. [Data Relationships](#data-relationships)
5. [Business Rules](#business-rules)
6. [System Capabilities](#system-capabilities)

---

## System Overview

This comprehensive HR system provides complete workforce management capabilities including employee management, time tracking, scheduling, accrual management, and payroll processing. The system features a unified rule engine that handles complex business logic for overtime, holiday pay, and leave accruals.

**Total Entities:** 28  
**Total Relationships:** 25+  
**Rule Engine Tables:** 5  
**Color-Coded Categories:** 6

---

## Feature Categories

### 🟣 Organizational Structure
- **Purpose:** Core employee and organizational hierarchy management
- **Entities:** 4 tables
- **Key Features:** Employee profiles, department hierarchy, position management, job codes

### 🟢 Time Management
- **Purpose:** Comprehensive time tracking and entry management
- **Entities:** 4 tables
- **Key Features:** Time entry types, time tracking, timesheets, clock in/out

### 🟡 Scheduling System
- **Purpose:** Advanced shift scheduling and assignment management
- **Entities:** 5 tables
- **Key Features:** Schedule creation, shift management, employee assignments, shift trading

### 🔴 Accrual System
- **Purpose:** Leave and benefit accrual management
- **Entities:** 4 tables
- **Key Features:** Accrual types, rules, balance tracking, transaction history

### 🔵 Payroll System
- **Purpose:** Complete payroll processing and management
- **Entities:** 3 tables
- **Key Features:** Pay periods, payroll calculations, itemized payroll entries

### 🟠 Rule Engine System
- **Purpose:** Unified business rules and processing engine
- **Entities:** 5 tables
- **Key Features:** Business rules, overtime rules, holiday rules, rule execution, processing steps

---

## Entity Details

### 🟣 Organizational Structure

#### Employee
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| EmployeeId | UUID | PK | Unique employee identifier - Primary key for employee records |
| EmployeeNumber | String | UNIQUE | Employee number for identification - Unique alphanumeric identifier for HR purposes |
| FirstName | String | Required | Employee's first name - Legal first name for official records |
| LastName | String | Required | Employee's last name - Legal last name for official records |
| MiddleName | String | Optional | Employee's middle name - Middle name or initial if applicable |
| Email | String | UNIQUE | Employee's email address - Primary communication email for system access |
| Phone | String | Optional | Employee's phone number - Primary contact phone number |
| HireDate | Date | Required | Employee's hire date - Official start date of employment |
| TerminationDate | Date | Optional | Employee's termination date - End date of employment if applicable |
| Status | Enum | Required | Employee status - **Values:** `ACTIVE`, `INACTIVE`, `TERMINATED`, `ON_LEAVE`, `SUSPENDED` |
| DepartmentId | UUID | FK | Reference to Department - Links employee to organizational department |
| PositionId | UUID | FK | Reference to Position - Links employee to job position |
| ManagerId | UUID | FK (Self) | Reference to Manager Employee - Self-referencing FK to employee's direct manager |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### Department
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| DepartmentId | UUID | PK | Unique department identifier - Primary key for department records |
| DepartmentCode | String | UNIQUE | Department code for identification - Short alphanumeric code for quick reference |
| DepartmentName | String | Required | Department name - Official name of the organizational unit |
| Description | String | Optional | Department description - Detailed description of department purpose and responsibilities |
| ParentDepartmentId | UUID | FK (Self) | Reference to parent department - Self-referencing FK for hierarchical organization structure |
| ManagerId | UUID | FK | Reference to department manager - Links to employee who manages this department |
| IsActive | Boolean | Required | Department active status - Indicates if department is currently operational |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### Position
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PositionId | UUID | PK | Unique position identifier - Primary key for position records |
| PositionCode | String | UNIQUE | Position code for identification - Short alphanumeric code for position reference |
| PositionTitle | String | Required | Position title - Official job title for the position |
| Description | String | Optional | Position description - Detailed description of position responsibilities and requirements |
| DepartmentId | UUID | FK | Reference to Department - Links position to organizational department |
| JobCodeId | UUID | FK | Reference to JobCode - Links position to job classification |
| SalaryGrade | String | Optional | Salary grade level - Compensation grade or level for this position |
| IsActive | Boolean | Required | Position active status - Indicates if position is currently available |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### JobCode
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| JobCodeId | UUID | PK | Unique job code identifier - Primary key for job code records |
| JobCode | String | UNIQUE | Job code for identification - Short alphanumeric code for job classification |
| JobTitle | String | Required | Job title - Official title for this job classification |
| Description | String | Optional | Job description - Detailed description of job responsibilities and requirements |
| Category | String | Optional | Job category - Classification category (e.g., "Management", "Technical", "Administrative") |
| IsExempt | Boolean | Required | FLSA exempt status - Indicates if position is exempt from overtime pay under Fair Labor Standards Act |
| DefaultHoursPerWeek | Decimal | Optional | Default weekly hours - Standard number of hours expected per week for this job |
| IsActive | Boolean | Required | Job code active status - Indicates if job code is currently in use |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

### 🟢 Time Management

#### TimeEntryType
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| TimeEntryTypeId | UUID | PK | Unique time entry type identifier - Primary key for time entry type records |
| TypeCode | String | UNIQUE | Type code for identification - Short alphanumeric code for quick reference |
| TypeName | String | Required | Type name - Display name for the time entry type |
| Description | String | Optional | Type description - Detailed description of when and how to use this time type |
| Category | Enum | Required | Time category - **Values:** `WORK`, `LEAVE`, `OVERTIME`, `HOLIDAY`, `BREAK`, `TRAINING`, `MEETING`, `OTHER` |
| IsPaid | Boolean | Required | Whether time is paid - Indicates if this time type results in compensation |
| IsAccrualEligible | Boolean | Required | Whether time accrues leave - Indicates if this time type contributes to leave accrual |
| AccrualFactor | Decimal | Optional | Accrual multiplier - Multiplier applied to hours for accrual calculations (e.g., 1.0 for regular work, 0.5 for part-time) |
| RequiresApproval | Boolean | Required | Whether approval is required - Indicates if this time type needs manager approval |
| MaxHoursPerDay | Decimal | Optional | Maximum hours per day - Maximum hours allowed per day for this time type |
| MaxHoursPerWeek | Decimal | Optional | Maximum hours per week - Maximum hours allowed per week for this time type |
| ColorCode | String | Optional | Display color code - Hex color code for UI display (e.g., "#FF5733") |
| IsActive | Boolean | Required | Type active status - Indicates if this time type is currently available for use |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### TimeEntry
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| TimeEntryId | UUID | PK | Unique time entry identifier - Primary key for time entry records |
| EmployeeId | UUID | FK | Reference to Employee - Links time entry to specific employee |
| TimeEntryTypeId | UUID | FK | Reference to TimeEntryType - Links to the type of time being recorded |
| ClockInOutId | UUID | FK (Nullable) | Reference to ClockInOut - Links to clock in/out record if applicable |
| TimeSheetId | UUID | FK (Nullable) | Reference to TimeSheet - Links to timesheet if entry is part of a timesheet |
| HolidayId | UUID | FK (Nullable) | Reference to Holiday - Links to holiday if entry is for holiday work |
| EntryDate | Date | Required | Entry date - Date for which time is being recorded |
| StartTime | Time | Required | Start time - Time when the activity began |
| EndTime | Time | Required | End time - Time when the activity ended |
| BreakDuration | Decimal | Optional | Break duration in hours - Total break time taken during this entry |
| TotalHours | Decimal | Required | Total hours worked - Calculated total hours (EndTime - StartTime - BreakDuration) |
| Description | String | Optional | Entry description - Additional details about the work performed |
| ProjectCode | String | Optional | Project code - Code identifying the project this time is associated with |
| CostCenter | String | Optional | Cost center - Code identifying the cost center for accounting purposes |
| Status | Enum | Required | Entry status - **Values:** `DRAFT`, `SUBMITTED`, `APPROVED`, `REJECTED`, `PROCESSED` |
| SubmittedAt | DateTime | Optional | Submission timestamp - When the entry was submitted for approval |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver - Employee who approved this time entry |
| ApprovedAt | DateTime | Optional | Approval timestamp - When the entry was approved |
| RejectionReason | String | Optional | Rejection reason - Reason provided if entry was rejected |
| CreatedBy | UUID | FK | Reference to creator - Employee who created this time entry |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### TimeSheet
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| TimeSheetId | UUID | PK | Unique timesheet identifier - Primary key for timesheet records |
| EmployeeId | UUID | FK | Reference to Employee - Links timesheet to specific employee |
| PayPeriodId | UUID | FK | Reference to PayPeriod - Links timesheet to specific pay period |
| StartDate | Date | Required | Timesheet start date - First date covered by this timesheet |
| EndDate | Date | Required | Timesheet end date - Last date covered by this timesheet |
| TotalRegularHours | Decimal | Required | Total regular hours - Sum of all regular work hours in this timesheet |
| TotalOvertimeHours | Decimal | Required | Total overtime hours - Sum of all overtime hours in this timesheet |
| TotalLeaveHours | Decimal | Required | Total leave hours - Sum of all leave hours taken in this timesheet |
| Status | Enum | Required | Timesheet status - **Values:** `DRAFT`, `SUBMITTED`, `APPROVED`, `REJECTED`, `PROCESSED` |
| SubmittedAt | DateTime | Optional | Submission timestamp - When the timesheet was submitted for approval |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver - Employee who approved this timesheet |
| ApprovedAt | DateTime | Optional | Approval timestamp - When the timesheet was approved |
| RejectionReason | String | Optional | Rejection reason - Reason provided if timesheet was rejected |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### ClockInOut
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ClockId | UUID | PK | Unique clock identifier - Primary key for clock in/out records |
| EmployeeId | UUID | FK | Reference to Employee - Links clock record to specific employee |
| ShiftAssignmentId | UUID | FK | Reference to ShiftAssignment - Links to assigned shift for this clock session |
| ClockInTime | DateTime | Required | Clock in timestamp - When employee clocked in |
| ClockOutTime | DateTime | Optional | Clock out timestamp - When employee clocked out (null if still clocked in) |
| ClockInLocation | String | Optional | Clock in location - Physical location where employee clocked in |
| ClockOutLocation | String | Optional | Clock out location - Physical location where employee clocked out |
| ClockInMethod | String | Optional | Clock in method - Method used to clock in (e.g., "Mobile App", "Time Clock", "Web Portal") |
| ClockOutMethod | String | Optional | Clock out method - Method used to clock out (e.g., "Mobile App", "Time Clock", "Web Portal") |
| Status | Enum | Required | Clock status - **Values:** `CLOCKED_IN`, `CLOCKED_OUT`, `BREAK`, `LUNCH`, `INVALID` |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

### 🟡 Scheduling System

#### Schedule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ScheduleId | UUID | PK | Unique schedule identifier - Primary key for schedule records |
| ScheduleName | String | Required | Schedule name - Descriptive name for the schedule (e.g., "Week 1 - Production") |
| ScheduleType | Enum | Required | Schedule type - **Values:** `WEEKLY`, `BIWEEKLY`, `MONTHLY`, `CUSTOM`, `ROTATING`, `FIXED` |
| DepartmentId | UUID | FK | Reference to Department - Links schedule to specific department |
| StartDate | Date | Required | Schedule start date - First date this schedule is effective |
| EndDate | Date | Required | Schedule end date - Last date this schedule is effective |
| RequiredEmployees | Integer | Required | Required number of employees - Minimum number of employees needed for this schedule |
| Status | Enum | Required | Schedule status - **Values:** `DRAFT`, `ACTIVE`, `INACTIVE`, `COMPLETED`, `CANCELLED` |
| Notes | String | Optional | Schedule notes - Additional information or special instructions for this schedule |
| CreatedBy | UUID | FK | Reference to creator - Employee who created this schedule |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### Shift
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ShiftId | UUID | PK | Unique shift identifier - Primary key for shift records |
| ShiftName | String | Required | Shift name - Descriptive name for the shift (e.g., "Day Shift", "Night Shift") |
| ShiftCode | String | Required | Shift code - Short alphanumeric code for quick reference (e.g., "DAY", "NIGHT") |
| StartTime | Time | Required | Shift start time - Time when the shift begins |
| EndTime | Time | Required | Shift end time - Time when the shift ends |
| BreakDuration | Decimal | Optional | Break duration in hours - Total break time included in this shift |
| DurationHours | Decimal | Required | Total shift duration - Total hours for this shift (EndTime - StartTime - BreakDuration) |
| Description | String | Optional | Shift description - Additional details about shift requirements or characteristics |
| IsOvernight | Boolean | Required | Whether shift is overnight - Indicates if shift crosses midnight |
| ColorCode | String | Optional | Display color code - Hex color code for UI display (e.g., "#3498DB") |
| IsActive | Boolean | Required | Shift active status - Indicates if this shift is currently available for scheduling |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### ScheduleFrequency
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ScheduleFrequencyId | UUID | PK | Unique frequency identifier - Primary key for schedule frequency records |
| ScheduleId | UUID | FK | Reference to Schedule - Links frequency to specific schedule |
| ShiftId | UUID | FK | Reference to Shift - Links frequency to specific shift |
| DayOfWeek | Enum | Required | Day of week - **Values:** `MONDAY`, `TUESDAY`, `WEDNESDAY`, `THURSDAY`, `FRIDAY`, `SATURDAY`, `SUNDAY` |
| StartTime | Time | Required | Start time for this day - Start time for this day of the week |
| EndTime | Time | Required | End time for this day - End time for this day of the week |
| IsActive | Boolean | Required | Frequency active status - Indicates if this frequency is currently active |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### ShiftAssignment
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AssignmentId | UUID | PK | Unique assignment identifier - Primary key for shift assignment records |
| ScheduleId | UUID | FK | Reference to Schedule - Links assignment to specific schedule |
| EmployeeId | UUID | FK | Reference to Employee - Links assignment to specific employee |
| Status | Enum | Required | Assignment status - **Values:** `ASSIGNED`, `CONFIRMED`, `CANCELLED`, `COMPLETED`, `NO_SHOW` |
| Notes | String | Optional | Assignment notes - Additional information about this assignment |
| AssignedAt | DateTime | Required | Assignment timestamp - When this assignment was created |
| AssignedBy | UUID | FK | Reference to assigner - Employee who made this assignment |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### ShiftTrade
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| TradeId | UUID | PK | Unique trade identifier - Primary key for shift trade records |
| FromEmployeeId | UUID | FK | Reference to giving employee - Employee who is giving up their shift |
| ToEmployeeId | UUID | FK | Reference to receiving employee - Employee who is taking the shift |
| FromAssignmentId | UUID | FK | Reference to giving assignment - Original assignment being traded away |
| ToAssignmentId | UUID | FK | Reference to receiving assignment - Assignment being traded for |
| Status | Enum | Required | Trade status - **Values:** `PENDING`, `APPROVED`, `REJECTED`, `CANCELLED`, `COMPLETED` |
| Reason | String | Optional | Trade reason - Explanation for why the trade is being requested |
| NeedApproval | Boolean | Required | Whether approval is needed - Indicates if this trade requires approval |
| NeedRejectionApproval | Boolean | Required | Whether rejection needs approval - Indicates if rejecting this trade requires approval |
| AutoApproveIfEligible | Boolean | Required | Auto-approve if eligible - Automatically approve if both employees are eligible |
| RequireManagerApproval | Boolean | Required | Require manager approval - Indicates if manager approval is required |
| AllowPartialTrade | Boolean | Required | Allow partial trade - Indicates if partial shift trades are allowed |
| MaxTradeDuration | Decimal | Optional | Maximum trade duration - Maximum number of hours for this trade |
| RequestedAt | DateTime | Required | Request timestamp - When the trade was requested |
| ApprovedAt | DateTime | Optional | Approval timestamp - When the trade was approved |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver - Employee who approved this trade |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

### 🔴 Accrual System

#### AccrualType
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualTypeId | UUID | PK | Unique accrual type identifier - Primary key for accrual type records |
| TypeCode | String | UNIQUE | Type code for identification - Short alphanumeric code for quick reference (e.g., "PTO", "SICK") |
| TypeName | String | Required | Type name - Display name for the accrual type (e.g., "Paid Time Off", "Sick Leave") |
| Description | String | Optional | Type description - Detailed description of this accrual type and its usage |
| Unit | Enum | Required | Accrual unit - **Values:** `HOURS`, `DAYS` |
| Category | Enum | Required | Accrual category - **Values:** `VACATION`, `SICK_LEAVE`, `PERSONAL`, `HOLIDAY`, `COMPENSATORY`, `OTHER` |
| MaxBalance | Decimal | Optional | Maximum balance allowed - Maximum amount that can be accrued (null for unlimited) |
| MaxCarryover | Decimal | Optional | Maximum carryover allowed - Maximum amount that can be carried over to next period |
| CarryoverExpiryMonths | Integer | Optional | Carryover expiry in months - Number of months before carryover expires (null for no expiry) |
| IsActive | Boolean | Required | Type active status - Indicates if this accrual type is currently available |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### AccrualRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualRuleId | UUID | PK | Unique accrual rule identifier - Primary key for accrual rule records |
| BusinessRuleId | UUID | FK | Reference to BusinessRule - Links to the business rule that governs this accrual |
| AccrualTypeId | UUID | FK | Reference to AccrualType - Links to the type of accrual this rule applies to |
| TimeEntryTypeId | UUID | FK | Reference to TimeEntryType - Links to time entry type that triggers this accrual |
| EffectivePayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod - Pay period when this rule becomes effective |
| RuleName | String | Required | Rule name - Descriptive name for this accrual rule |
| JobCodeId | UUID | FK | Reference to JobCode - Job code this rule applies to |
| DepartmentId | UUID | FK | Reference to Department - Department this rule applies to |
| EmployeeStatus | Enum | Required | Applicable employee status - **Values:** `ACTIVE`, `ON_LEAVE`, `PART_TIME`, `FULL_TIME` |
| YearsOfServiceMin | Decimal | Optional | Minimum years of service - Minimum years required for this rule to apply |
| YearsOfServiceMax | Decimal | Optional | Maximum years of service - Maximum years for this rule to apply |
| AccrualRate | Decimal | Required | Accrual rate - Rate at which accrual is earned (e.g., 0.08 for 8 hours per month) |
| AccrualFrequency | Enum | Required | Accrual frequency - **Values:** `HOURLY`, `DAILY`, `WEEKLY`, `MONTHLY`, `YEARLY` |
| WaitingPeriodDays | Integer | Optional | Waiting period in days - Days to wait before accrual begins |
| EffectiveDate | Date | Required | Rule effective date - Date when this rule becomes effective |
| ExpiryDate | Date | Optional | Rule expiry date - Date when this rule expires (null for no expiry) |
| IsActive | Boolean | Required | Rule active status - Indicates if this rule is currently active |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### AccrualBalance
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualBalanceId | UUID | PK | Unique balance identifier - Primary key for accrual balance records |
| EmployeeId | UUID | FK | Reference to Employee - Links balance to specific employee |
| AccrualTypeId | UUID | FK | Reference to AccrualType - Links balance to specific accrual type |
| LastUpdatedPayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod - Last pay period when balance was updated |
| CurrentBalance | Decimal | Required | Current balance - Current available balance for this accrual type |
| PendingBalance | Decimal | Required | Pending balance - Balance pending approval or processing |
| UsedBalance | Decimal | Required | Used balance - Total amount used from this accrual type |
| CarryoverBalance | Decimal | Required | Carryover balance - Amount carried over from previous period |
| CarryoverExpiryDate | Date | Optional | Carryover expiry date - Date when carryover balance expires |
| LastAccrualDate | Date | Optional | Last accrual date - Date when balance was last updated with new accruals |
| LastUpdated | DateTime | Required | Last update timestamp - When this balance record was last modified |

#### AccrualTransaction
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualTransactionId | UUID | PK | Unique transaction identifier - Primary key for accrual transaction records |
| EmployeeId | UUID | FK | Reference to Employee - Links transaction to specific employee |
| AccrualTypeId | UUID | FK | Reference to AccrualType - Links transaction to specific accrual type |
| TimeEntryId | UUID | FK | Reference to TimeEntry - Links transaction to time entry that triggered it |
| PayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod - Pay period when transaction occurred |
| TransactionType | Enum | Required | Transaction type - **Values:** `ACCRUAL`, `USAGE`, `ADJUSTMENT`, `CARRYOVER`, `EXPIRY`, `TRANSFER` |
| Amount | Decimal | Required | Transaction amount - Amount of accrual earned, used, or adjusted |
| BalanceAfter | Decimal | Required | Balance after transaction - Employee's balance after this transaction |
| ReferenceId | UUID | Optional | Reference identifier - ID of related record (e.g., leave request, adjustment) |
| ReferenceType | Enum | Required | Reference type - **Values:** `TIME_ENTRY`, `LEAVE_REQUEST`, `ADJUSTMENT`, `CARRYOVER`, `SYSTEM` |
| Description | String | Optional | Transaction description - Human-readable description of this transaction |
| ProcessedDate | DateTime | Required | Processing date - Date when transaction was processed |
| ProcessedBy | UUID | FK | Reference to processor - Employee or system that processed this transaction |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |

### 🔵 Payroll System

#### PayPeriod
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayPeriodId | UUID | PK | Unique pay period identifier - Primary key for pay period records |
| PeriodName | String | Required | Pay period name - Descriptive name for the pay period (e.g., "2024-01-01 to 2024-01-15") |
| StartDate | Date | Required | Period start date - First date covered by this pay period |
| EndDate | Date | Required | Period end date - Last date covered by this pay period |
| PayDate | Date | Required | Pay date - Date when employees receive their pay |
| Frequency | Enum | Required | Pay frequency - **Values:** `WEEKLY`, `BIWEEKLY`, `SEMIMONTHLY`, `MONTHLY`, `QUARTERLY`, `ANNUALLY` |
| Status | Enum | Required | Period status - **Values:** `DRAFT`, `OPEN`, `CLOSED`, `PROCESSED`, `CANCELLED` |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### Payroll
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayrollId | UUID | PK | Unique payroll identifier - Primary key for payroll records |
| EmployeeId | UUID | FK | Reference to Employee - Links payroll to specific employee |
| PayPeriodId | UUID | FK | Reference to PayPeriod - Links payroll to specific pay period |
| RegularHours | Decimal | Required | Regular hours worked - Total regular hours for this pay period |
| OvertimeHours | Decimal | Required | Overtime hours worked - Total overtime hours for this pay period |
| DoubleTimeHours | Decimal | Required | Double time hours worked - Total double time hours for this pay period |
| HolidayHours | Decimal | Required | Holiday hours worked - Total holiday hours for this pay period |
| SickHours | Decimal | Required | Sick hours used - Total sick leave hours used this pay period |
| VacationHours | Decimal | Required | Vacation hours used - Total vacation hours used this pay period |
| PersonalHours | Decimal | Required | Personal hours used - Total personal time hours used this pay period |
| OtherHours | Decimal | Required | Other hours - Total other types of hours for this pay period |
| GrossPay | Decimal | Required | Gross pay amount - Total gross pay before deductions |
| NetPay | Decimal | Required | Net pay amount - Final pay amount after all deductions |
| TotalDeductions | Decimal | Required | Total deductions - Sum of all deductions from gross pay |
| Status | Enum | Required | Payroll status - **Values:** `DRAFT`, `CALCULATED`, `APPROVED`, `PROCESSED`, `CANCELLED` |
| CalculatedAt | DateTime | Required | Calculation timestamp - When payroll was calculated |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver - Employee who approved this payroll |
| ApprovedAt | DateTime | Optional | Approval timestamp - When payroll was approved |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### PayrollItem
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayrollItemId | UUID | PK | Unique item identifier - Primary key for payroll item records |
| PayrollId | UUID | FK | Reference to Payroll - Links item to specific payroll record |
| ItemType | Enum | Required | Item type - **Values:** `EARNINGS`, `DEDUCTION`, `BENEFIT`, `TAX`, `BONUS`, `COMMISSION`, `REIMBURSEMENT` |
| ItemCode | String | Required | Item code - Short code for this payroll item (e.g., "REG", "OT", "FED_TAX") |
| ItemName | String | Required | Item name - Display name for this payroll item (e.g., "Regular Pay", "Overtime", "Federal Tax") |
| Amount | Decimal | Required | Item amount - Dollar amount for this payroll item |
| Hours | Decimal | Optional | Hours for this item - Number of hours associated with this item |
| Rate | Decimal | Optional | Rate for this item - Hourly rate or percentage rate for this item |
| IsPreTax | Boolean | Required | Pre-tax status - Indicates if this item is calculated before taxes |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |

### 🟠 Rule Engine System

#### BusinessRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| RuleId | UUID | PK | Unique rule identifier - Primary key for business rule records |
| RuleName | String | Required | Rule name - Descriptive name for this business rule |
| RuleType | Enum | Required | Rule type - **Values:** `OVERTIME`, `HOLIDAY`, `ACCRUAL`, `PREMIUM`, `DEDUCTION`, `BONUS` |
| DepartmentId | UUID | FK (Nullable) | Reference to Department - Department this rule applies to (null for all departments) |
| JobCodeId | UUID | FK (Nullable) | Reference to JobCode - Job code this rule applies to (null for all job codes) |
| TimeEntryTypeId | UUID | FK (Nullable) | Reference to TimeEntryType - Time entry type this rule applies to (null for all types) |
| Conditions | JSON | Required | Rule conditions - JSON object defining when this rule applies |
| Actions | JSON | Required | Rule actions - JSON object defining what this rule does |
| Priority | Integer | Required | Rule priority - Priority order for rule execution (lower numbers execute first) |
| EffectiveDate | Date | Required | Rule effective date - Date when this rule becomes effective |
| ExpiryDate | Date | Optional | Rule expiry date - Date when this rule expires (null for no expiry) |
| IsActive | Boolean | Required | Rule active status - Indicates if this rule is currently active |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### OvertimeRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| OvertimeRuleId | UUID | PK | Unique overtime rule identifier - Primary key for overtime rule records |
| BusinessRuleId | UUID | FK | Reference to BusinessRule - Links to the business rule that governs this overtime rule |
| ThresholdType | Enum | Required | Threshold type - **Values:** `DAILY`, `WEEKLY`, `BIWEEKLY`, `MONTHLY` |
| ThresholdHours | Decimal | Required | Threshold hours - Number of hours before overtime applies |
| Multiplier | Decimal | Required | Overtime multiplier - Multiplier for overtime pay (e.g., 1.5 for time and a half) |
| AppliesToHolidays | Boolean | Required | Applies to holidays - Indicates if this rule applies to holiday work |
| AppliesToWeekends | Boolean | Required | Applies to weekends - Indicates if this rule applies to weekend work |
| MaxOvertimeHours | Decimal | Optional | Maximum overtime hours - Maximum overtime hours allowed per period |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### HolidayRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayRuleId | UUID | PK | Unique holiday rule identifier - Primary key for holiday rule records |
| BusinessRuleId | UUID | FK | Reference to BusinessRule - Links to the business rule that governs this holiday rule |
| HolidayId | UUID | FK | Reference to Holiday - Links to specific holiday this rule applies to |
| PayType | Enum | Required | Pay type - **Values:** `REGULAR`, `HOLIDAY_PAY`, `DOUBLE_TIME`, `TRIPLE_TIME` |
| Multiplier | Decimal | Required | Holiday multiplier - Multiplier for holiday pay (e.g., 2.0 for double time) |
| RequiresWork | Boolean | Required | Requires work - Indicates if employee must work to receive holiday pay |
| FloatingHolidayRules | JSON | Optional | Floating holiday rules - JSON object defining floating holiday logic |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### RuleExecution
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| RuleExecutionId | UUID | PK | Unique execution identifier - Primary key for rule execution records |
| TimeEntryId | UUID | FK | Reference to TimeEntry - Links execution to specific time entry |
| EmployeeId | UUID | FK | Reference to Employee - Links execution to specific employee |
| PayPeriodId | UUID | FK | Reference to PayPeriod - Links execution to specific pay period |
| RuleType | Enum | Required | Rule type executed - **Values:** `OVERTIME`, `HOLIDAY`, `ACCRUAL`, `PREMIUM`, `DEDUCTION`, `BONUS` |
| AppliedRules | JSON | Required | Applied rules - JSON array of rules that were applied |
| CalculatedAmount | Decimal | Required | Calculated amount - Dollar amount calculated by rule execution |
| CalculatedHours | Decimal | Required | Calculated hours - Hours calculated by rule execution |
| Status | Enum | Required | Execution status - **Values:** `PENDING`, `SUCCESS`, `FAILED`, `SKIPPED` |
| ProcessedAt | DateTime | Required | Processing timestamp - When rule execution was completed |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |

#### RuleProcessingStep
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| StepId | UUID | PK | Unique step identifier - Primary key for rule processing step records |
| RuleExecutionId | UUID | FK | Reference to RuleExecution - Links step to specific rule execution |
| StepName | String | Required | Step name - Name of this processing step (e.g., "Validate Conditions", "Calculate Amount") |
| InputData | JSON | Required | Input data - JSON object containing input data for this step |
| OutputData | JSON | Required | Output data - JSON object containing output data from this step |
| Status | Enum | Required | Step status - **Values:** `PENDING`, `PROCESSING`, `SUCCESS`, `FAILED`, `SKIPPED` |
| ProcessedAt | DateTime | Required | Processing timestamp - When this step was completed |
| ErrorMessage | String | Optional | Error message - Error message if step failed |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |

### Additional Entities

#### Holiday
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayId | UUID | PK | Unique holiday identifier - Primary key for holiday records |
| HolidayName | String | Required | Holiday name - Name of the holiday (e.g., "New Year's Day", "Christmas") |
| HolidayDate | Date | Required | Holiday date - Date when the holiday occurs |
| IsObserved | Boolean | Required | Is observed holiday - Indicates if this is an observed holiday (may differ from actual date) |
| IsFloating | Boolean | Required | Is floating holiday - Indicates if this is a floating holiday that can be taken anytime |
| AppliesToAll | Boolean | Required | Applies to all employees - Indicates if this holiday applies to all employees |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

#### HolidayAssignment
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayAssignmentId | UUID | PK | Unique assignment identifier - Primary key for holiday assignment records |
| HolidayId | UUID | FK | Reference to Holiday - Links assignment to specific holiday |
| EmployeeId | UUID | FK | Reference to Employee - Links assignment to specific employee (null for department/job code assignments) |
| DepartmentId | UUID | FK | Reference to Department - Links assignment to specific department (null for employee/job code assignments) |
| JobCodeId | UUID | FK | Reference to JobCode - Links assignment to specific job code (null for employee/department assignments) |

#### EmployeeAvailability
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AvailabilityId | UUID | PK | Unique availability identifier - Primary key for employee availability records |
| EmployeeId | UUID | FK | Reference to Employee - Links availability to specific employee |
| DayOfWeek | Enum | Required | Day of week - **Values:** `MONDAY`, `TUESDAY`, `WEDNESDAY`, `THURSDAY`, `FRIDAY`, `SATURDAY`, `SUNDAY` |
| StartTime | Time | Required | Available start time - Time when employee becomes available on this day |
| EndTime | Time | Required | Available end time - Time when employee stops being available on this day |
| IsAvailable | Boolean | Required | Availability status - Indicates if employee is available during this time slot |
| EffectiveDate | Date | Required | Effective date - Date when this availability becomes effective |
| ExpiryDate | Date | Optional | Expiry date - Date when this availability expires (null for no expiry) |
| CreatedAt | DateTime | Required | Record creation timestamp - Audit trail for record creation |
| UpdatedAt | DateTime | Required | Record last update timestamp - Audit trail for record modifications |

---

## Data Relationships

### Primary Relationships
- **Employee** → **Department** (Many-to-One)
- **Employee** → **Position** (Many-to-One)
- **Employee** → **TimeEntry** (One-to-Many)
- **Employee** → **TimeSheet** (One-to-Many)
- **Employee** → **ShiftAssignment** (One-to-Many)
- **Employee** → **AccrualBalance** (One-to-Many)
- **Employee** → **Payroll** (One-to-Many)

### Rule Engine Relationships
- **BusinessRule** → **OvertimeRule** (One-to-One)
- **BusinessRule** → **HolidayRule** (One-to-One)
- **BusinessRule** → **AccrualRule** (One-to-One)
- **TimeEntry** → **RuleExecution** (One-to-Many)
- **RuleExecution** → **RuleProcessingStep** (One-to-Many)

### Scheduling Relationships
- **Schedule** → **ScheduleFrequency** (One-to-Many)
- **Schedule** → **ShiftAssignment** (One-to-Many)
- **Shift** → **ScheduleFrequency** (One-to-Many)
- **ShiftAssignment** → **ShiftTrade** (One-to-Many)

---

## Business Rules

### Rule Types
1. **OVERTIME** - Overtime calculation rules
2. **HOLIDAY** - Holiday pay rules
3. **ACCRUAL** - Leave accrual rules
4. **PREMIUM** - Premium pay rules

### Rule Processing Flow
1. **Time Entry Created** → Triggers rule evaluation
2. **Business Rules Applied** → Based on employee, department, job code
3. **Specific Rules Executed** → Overtime, Holiday, Accrual rules
4. **Results Calculated** → Amounts and hours calculated
5. **Payroll Updated** → Results flow to payroll system

---

## System Capabilities

### Core Features
- ✅ **Employee Management** - Complete employee lifecycle
- ✅ **Time Tracking** - Comprehensive time entry and tracking
- ✅ **Scheduling** - Advanced shift scheduling and management
- ✅ **Leave Management** - Accrual and leave tracking
- ✅ **Payroll Processing** - Complete payroll calculations
- ✅ **Rule Engine** - Unified business rules processing
- ✅ **Approval Workflows** - Multi-level approval system
- ✅ **Audit Trail** - Complete change tracking

### Advanced Features
- ✅ **Shift Trading** - Employee-to-employee shift exchanges
- ✅ **Holiday Management** - Holiday pay and scheduling
- ✅ **Overtime Rules** - Flexible overtime calculations
- ✅ **Accrual Management** - Complex leave accrual rules
- ✅ **Multi-Department Support** - Hierarchical department structure
- ✅ **Flexible Time Types** - Customizable time entry categories
- ✅ **Real-time Processing** - Immediate rule execution
- ✅ **JSON Configuration** - Flexible rule conditions and actions

### Integration Points
- **Time Entry** → **Rule Engine** → **Payroll**
- **Scheduling** → **Time Tracking** → **Accruals**
- **Employee Data** → **All Systems** (Central reference)
- **Business Rules** → **All Calculations** (Unified processing)

---

## Technical Specifications

### Database Design
- **Primary Keys:** UUID format for all entities
- **Foreign Keys:** Proper referential integrity
- **Constraints:** UNIQUE, NOT NULL, CHECK constraints
- **Indexes:** Optimized for performance
- **JSON Fields:** Flexible configuration storage

### Data Types
- **UUID:** Primary and foreign keys
- **String:** Text fields with appropriate lengths
- **Decimal:** Numeric fields with precision
- **DateTime:** Timestamp fields
- **Boolean:** True/false flags
- **Enum:** Predefined value sets
- **JSON:** Flexible data storage

### Performance Considerations
- **Indexed Fields:** Frequently queried columns
- **Partitioning:** Large tables by date ranges
- **Caching:** Frequently accessed data
- **Archiving:** Historical data management

---

*This documentation represents a comprehensive HR system with 28 entities, 25+ relationships, and a unified rule engine capable of handling complex business logic for workforce management.*
