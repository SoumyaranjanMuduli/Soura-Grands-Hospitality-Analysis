# 🏨 SouRa Grands — Hospitality Data Analysis

> **Power BI business intelligence project** for SouRa Technologies — a 3-page interactive dashboard analyzing revenue, occupancy, ratings, and booking performance across 7 hotel properties in 4 Indian cities.

---

## 📌 Project Overview

SouRa Grands is a luxury hotel chain operating across **Mumbai, Bangalore, Hyderabad, and Delhi**. This project builds a full analytics suite to help management answer:

- Which properties are driving the most revenue — and which are underperforming?
- How does occupancy vary by city, week, and day type?
- Which booking platforms bring in the most revenue?
- Where is revenue being lost to cancellations?
- How do guest ratings vary across properties and room classes?

The solution is a **3-page Power BI dashboard** built on a star schema data model with 5 tables.

---

## 🗂️ Repository Structure

```
📦 soura-hospitality-analysis
 ┣ 📊 ~$metrics list.xlsx              # Calculated metrics reference
 ┣ 📊 dim_date.csv                     # Date dimension table
 ┣ 📊 dim_hotels.csv                   # Hotel/property master data
 ┣ 📊 dim_rooms.csv                    # Room category master data
 ┣ 📊 fact_aggregated_bookings.csv     # Aggregated booking facts
 ┣ 📊 fact_bookings.csv                # Transactional booking facts
 ┣ 📄 meta_data_hospitality.xlsx       # Data dictionary
 ┣ 🖼️ mock up dashboard_soura grands   # Dashboard wireframe/mockup
 ┗ 📄 README.md
```

---

## 🏗️ Data Model (Star Schema)

```
                    ┌─────────────┐
                    │  dim_date   │
                    └──────┬──────┘
                           │
┌────────────┐    ┌────────┴────────┐    ┌──────────────────────────┐
│ dim_hotels │────│  fact_bookings  │────│ fact_aggregated_bookings  │
└────────────┘    └────────┬────────┘    └──────────────────────────┘
                           │
                    ┌──────┴──────┐
                    │  dim_rooms  │
                    └─────────────┘
```

| Table | Type | Description |
|---|---|---|
| `dim_hotels` | Dimension | Property name, city, category |
| `dim_rooms` | Dimension | Room class (Elite, Premium, Presidential, Standard) |
| `dim_date` | Dimension | Date, week number, day type (Weekend/Weekday) |
| `fact_bookings` | Fact | Individual booking transactions with status |
| `fact_aggregated_bookings` | Fact | Pre-aggregated capacity and booking counts |

---

## 📊 Dashboard Pages

### Page 1 — Overall Analysis (Home Dashboard)

**KPI Cards**
- Total Bookings: **135K**
- Total Revenue: **₹2 Billion**
- Occupancy Rate: **57.87%**
- Average Rating: **3.62 / 5.00**

**Visuals**
- Revenue split by city (bar chart)
- Occupancy % by city (bar chart)
- Average rating by city (bar chart)
- Occupancy % and Avg Rating trend by week number (dual-axis line chart)
- Property by key metrics table (Revenue · Rating · Occupancy · Bookings)
- Occupancy % by day type — Weekend vs. Weekday (donut chart)
- Occupancy % by booking platform (horizontal bar chart)

**Slicers**: Property Name · City · Booking Platform · Booking Status · Week Number · Month

---

### Page 2 — Revenue Analysis

**Visuals**
- Revenue by Month: May ₹581.93M → July ₹572.91M → June ₹553.93M
- Revenue by City: Mumbai ₹668.64M leads, Delhi at ₹294.50M
- Revenue by Weekday type: Weekday ₹1,184.81M vs. Weekend ₹523.96M
- Revenue by Property: SouRa Exotica leads at ₹320.31M
- Revenue by Platform: "Others" channel dominates at ₹699.35M; MakeYourTrip at ₹340.83M
- Revenue lost to cancellations by property (SouRa Exotica leads at ₹54.40M)
- Revenue by Month & Date (daily trend line — May to July)

