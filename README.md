# 🏠 Airbnb Redundant Listings — Revenue Recovery Analysis

## 📌 Project Overview

Airbnb has a large and diverse marketplace of accommodation listings. However, a significant percentage of listings become **redundant**, meaning they receive **zero bookings for an extended period**, resulting in underutilized inventory and potential revenue loss.

This project focuses on identifying the characteristics of redundant listings and analyzing whether **listing image count** is associated with booking performance.

> **Business Context:** Airbnb Total Revenue — approximately **$9.917B**

### 🎯 Business Objective

Reduce the percentage of redundant listings by:

* Identifying listings with zero bookings over the past year.
* Understanding factors associated with low booking activity.
* Investigating the relationship between **image count and bookings**.
* Identifying opportunities for pricing, reviews, location, and listing-quality improvements.
* Designing a pilot strategy to reactivate underperforming listings.

---

# 🔎 Problem Statement

The Revenue Team identified that approximately **20–30% of listings had not received a booking in over a year**.

These redundant listings can create:

* Revenue leakage
* Increased operational costs
* Underutilized marketplace inventory
* Reduced host engagement
* Poor marketplace efficiency
* Oversupply of inactive properties

The Analytics Team was asked to determine **why certain listings receive fewer bookings and how Airbnb could potentially recover lost revenue.**

---

# 💡 Key Hypothesis

> **Listings with fewer images are more likely to become redundant because insufficient visual information can reduce customer confidence and booking intent.**

The analysis therefore investigates:

**Image Count → Booking Frequency → Redundancy Risk**

Listings were grouped according to the number of images associated with each property and compared against booking activity.

---

# 🧩 Data Sources

The analysis combines four datasets:

| Dataset      | Purpose                                                           |
| ------------ | ----------------------------------------------------------------- |
| **Listings** | Listing information such as Listing ID, posting date and location |
| **Images**   | Number of images associated with each listing                     |
| **Bookings** | Booking activity and booking dates                                |
| **Hosts**    | Host information and host type                                    |

### 🔗 Data Relationships

```text
                 ┌─────────────┐
                 │   Listings  │
                 └──────┬──────┘
                        │
             ┌──────────┼──────────┐
             │          │          │
             ▼          ▼          ▼
        ┌────────┐ ┌──────────┐ ┌────────┐
        │ Images │ │ Bookings │ │ Hosts  │
        └────────┘ └──────────┘ └────────┘
```

The datasets were joined using the relevant **Listing ID / Host ID relationships** to create an analytical master dataset.

---

# 🛠️ Tools & Technologies

### Data Analysis

* **SQL**
* **Python**
* **Pandas**

### Database

* **PostgreSQL**
* SQL joins
* Aggregations
* Date transformations
* Conditional logic

### Analytics

* Data transformation
* Exploratory Data Analysis
* KPI analysis
* Segmentation
* Hypothesis testing
* Revenue opportunity analysis

### Reporting / Visualization

* Power BI / Dashboarding
* Charts and KPI reporting

### Development

* Git
* GitHub
* README documentation

---

# 🔄 Project Workflow

```text
Raw Airbnb Data
       ↓
Data Exploration
       ↓
Data Quality Assessment
       ↓
Listings + Images + Bookings + Hosts
       ↓
Master Analytical Dataset
       ↓
Business Rules
       ↓
Listing Segmentation
       ↓
Image Count Analysis
       ↓
Booking Performance Analysis
       ↓
Redundant Listing Identification
       ↓
Insights & Recommendations
       ↓
Pilot Strategy
       ↓
Revenue Recovery Opportunity
```

---

# 🧠 Problem-Solving Approach

## 1. Understand the Raw Data

The first step was to understand the structure and relationships between the datasets.

Key areas examined:

* Number of listings
* Listing posting dates
* Listing locations
* Number of images
* Booking history
* Host type
* Booking dates
* Data completeness
* Duplicate records
* Relevant keys for joining datasets

---

## 2. Define Business Logic

Listings were classified based on booking activity and image availability.

### Redundant Listing Definition

A listing is considered **redundant** when:

```text
No booking received during the previous year
```

The analysis then compared redundant and active listings based on:

* Image count
* Booking frequency
* Location
* Host characteristics
* Pricing competitiveness
* Reviews and ratings

---

# 🗄️ SQL Data Transformation

