# ASG Airlines — Data Engineering & Analytics

## 1. Project Overview

This project implements an end-to-end data engineering and analytics pipeline for ASG Airlines. The pipeline processes flight, booking, passenger, and payment data containing realistic data-quality challenges such as missing values, duplicate identifiers, inconsistent time-related records, and incomplete payment information.

The processed data is transformed into analytical datasets and presented through an interactive Power BI dashboard for operational and business analysis.

## 2. Objectives

* Ingest and validate airline datasets.
* Identify and handle data-quality issues.
* Standardize flight and booking information.
* Detect duplicate and ambiguous identifiers.
* Protect personally identifiable information (PII).
* Generate airline, route, duration, booking, payment, and anomaly KPIs.
* Build an interactive Power BI dashboard.
* Produce reusable analytical datasets for reporting.

## 3. Data Sources

The project uses four primary datasets:

* Flights
* Bookings
* Passengers
* Payments

The data represents airline operational and transactional information.

## 4. Data Engineering Workflow

The implemented workflow is:

**Raw Data → Data Ingestion → Data Quality & Validation → Cleaning & Standardization → PII Protection → Analytics & KPI Layer → Power BI Dashboard**

The workflow includes:

1. Loading the raw datasets using Python and Pandas.
2. Inspecting schemas, data types, missing values, duplicates, and identifiers.
3. Validating flight IDs and temporal consistency.
4. Checking relationships between bookings, flights, passengers, and payments.
5. Creating analytical features such as route, flight duration in minutes, departure/arrival dates, departure/arrival hours, and overnight-flight indicators.
6. Aggregating payment information at booking level.
7. Identifying payment-status inconsistencies.
8. Protecting passenger-related PII using hashing and masking.
9. Generating KPI and performance tables.
10. Exporting analytical datasets as CSV files for Power BI.

## 5. Data Quality & Validation

The pipeline performed several quality checks.

### Flight data

* 1,005 flight records were processed.
* No missing values were identified in the flight dataset.
* Flight duration was validated against the difference between arrival and departure timestamps.
* 1 duplicate flight ID (`6F250`) was identified.
* The duplicate ID represented two distinct flight records with different routes and timings.
* Both records were retained in the raw flight data.
* Because the duplicate ID could not be safely mapped to a unique flight record, the affected booking records were flagged as having no reliable flight match.

### Booking data

* 1,000 booking records were processed.
* 75 booking-status values were missing.
* No duplicate booking IDs were identified.
* Booking timestamps were checked against flight departure times.

### Payment data

* Payment records were aggregated at booking level.
* Missing payment amounts were identified.
* Payment-related inconsistencies were classified into meaningful categories.

### Referential integrity

Relationships between bookings, flights, passengers, and payments were validated, with no unmatched foreign-key references identified during the final audit.

## 6. PII Protection

Passenger-sensitive information was not included in the protected analytical dataset in its original form.

The following transformations were applied:

* Passport numbers → SHA-256 hashes.
* Emergency contact names → partially masked values.
* Emergency contact phone numbers → masked values with only the final four digits retained.

The protected dataset contains no raw passport numbers, emergency contact names, or emergency contact phone numbers.

## 7. Feature Engineering

The flight analytics layer includes:

* Flight duration in minutes
* Route
* Departure date
* Arrival date
* Departure hour
* Arrival hour
* Overnight-flight indicator

A flight is classified as overnight when its arrival date differs from its departure date.

## 8. Key KPIs

| KPI                         |          Value |
| --------------------------- | -------------: |
| Total Flights               |          1,005 |
| Total Bookings              |          1,000 |
| Average Flight Duration     | 164.62 minutes |
| Overnight Flights           |            122 |
| Overnight Flight Percentage |         12.14% |
| Cancelled Bookings          |            314 |
| Cancellation Rate           |         31.40% |
| Payment Issue Bookings      |            369 |
| Unknown Airline Flights     |             69 |
| Ambiguous Flight Bookings   |              2 |

## 9. Airline Analysis

The airline performance dataset provides:

