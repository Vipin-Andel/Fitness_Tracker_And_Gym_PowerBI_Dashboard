# 🧮 DAX Measures Documentation
## Fitness Gym PowerBI Dashboard - Technical Implementation

### Overview
This document contains all the custom DAX measures implemented in the Fitness Gym PowerBI Dashboard, demonstrating advanced business intelligence capabilities and complex data modeling techniques.

---

## 📊 Core Business Metrics

### Member Analytics
```dax
// Gym Members count 
Users_Count = DISTINCTCOUNT(Users[UserID])

// Maximum Users in Calendar
Max_users = MAXX(ALL('Calendar'[Month],'Calendar'[MonthIndex]), [Users_Count])

// Minimum Users in Calendar
Min_users = MINX(ALL('Calendar'[Month],'Calendar'[MonthIndex]), [Users_Count])
```

### Financial Performance Measures
```dax
// Total Revenue
Revenue = SUM(Payments[Amount])

// Total Expenses
Expanses = SUM(Expenses[Amount])

// Profit Calculation
Profit = [Revenue] - [Expanses]
```

---

## 🎨 Advanced Visualization & Conditional Formatting

### Dynamic Color Coding
```dax
// Conditional User Color based on Min/Max
Con_Users_Color =
VAR MaxUser = [Max_users]
VAR MinUser = [Min_users]
VAR Con_Color = SELECTEDVALUE(ColorCodes[Codes])
VAR Value_Switch = SELECTEDVALUE(Min_Max_Switch[Type])
VAR Min_Color = IF(MinUser = [Users_Count], Con_Color, "gray")
VAR Max_Color = IF(MaxUser = [Users_Count], Con_Color, "gray")
RETURN IF(Value_Switch = "Max", Max_Color, Min_Color)
```

### SVG Progress Bar Visualization
```dax
// SVG Progress Bar Chart
SVG_BarChart1 =
VAR ProgressValue = [ProgressPercent]
VAR BarWidth = 260
VAR ProgressWidth = BarWidth * (ProgressValue / 100)
VAR Select_Color = SELECTEDVALUE(ColorCodes[Codes])
VAR SVG_Data_URL = "data:image/svg+xml;utf8,"
VAR SVG =
    "<svg width='400' height='40' xmlns='http://www.w3.org/2000/svg'>" &
        "<rect x='10' y='10' width='" & BarWidth & "' height='20' rx='10' ry='10' fill='#555' />" &
        "<rect x='10' y='10' width='" & ProgressWidth & "' height='20' rx='10' ry='10' fill='" & Select_Color & "' />" &
        "<text x='360' y='25' font-family='Arial' font-size='25' font-weight='bold' fill='white' text-anchor='end' alignment-baseline='middle'>" &
            ROUND(ProgressValue, 0) & "%" &
        "</text>" &
    "</svg>"
RETURN
    SVG_Data_URL & SVG
```

---

## ⏰ Membership Duration Tracking

### Date Calculations
```dax
// Completed Membership Days
Complete_Days =
DATEDIFF(
    SELECTEDVALUE(Users[MembershipStart]),
    MIN(TODAY(), SELECTEDVALUE(Users[MembershipEnd])),
    DAY
)

// Remaining Membership Days
RemainingDays =
DATEDIFF(
    SELECTEDVALUE(Users[MembershipStart], TODAY()),
    SELECTEDVALUE(Users[MembershipEnd]),
    DAY
)

// Left Days = Remaining - Completed
Left_Days_Count = [RemainingDays] - [Complete_Days]

// Total Membership Days
Total_Days = [Complete_Days] + [Left_Days_Count]

// Progress % of Membership
ProgressPercent = DIVIDE([Complete_Days], [Total_Days], 0) * 100
```

---

## 🏆 Membership Tier Analytics

### Membership Type Counts
```dax
// Membership Type Counts
User_Platinum = CALCULATE([Users_Count], Users[Membership] = "Platinum")
User_Gold = CALCULATE([Users_Count], Users[Membership] = "Gold")
User_Silver = CALCULATE([Users_Count], Users[Membership] = "Silver")
```