A master analytical table was created by joining the major Airbnb datasets.

Example transformation logic:

```sql
SELECT
    l.Listing_Id,
    l.Posting_Date,
    l.Posting_Time,
    l.Location,
    COUNT(DISTINCT i.Image_Id) AS Image_Count,
    COUNT(DISTINCT b.Booking_Id) AS Booking_Count,
    h.Host_Type,
    EXTRACT(YEAR FROM l.Posting_Date) AS Year,
    EXTRACT(MONTH FROM l.Posting_Date) AS Month,
    EXTRACT(DAY FROM l.Posting_Date) AS Day
FROM Airbnb.Listings l

LEFT JOIN Airbnb.Images i
    ON l.Listing_Id = i.Listing_Id

LEFT JOIN Airbnb.Bookings b
    ON l.Listing_Id = b.Listing_Id

LEFT JOIN Airbnb.Hosts h
    ON l.Listing_Id = h.Listing_Id

GROUP BY
    l.Listing_Id,
    l.Posting_Date,
    l.Posting_Time,
    l.Location,
    h.Host_Type
ORDER BY
    l.Listing_Id;
```

### Why `LEFT JOIN`?

`LEFT JOIN` was important because listings with:

* No images
* No bookings
* No host activity

should not automatically disappear from the analysis.

These listings may represent exactly the **underperforming inventory** we want to investigate.

---

# 📊 Image Count Segmentation

Listings were grouped according to the number of associated images.

Example segmentation:

| Image Count | Segment               |
| ----------: | --------------------- |
|         0–5 | Low Image Coverage    |
|        6–10 | Medium Image Coverage |
|         11+ | High Image Coverage   |

For each group, the analysis calculated:

* Number of listings
* Total bookings
* Average bookings per listing
* Redundant listing count
* Redundancy percentage

---

# 🐍 Python Analysis

Python/Pandas was used to perform additional transformation and analysis.

Example:

```python
import pandas as pd
from datetime import datetime

df['Posting_Date'] = pd.to_datetime(df['Posting_Date'])

current_date = datetime.now().date()

df['days_open'] = (
    current_date - df['Posting_Date'].dt.date
).dt.days

df['Image_Group'] = pd.cut(
    df['Image_Count'],
    bins=[-1, 5, 10, float('inf')],
    labels=['0-5', '6-10', '11+']
)

summary = (
    df.groupby('Image_Group')
      .agg(
          Listings=('Listing_Id', 'count'),
          Average_Bookings=('Booking_Count', 'mean')
      )
      .reset_index()
)

print(summary)
```

This enabled comparison of listing performance across image-count groups.

---

# 📈 Key Insight

## Hypothesis: ✅ TRUE

The analysis indicates that:

> **Listings with fewer images are more likely to experience redundancy and lower booking activity.**

Listings with limited visual content may provide insufficient information for potential guests, increasing uncertainty and reducing booking intent.

### Business Interpretation

More images can potentially improve:

* Listing attractiveness
* Customer confidence
* Property transparency
* Search-to-booking conversion
* Overall listing competitiveness

However, **image count should not be treated as the only causal factor**. Pricing, location, reviews, property type, availability, and listing quality should also be evaluated before implementing a broad marketplace intervention.

---

# 🔍 Highlight Insights

### 1. Image Count Matters

Listings with insufficient images showed a stronger association with low booking activity.

**Potential action:** Encourage hosts to upload a more complete set of high-quality property images.

---

### 2. Redundant Listings Represent Revenue Opportunity

Listings with no bookings for an extended period represent **underutilized marketplace inventory**.

Rather than treating these listings only as inactive inventory, Airbnb can identify which listings have realistic potential for reactivation.

---

### 3. Pricing Should Be Investigated

An attractive listing can still remain redundant if its price is significantly higher than comparable properties.

Potential analysis:

```text
Listing Price
       ↓
Comparable Listings
       ↓
Price Difference %
       ↓
Booking Performance
```

---

### 4. Reviews Provide Social Proof

Listings with few or no reviews may have lower customer trust.

Potential intervention:

* Improve guest experience
* Encourage legitimate review collection
* Improve listing transparency
* Highlight verified property information

---

### 5. Location Is an Important Segmentation Factor

Listing performance should be evaluated against **local market conditions**.

A listing with few bookings may be affected by:

