# Bike Share Analysis Documentation
**Generated:** February 14, 2026

---

## Overview

The Bike Share Analysis Dashboard provides a comprehensive view of ride activity across rider types, bike categories, time periods, and usage patterns.

The purpose of this dashboard is to:
- Monitor total ride performance
- Understand rider behaviour (Member vs Casual)
- Identify seasonal and daily usage trends
- Evaluate bike type performance
- Support operational and strategic decision-making

---

## Model Statistics
- **Total Tables:** 6
- **Total Columns:** 34 (across all tables)
- **Total Measures:** 1
- **Total Relationships:** 3
- **Last Modified:** February 8, 2026, 01:01 AM

---

## Table Structure

### 1. **Bikeshare** (Primary Fact Table)
**Type:** Data Table  
**Purpose:** Core bike-sharing transaction data  
**Columns:** 8  
**Partitions:** 1

#### Columns:
| Column | Data Type | Format | Summarize | Notes |
|--------|-----------|--------|-----------|-------|
| `ride_id` | String | - | None | Primary identifier for each ride |
| `Bike Type` | String | - | None | Type of bike used (e.g., "Classic Bike", "Electric Bike") |
| `Rider Type` | String | - | None | Membership type (e.g., "Member", "Casual") |
| `Start Date` | DateTime | `dddd, d mmmm yyyy` | None | Date portion of ride start (Key column for relationships) |
| `End Date` | DateTime | `dddd, d mmmm yyyy` | None | Date portion of ride end (Key column for relationships) |
| `Duration` | Integer | `0` | Sum | Ride duration in minutes |
| `Start Time` | DateTime | `h:nn:ss AM/PM` | None | Time portion of ride start |
| `End Time` | DateTime | `h:nn:ss AM/PM` | None | Time portion of ride end |

**Data Source:** Multiple CSV files from folder `C:\Users\User\OneDrive - dammyboss\Power BI\BikeShare\Bikeshare`

---

### 2. **Calendar** (Lookup/Dimension Table)
**Type:** Calculated Table  
**Purpose:** Date dimension for analyzing trends by start date  
**Columns:** 5  
**Partitions:** 1

#### Columns:
| Column | Data Type | Format | Notes |
|--------|-----------|--------|-------|
| `Date` | DateTime | Short Date | Primary key for calendar dimension |
| `MonthNum` | Integer | `0` | Month number (1-12) |
| `Month Name` | String | - | Abbreviated month name (Jan, Feb, etc.) |
| `WeekdayNum` | Integer | `0` | Day of week number (1-7) |
| `WeekDay` | String | - | Abbreviated day name (Mon, Tue, etc.) |

**Source Formula:** `CALENDAR(MIN(Bikeshare[Start Date]),MAX(Bikeshare[Start Date]))`

---

### 3. **LocalDateTable_e8f49519-4f7d-4444-bd8b-e7521cf7ae7b** (Hidden Date Table)
**Type:** Auto-generated Date Table  
**Purpose:** Supporting table for End Date relationships  
**Status:** Hidden (Internal use)  
**Columns:** 7  
**Hierarchy:** Date Hierarchy (Year → Quarter → Month → Day)

---

### 4. **DateTableTemplate_dcf0af77-f3b0-477b-b922-299bb45b6f1b** (Hidden Template)
**Type:** Date Table Template  
**Purpose:** System template for date dimension  
**Status:** Hidden & Private  
**Columns:** 7  
**Hierarchy:** Date Hierarchy (Year → Quarter → Month → Day)

---

### 5. **LocalDateTable_0b5723da-6c32-4237-943e-fa03fab07221** (Hidden Date Table)
**Type:** Auto-generated Date Table  
**Purpose:** Supporting table for Calendar relationships  
**Status:** Hidden (Internal use)  
**Columns:** 7  
**Hierarchy:** Date Hierarchy (Year → Quarter → Month → Day)

---

### 6. **All Maesures** (Measures Table)
**Type:** Utility Table  
**Purpose:** Container for model measures  
**Columns:** 1  
**Measures:** 1

#### Measures:
- **Total Rides** `= COUNT(Bikeshare[ride_id])`
  - Format: Integer (0)
  - Description: Total number of bike rides in the dataset

---

## Relationships

### Relationship 1: Bikeshare → LocalDateTable (End Date)
- **From:** `Bikeshare[End Date]`
- **To:** `LocalDateTable_e8f49519-4f7d-4444-bd8b-e7521cf7ae7b[Date]`
- **Cardinality:** Many-to-One
- **Cross Filtering:** One Direction
- **Join Behavior:** Date Part Only
- **Status:** Active ✓

### Relationship 2: Calendar → LocalDateTable (Date)
- **From:** `Calendar[Date]`
- **To:** `LocalDateTable_0b5723da-6c32-4237-943e-fa03fab07221[Date]`
- **Cardinality:** Many-to-One
- **Cross Filtering:** One Direction
- **Join Behavior:** Date Part Only
- **Status:** Active ✓

### Relationship 3: Bikeshare → Calendar (Start Date)
- **From:** `Bikeshare[Start Date]`
- **To:** `Calendar[Date]`
- **Cardinality:** Many-to-One
- **Cross Filtering:** One Direction
- **Status:** Active ✓

---