* Flight count
* Average flight duration
* Overnight flight count
* Overnight flight percentage

Airlines represented in the processed dataset include IndiGo, SpiceJet, Air India, Vistara, and records with an unknown airline value.

## 10. Route Analysis

The route performance dataset provides:

* Flight traffic by route
* Average flight duration by route
* Overnight flight percentage by route

There are 30 distinct routes in the analytical dataset.

The highest-volume route is:

**BOM → CCU — 90 flights**

## 11. Operational Anomaly Analysis

The project identifies operational and data-quality anomalies rather than claiming to calculate flight delays, since the available dataset does not contain scheduled-versus-actual timing fields.

Identified anomaly categories include:

* Unknown airline
* Payment status issues
* Missing payment amounts
* Ambiguous flight IDs

## 12. Power BI Dashboard

The Power BI report contains five interactive pages:

### Overview

Provides a high-level view of:

* Total flights
* Total bookings
* Average flight duration
* Overnight flights
* Cancellation rate
* Booking status distribution
* Airline distribution
* Route traffic
* Overnight versus same-day flights
* Payment-status issues

### Duration Analysis

Provides:

* Average flight duration by airline
* Average flight duration by route
* Overnight-flight percentage by airline
* Airline filtering

### Route Performance

Provides:

* Flight traffic by route
* Average duration by route
* Overnight-flight percentage by route
* Route filtering

### Airline Trends

Provides:

* Flight count by airline
* Average flight duration by airline
* Overnight-flight percentage by airline
* Airline filtering

### Anomaly Insights

Provides:

* Operational anomalies by type
* Booking payment-status issues
* Payment issue KPI
* Anomaly summary
* Airline filtering

## 13. Important Assumptions

### Duplicate flight ID

The flight ID `6F250` occurs twice but represents two different flight records. Since the booking data references only the flight ID and does not contain an additional identifier capable of distinguishing the two records, the duplicate records were retained in the raw dataset but excluded from the reliable flight lookup used for booking-level integration.

The affected bookings are explicitly flagged rather than being assigned an arbitrary flight record.

### Missing booking status

Missing booking statuses were retained as missing values rather than being artificially assigned a booking status.

### Flight delays

The dataset does not contain scheduled and actual departure/arrival timestamps. Therefore, the project reports operational and data-quality anomalies rather than calculating a flight-delay metric.

## 14. Project Structure

```text
ASG-Airlines-Data-Engineering-Analytics/
│
├── data/
│   ├── ASG_protected_booking_analytics.csv
│   ├── ASG_flight_analytics.csv
│   ├── ASG_airline_performance.csv
│   ├── ASG_route_performance.csv
│   ├── ASG_operational_anomalies.csv
│   └── ASG_kpi_summary.csv
│
├── notebook/
│   └── <completed Jupyter notebook>
│
├── dashboard/
│   └── ASG_Airlines_Analytics_Dashboard.pbix
│
├── documentation/
│   └── ASG_Airlines_Data_Engineering_Workflow.png
│
└── screenshots/
    ├── 01_Overview.png
    ├── 02_Duration_Analysis.png
    ├── 03_Route_Performance.png
    ├── 04_Airline_Trends.png
    └── 05_Anomaly_Insights.png
```

## 15. Technologies Used

* Python
* Pandas
* Jupyter Notebook
* Power BI
* Git / GitHub
* SHA-256 hashing for PII protection

## 16. Limitations

* The available dataset does not provide scheduled-versus-actual flight timings, so true flight-delay analysis is not included.
* The duplicate `6F250` flight ID cannot be uniquely resolved at booking level.
* Some booking statuses are missing in the source data.
* The analytical datasets are generated locally and consumed by Power BI.

## 17. Conclusion

The project demonstrates a complete airline data engineering and analytics workflow, from raw data validation and cleaning through privacy protection, analytical transformation, KPI generation, and interactive business intelligence reporting.

The resulting pipeline provides a structured and reproducible foundation for airline operational analysis while explicitly documenting data-quality limitations and privacy considerations.