### Active/Expired Status Tracking
```dax
// Active Members by Type
Platinum_Active = CALCULATE([User_Platinum], Users[Status] = "Active")
Gold_Active = CALCULATE([User_Gold], Users[Status] = "Active")
Silver_Active = CALCULATE([User_Silver], Users[Status] = "Active")

// Expired Members by Type
Gold_Expired = CALCULATE([User_Gold], Users[Status] = "Expired")
Silver_Expired = CALCULATE([User_Silver], Users[Status] = "Expired")
Platinum_Expired = CALCULATE([User_Platinum], Users[Status] = "Expired")
```

---

## 💪 Health & Fitness Calculators

### BMI Calculation System
```dax
// BMI Calculation
BMI =
VAR WeightKg = SELECTEDVALUE(Weight_Slider[Weight_Slider])
VAR HeightFeet = SELECTEDVALUE(Height_Slider[Height_Slider])
VAR HeightMeters = HeightFeet * 0.3048
RETURN ROUND(WeightKg / (HeightMeters * HeightMeters), 1)

// BMI Color Categories
BMI Color =
VAR BMIValue = [BMI]
RETURN
    SWITCH(
        TRUE(),
        BMIValue < 18.5, "#467AB5",    // Underweight - Blue
        BMIValue < 25, "#77A95C",      // Normal - Green
        BMIValue < 30, "#CD714C",      // Overweight - Orange
        "#C94B4B"                      // Obese - Red
    )

// BMI Category Label
BMI Color Label =
VAR BMIValue = [BMI]
RETURN
    SWITCH(
        TRUE(),
        BMIValue < 18.5, "Underweight",
        BMIValue < 25, "Normal",
        BMIValue < 30, "Overweight",
        "Obese"
    )
```

### Metabolic Rate Calculations
```dax
// Basal Metabolic Rate (BMR)
BMR =
VAR _Weight = SELECTEDVALUE(Weight_Slider[Weight_Slider])
VAR Height = SELECTEDVALUE(Height_Slider[Height_Slider])
VAR Age = SELECTEDVALUE(Age_Slider[Age_Slider])
VAR Gender = SELECTEDVALUE(Slider_Gender[Category])
RETURN
    IF(
        Gender = "Male",
        (10 * _Weight) + (6.25 * Height) - (5 * Age) + 5,
        (10 * _Weight) + (6.25 * Height) - (5 * Age) - 161
    )

// Total Daily Energy Expenditure (TDEE)
TDEE =
VAR BMR_Value = [BMR]
VAR Activity = SELECTEDVALUE(Slider_Activity[ActivityFactor])
RETURN BMR_Value * Activity
```

### Calorie Target Recommendations
```dax
// Calorie Targets
Maintain Calories = [TDEE]
Mild Weight Loss Calories = [TDEE] * 0.92
Weight Loss Calories = [TDEE] * 0.85
Extreme Weight Loss Calories = [TDEE] * 0.70
```

---

## 🔄 System Utilities

### Data Refresh Tracking
```dax
// Last Refresh Time
Last_Refresh = FORMAT(FIRSTNONBLANK(Last_Refresh[Refresh], "None"), "hh:mm AM/PM")
```

---

## 🎯 Technical Highlights

### Advanced DAX Techniques Demonstrated:
- **Variable Usage**: Efficient calculation with VAR statements
- **Conditional Logic**: SWITCH and IF statements for dynamic categorization
- **Date Intelligence**: DATEDIFF functions for temporal analysis
- **Statistical Functions**: MIN, MAX, and aggregation functions
- **SVG Integration**: Custom visual elements with embedded SVG code
- **Color Coding**: Dynamic formatting based on data values
- **Interactive Elements**: Slider integration with SELECTEDVALUE functions

### Business Logic Implementation:
- **Health Standards**: WHO BMI categories and metabolic calculations
- **Financial Analytics**: Profit/loss calculations with real-time updates
- **Membership Management**: Multi-tier analysis with status tracking
- **Progress Tracking**: Percentage calculations with visual representations

---

## 📋 Usage Instructions

### For Developers:
1. Copy the desired DAX measures from this documentation
2. Paste directly into PowerBI Desktop's measure creation interface
3. Ensure all referenced tables and columns exist in your data model
4. Adjust table/column names to match your specific data schema

### For Data Analysts:
- These measures demonstrate advanced PowerBI capabilities
- Use as templates for similar business intelligence requirements
- Modify calculations based on specific business rules and requirements

---

*This comprehensive DAX implementation showcases enterprise-level business intelligence development with advanced visualization and calculation capabilities.*