## Data Flow Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   CSV Files (Bikeshare)                │
│  C:\Users\User\OneDrive - dammyboss\...\Bikeshare\*.csv │
└────────┬────────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────┐
│      Transform File Query (M)        │
│  - Parse CSV files                  │
│  - Promote headers                  │
│  - Clean column names               │
│  - Extract date/time components     │
└────────┬─────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────┐
│     Bikeshare Table (8 columns)      │
│  - ride_id                          │
│  - Bike Type                        │
│  - Rider Type                       │
│  - Start Date / End Date            │
│  - Duration (Minutes)               │
│  - Start Time / End Time            │
└────────┬──────────────┬──────────────┘
         │              │
         │              ▼
         │     ┌──────────────────────────────┐
         │     │ Calendar Table               │
         │     │ (Calculated from Start Date) │
         │     │ - Date                       │
         │     │ - MonthNum, Month Name       │
         │     │ - WeekdayNum, WeekDay        │
         │     └──────────┬───────────────────┘
         │                │
         │                ▼
         │     ┌──────────────────────────────┐
         │     │ LocalDateTable               │
         │     │ (Support for Calendar)       │
         │     └──────────────────────────────┘
         │
         ▼
    ┌────────────────────┐
    │ All Maesures Table │
    │ - Total Rides      │
    └────────────────────┘
```

---

## Key Metrics & Measures

### Total Rides
- **Formula:** `COUNT(Bikeshare[ride_id])`
- **Purpose:** Counts the total number of unique bike rides
- **Table:** All Maesures

---

## Data Transformations Applied

The Bikeshare data undergoes the following transformations:

1. **File Loading:** CSV files are loaded from the source folder
2. **Column Cleaning:**
   - `rideable_type` → `Bike Type`
   - `started_at` → `Start Date` + `Start Time`
   - `ended_at` → `End Date` + `End Time`
   - `member_casual` → `Rider Type`

3. **Data Type Formatting:**
   - Bike Type: Proper case transformation (e.g., "CLASSIC_BIKE" → "Classic Bike")
   - Dates: Formatted as "dddd, d mmmm yyyy"
   - Times: Formatted as "h:nn:ss AM/PM"

4. **Calculated Columns:**
   - `Duration`: Calculated as the difference between End Date/Time and Start Date/Time (in minutes)
   - `Start Time` & `End Time`: Extracted time-only portions from datetime columns
   - Time precision adjusted to hour boundaries

5. **Data Cleanup:**
   - Station location columns removed (lat/long, station names/IDs)
   - Unnecessary columns filtered out

---

## Culture & Localization

- **Default Culture:** en-US (English - United States)
- **Source Query Culture:** en-GB (English - United Kingdom)
- **Time Intelligence:** Enabled

---

## Model Configuration

### Data Access Options
- **Legacy Redirects:** Enabled
- **Return Error Values as Null:** Enabled
- **Fast Combine:** Disabled
- **Is Empty:** No

### Aggregation & Performance
- **Force Unique Names:** Disabled
- **Discourage Implicit Measures:** Disabled
- **Discourage Report Measures:** Disabled
- **Encourage Composite Models:** Disabled

### Query & Filter Behavior
- **Default Data View:** Full
- **Data Source Default Max Connections:** 10
- **Data Source Variables Override Behavior:** Disallow
- **Direct Lake Behavior:** Automatic
- **Value Filter Behavior:** Automatic
- **Selection Expression Behavior:** Automatic

---

## Security & Row-Level Security (RLS)

Current model has no Row-Level Security (RLS) roles defined. All users with access to the model can view all data.

---

## Annotations & Metadata

### Model-Level Annotations
- `__PBI_TimeIntelligenceEnabled`: 1 (Time Intelligence functions enabled)
- `PBI_QueryOrder`: [Bikeshare, Sample File, Parameter1, Transform Sample File, Transform File, All Maesures]

### Helper Queries & Parameters
- **Parameter1:** Binary parameter pointing to "Sample File" for data transformation functions
- **Sample File:** First file from Bikeshare folder (used as template)
- **Transform Sample File:** CSV parsing query template
- **Transform File:** Parameterized transformation function

---

## Recent Activity

| Event | Date/Time |
|-------|-----------|
| Last Modified | Feb 7, 2026 at 11:58 PM |
| Last Structure Change | Feb 8, 2026 at 1:01 AM |
| Last Processed | Feb 14, 2026 at 8:04 PM |
| Last Update | Feb 14, 2026 at 10:09 PM |
| Last Schema Update | Feb 7, 2026 at 11:58 PM |

---

## Model Statistics

| Metric | Value |
|--------|-------|
| Estimated Size | ~284 MB |
| Model Type | Tabular |
| Compatibility Level | 1600 (Power BI / SSAS 2019+) |
| Number of Partitions | 6 |
| Current State | Unprocessed |

---

## Notes & Recommendations

1. **Data Source:** The model pulls from multiple CSV files. Ensure the source folder path remains accessible.
2. **Performance:** With ~284 MB, consider implementing aggregations if query performance becomes an issue.
3. **Time Intelligence:** The model has time intelligence enabled, allowing for YTD, MTD, and other time-based calculations.
4. **Hidden Tables:** There are several hidden (system-generated) date tables that support the date relationships. These are internal and not meant to be used directly.
5. **Measure Extension:** New measures should be added to the "All Maesures" table (note the typo in original name).

---

## Query Examples

### Example 1: Total Rides by Bike Type
```
= SUMMARIZECOLUMNS(
    Bikeshare[Bike Type],
    "Total Rides", 'All Maesures'[Total Rides]
  )
```

### Example 2: Rides by Month
```
= SUMMARIZECOLUMNS(
    Calendar[Month Name],
    Calendar[MonthNum],
    "Total Rides", 'All Maesures'[Total Rides]
  )
```

### Example 3: Average Duration by Rider Type
```
= AVERAGEX(
    'All Maesures',
    Bikeshare[Duration]
  )
```

---

**Documentation End**  
*This documentation was auto-generated from the Power BI Desktop model.*