* Low tourist demand
* Poor accessibility
* Market saturation
* Seasonal demand
* Competition from nearby listings

---

# ⚠️ Challenging Parts

## 1. Joining Multiple Datasets

Combining Listings, Images, Bookings, and Hosts required careful handling of one-to-many relationships.

A direct join between Images and Bookings can create row multiplication.

For example:

```text
1 Listing
 ├── 10 Images
 └── 5 Bookings
```

A naive join can produce:

```text
10 × 5 = 50 rows
```

Therefore, using:

```sql
COUNT(DISTINCT Image_Id)
COUNT(DISTINCT Booking_Id)
```

is important to avoid inflated metrics.

---

## 2. Identifying Redundant Listings

The definition of redundancy required a clear time-based business rule.

The analysis needed to distinguish between:

* New listings
* Temporarily inactive listings
* Seasonal listings
* Permanently underperforming listings
* Listings with zero bookings for more than one year

---

## 3. Handling Listings With No Images

Listings without images are analytically important because they may be strong candidates for improvement.

Therefore, the transformation logic needed to preserve these records instead of filtering them out.

---

## 4. Separating Correlation From Causation

The analysis shows an association between image count and booking performance.

However:

> More images do not automatically cause more bookings.

Other variables such as price, location, ratings, property type, availability, and demand may influence the outcome.

This is why the project recommends a **pilot experiment** before implementing the strategy across the entire marketplace.

---

# 👩‍💻 Responsibilities

As the Data Analyst, responsibilities included:

* Explored raw Airbnb datasets.
* Identified relevant tables and relationships.
* Defined business rules for redundant listings.
* Joined Listings, Images, Bookings, and Hosts datasets.
* Created SQL transformation queries.
* Applied aggregations and conditional logic.
* Calculated image counts and booking counts.
* Segmented listings based on image availability.
* Used Python/Pandas for additional data transformation.
* Analyzed booking behavior across image-count groups.
* Validated the business hypothesis.
* Identified potential revenue recovery opportunities.
* Developed actionable recommendations.
* Designed a pilot strategy for testing listing improvements.
* Prepared analytical outputs for dashboard/reporting.

---

# 🚀 Pilot Strategy

A controlled pilot can be used to determine whether improving redundant listings increases bookings.

## Step 1 — Select Pilot Listings

Select approximately **10–20% of redundant listings**.

Prioritize listings with:

* Low image count
* Competitive location
* Reasonable pricing potential
* Existing reviews
* Active hosts
* Sufficient availability

---

## Step 2 — Create Test Groups

```text
Redundant Listings
        ↓
   ┌────┴────┐
   ↓         ↓
Control    Treatment
Group       Group
   ↓         ↓
No Change   Improvements
             ↓
      Images + Pricing
             ↓
       Booking Impact
```

---

## Step 3 — Treatment Strategy

Potential improvements:

### 📷 Images

* Increase image count
* Improve image quality
* Add photos of key rooms and amenities
* Use professional photography where feasible

### 💰 Pricing

* Compare against nearby competitors
* Identify overpriced listings
* Test targeted discounts
* Adjust prices during low-demand periods

### ⭐ Reviews

* Improve guest experience
* Encourage legitimate reviews
* Address recurring negative feedback

### 📝 Listing Content

* Improve listing title
* Improve description
* Highlight important amenities
* Improve property information

---

# 📏 Pilot KPIs

The pilot should track:

| KPI             | Purpose                                |
| --------------- | -------------------------------------- |
| Booking Rate    | Measure booking improvement            |
| Total Bookings  | Measure incremental demand             |
| Revenue         | Measure financial impact               |
| Listing Views   | Measure visibility                     |
| Conversion Rate | Measure visitor-to-booking performance |
| Image Count     | Measure content improvement            |
| Average Price   | Monitor pricing changes                |
| Review Score    | Monitor customer perception            |
| Redundancy Rate | Measure inactive listing reduction     |

---

# 📊 Pre vs Post Analysis

Compare performance before and after intervention.

```text
             BEFORE          AFTER
               │               │
               ▼               ▼
          Listing Views    Listing Views
          Bookings         Bookings
          Revenue          Revenue
          Conversion       Conversion
               │               │
               └───────┬───────┘
                       ▼
                 Impact Analysis
```

The treatment group should be compared against a control group to determine whether the interventions generated meaningful incremental bookings.

---

