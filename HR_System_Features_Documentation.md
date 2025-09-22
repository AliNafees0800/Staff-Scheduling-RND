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
| ScheduleId | UUID | PK | Unique schedule identifier |
| ScheduleName | String | Required | Schedule name |
| DepartmentId | UUID | FK | Reference to Department |
| StartDate | Date | Required | Schedule start date |
| EndDate | Date | Required | Schedule end date |
| Status | Enum | Required | Schedule status |
| Notes | String | Optional | Schedule notes |
| CreatedBy | UUID | FK | Reference to creator |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### Shift
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ShiftId | UUID | PK | Unique shift identifier |
| ShiftName | String | Required | Shift name |
| ShiftCode | String | Required | Shift code |
| StartTime | Time | Required | Shift start time |
| EndTime | Time | Required | Shift end time |
| BreakDuration | Decimal | Optional | Break duration in hours |
| DurationHours | Decimal | Required | Total shift duration |
| Description | String | Optional | Shift description |
| IsOvernight | Boolean | Required | Whether shift is overnight |
| ColorCode | String | Optional | Display color code |
| IsActive | Boolean | Required | Shift active status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### ScheduleFrequency
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| ScheduleFrequencyId | UUID | PK | Unique frequency identifier |
| ScheduleId | UUID | FK | Reference to Schedule |
| ShiftId | UUID | FK | Reference to Shift |
| DayOfWeek | Enum | Required | Day of week |
| StartTime | Time | Required | Start time for this day |
| EndTime | Time | Required | End time for this day |
| RequiredEmployees | Integer | Required | Required number of employees |
| IsActive | Boolean | Required | Frequency active status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### ShiftAssignment
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AssignmentId | UUID | PK | Unique assignment identifier |
| ScheduleId | UUID | FK | Reference to Schedule |
| EmployeeId | UUID | FK | Reference to Employee |
| Status | Enum | Required | Assignment status |
| Notes | String | Optional | Assignment notes |
| AssignedAt | DateTime | Required | Assignment timestamp |
| AssignedBy | UUID | FK | Reference to assigner |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### ShiftTrade
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| TradeId | UUID | PK | Unique trade identifier |
| FromEmployeeId | UUID | FK | Reference to giving employee |
| ToEmployeeId | UUID | FK | Reference to receiving employee |
| FromAssignmentId | UUID | FK | Reference to giving assignment |
| ToAssignmentId | UUID | FK | Reference to receiving assignment |
| Status | Enum | Required | Trade status |
| Reason | String | Optional | Trade reason |
| NeedApproval | Boolean | Required | Whether approval is needed |
| NeedRejectionApproval | Boolean | Required | Whether rejection needs approval |
| AutoApproveIfEligible | Boolean | Required | Auto-approve if eligible |
| RequireManagerApproval | Boolean | Required | Require manager approval |
| AllowPartialTrade | Boolean | Required | Allow partial trade |
| MaxTradeDuration | Decimal | Optional | Maximum trade duration |
| RequestedAt | DateTime | Required | Request timestamp |
| ApprovedAt | DateTime | Optional | Approval timestamp |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

### 🔴 Accrual System