---

### Page 3 — Ratings & Bookings Analysis

**KPI Cards** (same top row for context)

**Visuals**
- Rating by Property: SouRa Blu highest at 3.96; SouRa Seasons lowest at 2.29
- Average Rating by Room Class month-wise (line chart — Elite, Premium, Presidential, Standard across May/June/July)
- Booking count by Room Class: Elite 49.51K · Standard 38.45K · Premium 30.57K · Presidential 16.07K
- Bookings vs. Capacity by property (table): SouRa Exotica capacity 40,940 vs. 23,441 bookings
- Total Bookings and Cancelled Bookings by date (dual time-series: May–July 2022)

---

## 💡 Key Insights

**Revenue**
- Mumbai alone generates **₹668.64M** — more than Hyderabad and Delhi combined
- Weekdays generate **2.26x more revenue** than weekends — counter-intuitive for a hotel chain; likely driven by corporate travel
- "Others" aggregated channel brings the most revenue, but **MakeYourTrip is the top named platform** at ₹340.83M
- SouRa Exotica is the top earner at ₹320.31M but also loses the most to cancellations (₹54.40M)

**Occupancy**
- Average occupancy across all properties: **57.87%** — significant room to grow
- Delhi has the highest occupancy at **60.55%** despite lowest revenue — suggests lower room rates
- SouRa Seasons is the worst performer: **44.62% occupancy, 2.29 rating, ₹66M revenue**
- Weekend occupancy is marginally higher (52.8%) than weekday (47.2%)

**Ratings**
- SouRa Blu leads with **3.96** — only property approaching a 4.0 average
- SouRa Seasons at **2.29** is a significant outlier and operational risk
- Room class ratings are nearly flat across months (3.58–3.69) — no seasonal improvement visible
- Elite room class books the most (49.51K) but Presidential has the lowest volume (16.07K)

**Bookings**
- Cancellation volume closely tracks total booking volume — pattern visible in the daily time-series
- SouRa Palace has the best capacity utilization relative to its property size

---

## 🔧 Tools Used

| Tool | Purpose |
|---|---|
| **Power BI Desktop** | Data modeling, DAX measures, dashboard |
| **Microsoft Excel / CSV** | Source data files |
| **DAX** | Custom KPI measures (Revenue, Occupancy %, Avg Rating, Cancellations) |

---

## 📐 Key DAX Measures

```dax
-- Total Revenue
Total Revenue = SUM(fact_bookings[revenue_realized])

-- Occupancy %
Occupancy % = 
DIVIDE(
    SUM(fact_aggregated_bookings[successful_bookings]),
    SUM(fact_aggregated_bookings[capacity])
)

-- Average Rating
Avg Rating = AVERAGE(fact_bookings[ratings_given])

-- Revenue Lost (Cancellations)
Revenue Lost = 
CALCULATE(
    SUM(fact_bookings[revenue_generated]) - SUM(fact_bookings[revenue_realized])
)

-- Total Cancelled Bookings
Cancelled Bookings = 
CALCULATE(
    COUNT(fact_bookings[booking_id]),
    fact_bookings[booking_status] = "Cancelled"
)
```

---

## 🚀 How to Use

```bash
# Clone the repo
git clone https://github.com/yourusername/soura-hospitality-analysis.git
```

1. Open **Power BI Desktop**
2. Click **Get Data → Text/CSV** → load all 5 data files
3. Go to **Model View** → connect tables as shown in the star schema above
4. Import the DAX measures from the metrics list
5. Build visuals as shown across 3 report pages

---

## 👤 Author

**Soumya Ranjan Muduli**  
[LinkedIn](https://linkedin.com/in/yourprofile) · [Portfolio](https://yourportfolio.com) · [Email](mailto:you@email.com)

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

*Built as part of the Codebasics Data Analytics Bootcamp. Data belongs to SouRa Technologies and is used for educational purposes only.*