# 💰 Results / Business Impact

### Primary Finding

The analysis validated the hypothesis that:

> **Listings with fewer images are more likely to become redundant.**

### Potential Business Impact

Improving underperforming listings could potentially:

* Reduce redundant inventory
* Increase listing utilization
* Generate incremental bookings
* Improve host engagement
* Increase marketplace efficiency
* Recover otherwise lost revenue
* Improve the guest browsing experience

The project therefore converts a broad revenue problem into a **measurable listing-level optimization strategy**.

---

# 🎯 Recommended Business Actions

### Priority 1 — Target Low-Image Redundant Listings

Identify redundant listings with very low image counts and prioritize them for intervention.

### Priority 2 — Benchmark Pricing

Compare listing prices against similar properties in the same geographic market.

### Priority 3 — Improve Listing Quality

Provide hosts with recommendations around:

* Images
* Titles
* Descriptions
* Amenities
* Reviews

### Priority 4 — Run Controlled Experiments

Don't immediately apply changes to every redundant listing.

Run a controlled pilot first.

### Priority 5 — Build an Automated Monitoring Dashboard

Monitor redundant listings continuously instead of performing the analysis only once.

---

# 📌 Suggested Dashboard

The final dashboard can contain the following sections.

## Executive KPIs

```text
┌─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│ Total Listings  │ Redundant %     │ Total Bookings  │ Revenue         │
├─────────────────┼─────────────────┼─────────────────┼─────────────────┤
│      KPI        │       KPI       │       KPI       │       KPI       │
└─────────────────┴─────────────────┴─────────────────┴─────────────────┘
```

## Recommended Visuals

### 1. Redundant Listings by Image Group

Bar chart:

```text
0–5 Images    █████████████
6–10 Images   ████████
11+ Images    ████
```

### 2. Average Bookings by Image Count

Shows whether booking activity changes as image coverage increases.

### 3. Redundant Listings by Location

Identify geographic markets with high concentrations of inactive listings.

### 4. Price vs Booking Performance

Identify potentially overpriced listings.

### 5. Review Score vs Booking Performance

Analyze whether stronger ratings correlate with higher booking activity.

### 6. Pilot Performance

Compare:

```text
Control Group
      vs
Treatment Group
```

for bookings, conversion, and revenue.

---

# 🏆 Business Takeaway

The project demonstrates how analytics can transform a **revenue leakage problem into an actionable optimization strategy**.

Instead of simply reporting that 20–30% of listings are redundant, the analysis identifies **which characteristics are associated with redundancy** and proposes a measurable approach to improve listing performance.

The strongest initial opportunity identified is:

> **Prioritize redundant listings with low image counts, test image and pricing improvements through a controlled pilot, and scale successful interventions based on incremental booking and revenue impact.**

---

# 📚 Skills Demonstrated

```text
SQL
├── JOINs
├── GROUP BY
├── Aggregations
├── CASE statements
├── Date functions
└── Data transformation

Python
├── Pandas
├── Data cleaning
├── Feature engineering
├── Segmentation
└── Exploratory analysis

Business Analytics
├── Problem definition
├── Hypothesis testing
├── KPI development
├── Root-cause analysis
├── Pilot design
└── Revenue opportunity analysis

BI / Reporting
├── Dashboard design
├── KPI reporting
├── Business storytelling
└── Actionable recommendations
```

---

# 📂 Suggested Repository Structure

```text
airbnb-redundant-listings-revenue-analysis/
│
├── README.md
│
├── data/
│   ├── listings.csv
│   ├── images.csv
│   ├── bookings.csv
│   └── hosts.csv
│
├── sql/
│   ├── master_table.sql
│   ├── redundant_listings.sql
│   └── image_booking_analysis.sql
│
├── python/
│   ├── data_analysis.py
│   └── listing_segmentation.py
│
├── dashboard/
│   └── airbnb_revenue_recovery.pbix
│
├── screenshots/
│   └── dashboard.png
│
└── docs/
    └── business_recommendations.md
```

---

# ⭐ Project Outcome

**Business Problem → Data → SQL Transformation → Python Analysis → Hypothesis Validation → Dashboard → Pilot Strategy → Revenue Recovery Opportunity**

This project demonstrates an end-to-end **Data Analytics workflow focused on solving a real-world marketplace revenue problem**, rather than simply creating visualizations.