#### AccrualType
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualTypeId | UUID | PK | Unique accrual type identifier |
| TypeCode | String | UNIQUE | Type code for identification |
| TypeName | String | Required | Type name |
| Description | String | Optional | Type description |
| Unit | Enum | Required | Accrual unit (Hours, Days) |
| Category | Enum | Required | Accrual category |
| MaxBalance | Decimal | Optional | Maximum balance allowed |
| MaxCarryover | Decimal | Optional | Maximum carryover allowed |
| CarryoverExpiryMonths | Integer | Optional | Carryover expiry in months |
| IsActive | Boolean | Required | Type active status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### AccrualRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualRuleId | UUID | PK | Unique accrual rule identifier |
| BusinessRuleId | UUID | FK | Reference to BusinessRule |
| AccrualTypeId | UUID | FK | Reference to AccrualType |
| TimeEntryTypeId | UUID | FK | Reference to TimeEntryType |
| EffectivePayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod |
| RuleName | String | Required | Rule name |
| JobCodeId | UUID | FK | Reference to JobCode |
| DepartmentId | UUID | FK | Reference to Department |
| EmployeeStatus | Enum | Required | Applicable employee status |
| YearsOfServiceMin | Decimal | Optional | Minimum years of service |
| YearsOfServiceMax | Decimal | Optional | Maximum years of service |
| AccrualRate | Decimal | Required | Accrual rate |
| AccrualFrequency | Enum | Required | Accrual frequency |
| WaitingPeriodDays | Integer | Optional | Waiting period in days |
| EffectiveDate | Date | Required | Rule effective date |
| ExpiryDate | Date | Optional | Rule expiry date |
| IsActive | Boolean | Required | Rule active status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### AccrualBalance
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualBalanceId | UUID | PK | Unique balance identifier |
| EmployeeId | UUID | FK | Reference to Employee |
| AccrualTypeId | UUID | FK | Reference to AccrualType |
| LastUpdatedPayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod |
| CurrentBalance | Decimal | Required | Current balance |
| PendingBalance | Decimal | Required | Pending balance |
| UsedBalance | Decimal | Required | Used balance |
| CarryoverBalance | Decimal | Required | Carryover balance |
| CarryoverExpiryDate | Date | Optional | Carryover expiry date |
| LastAccrualDate | Date | Optional | Last accrual date |
| LastUpdated | DateTime | Required | Last update timestamp |

#### AccrualTransaction
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AccrualTransactionId | UUID | PK | Unique transaction identifier |
| EmployeeId | UUID | FK | Reference to Employee |
| AccrualTypeId | UUID | FK | Reference to AccrualType |
| TimeEntryId | UUID | FK | Reference to TimeEntry |
| PayPeriodId | UUID | FK (Nullable) | Reference to PayPeriod |
| TransactionType | Enum | Required | Transaction type |
| Amount | Decimal | Required | Transaction amount |
| BalanceAfter | Decimal | Required | Balance after transaction |
| ReferenceId | UUID | Optional | Reference identifier |
| ReferenceType | Enum | Required | Reference type |
| Description | String | Optional | Transaction description |
| ProcessedDate | DateTime | Required | Processing date |
| ProcessedBy | UUID | FK | Reference to processor |
| CreatedAt | DateTime | Required | Record creation timestamp |

### 🔵 Payroll System

#### PayPeriod
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayPeriodId | UUID | PK | Unique pay period identifier |
| PeriodName | String | Required | Pay period name |
| StartDate | Date | Required | Period start date |
| EndDate | Date | Required | Period end date |
| PayDate | Date | Required | Pay date |
| Frequency | Enum | Required | Pay frequency |
| Status | Enum | Required | Period status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### Payroll
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayrollId | UUID | PK | Unique payroll identifier |
| EmployeeId | UUID | FK | Reference to Employee |
| PayPeriodId | UUID | FK | Reference to PayPeriod |
| RegularHours | Decimal | Required | Regular hours worked |
| OvertimeHours | Decimal | Required | Overtime hours worked |
| DoubleTimeHours | Decimal | Required | Double time hours worked |
| HolidayHours | Decimal | Required | Holiday hours worked |
| SickHours | Decimal | Required | Sick hours used |
| VacationHours | Decimal | Required | Vacation hours used |
| PersonalHours | Decimal | Required | Personal hours used |
| OtherHours | Decimal | Required | Other hours |
| GrossPay | Decimal | Required | Gross pay amount |
| NetPay | Decimal | Required | Net pay amount |
| TotalDeductions | Decimal | Required | Total deductions |
| Status | Enum | Required | Payroll status |
| CalculatedAt | DateTime | Required | Calculation timestamp |
| ApprovedBy | UUID | FK (Nullable) | Reference to approver |
| ApprovedAt | DateTime | Optional | Approval timestamp |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### PayrollItem
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| PayrollItemId | UUID | PK | Unique item identifier |
| PayrollId | UUID | FK | Reference to Payroll |
| ItemType | Enum | Required | Item type |
| ItemCode | String | Required | Item code |
| ItemName | String | Required | Item name |
| Amount | Decimal | Required | Item amount |
| Hours | Decimal | Optional | Hours for this item |
| Rate | Decimal | Optional | Rate for this item |
| IsPreTax | Boolean | Required | Pre-tax status |
| CreatedAt | DateTime | Required | Record creation timestamp |

### 🟠 Rule Engine System

#### BusinessRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| RuleId | UUID | PK | Unique rule identifier |
| RuleName | String | Required | Rule name |
| RuleType | Enum | Required | Rule type |
| DepartmentId | UUID | FK (Nullable) | Reference to Department |
| JobCodeId | UUID | FK (Nullable) | Reference to JobCode |
| TimeEntryTypeId | UUID | FK (Nullable) | Reference to TimeEntryType |
| Conditions | JSON | Required | Rule conditions |
| Actions | JSON | Required | Rule actions |
| Priority | Integer | Required | Rule priority |
| EffectiveDate | Date | Required | Rule effective date |
| ExpiryDate | Date | Optional | Rule expiry date |
| IsActive | Boolean | Required | Rule active status |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### OvertimeRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| OvertimeRuleId | UUID | PK | Unique overtime rule identifier |
| BusinessRuleId | UUID | FK | Reference to BusinessRule |
| ThresholdType | Enum | Required | Threshold type |
| ThresholdHours | Decimal | Required | Threshold hours |
| Multiplier | Decimal | Required | Overtime multiplier |
| AppliesToHolidays | Boolean | Required | Applies to holidays |
| AppliesToWeekends | Boolean | Required | Applies to weekends |
| MaxOvertimeHours | Decimal | Optional | Maximum overtime hours |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### HolidayRule
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayRuleId | UUID | PK | Unique holiday rule identifier |
| BusinessRuleId | UUID | FK | Reference to BusinessRule |
| HolidayId | UUID | FK | Reference to Holiday |
| PayType | Enum | Required | Pay type |
| Multiplier | Decimal | Required | Holiday multiplier |
| RequiresWork | Boolean | Required | Requires work |
| FloatingHolidayRules | JSON | Optional | Floating holiday rules |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### RuleExecution
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| RuleExecutionId | UUID | PK | Unique execution identifier |
| TimeEntryId | UUID | FK | Reference to TimeEntry |
| EmployeeId | UUID | FK | Reference to Employee |
| PayPeriodId | UUID | FK | Reference to PayPeriod |
| RuleType | Enum | Required | Rule type executed |
| AppliedRules | JSON | Required | Applied rules |
| CalculatedAmount | Decimal | Required | Calculated amount |
| CalculatedHours | Decimal | Required | Calculated hours |
| Status | Enum | Required | Execution status |
| ProcessedAt | DateTime | Required | Processing timestamp |
| CreatedAt | DateTime | Required | Record creation timestamp |

#### RuleProcessingStep
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| StepId | UUID | PK | Unique step identifier |
| RuleExecutionId | UUID | FK | Reference to RuleExecution |
| StepName | String | Required | Step name |
| InputData | JSON | Required | Input data |
| OutputData | JSON | Required | Output data |
| Status | Enum | Required | Step status |
| ProcessedAt | DateTime | Required | Processing timestamp |
| ErrorMessage | String | Optional | Error message |
| CreatedAt | DateTime | Required | Record creation timestamp |

### Additional Entities

#### Holiday
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayId | UUID | PK | Unique holiday identifier |
| HolidayName | String | Required | Holiday name |
| HolidayDate | Date | Required | Holiday date |
| IsObserved | Boolean | Required | Is observed holiday |
| IsFloating | Boolean | Required | Is floating holiday |
| AppliesToAll | Boolean | Required | Applies to all employees |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

#### HolidayAssignment
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| HolidayAssignmentId | UUID | PK | Unique assignment identifier |
| HolidayId | UUID | FK | Reference to Holiday |
| EmployeeId | UUID | FK | Reference to Employee |
| DepartmentId | UUID | FK | Reference to Department |
| JobCodeId | UUID | FK | Reference to JobCode |

#### EmployeeAvailability
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| AvailabilityId | UUID | PK | Unique availability identifier |
| EmployeeId | UUID | FK | Reference to Employee |
| DayOfWeek | Enum | Required | Day of week |
| StartTime | Time | Required | Available start time |
| EndTime | Time | Required | Available end time |
| IsAvailable | Boolean | Required | Availability status |
| EffectiveDate | Date | Required | Effective date |
| ExpiryDate | Date | Optional | Expiry date |
| CreatedAt | DateTime | Required | Record creation timestamp |
| UpdatedAt | DateTime | Required | Record last update timestamp |

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
