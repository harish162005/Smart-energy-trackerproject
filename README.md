# Smart Energy Tracker

A Java Spring Boot application that tracks daily electricity usage, predicts the bi-monthly TNEB (Tamil Nadu) bill, and raises alerts when usage suddenly spikes.

## Problem
In Tamil Nadu, electricity bills come once in two months, so a sudden rise in usage is noticed only when the bill arrives. This project tracks usage daily, predicts the bill early, and warns about abnormal days.

## Features
- Register and login (passwords stored as BCrypt hashes)
- Add meters and daily readings
- Bill calculation using TNEB slab rates (rates stored in a database table, not hard-coded)
- Bill prediction for the 60-day billing cycle
- Spike alerts when a day's usage is more than 1.5x the recent average
- Smart meter simulator that generates readings
- Dashboard with usage graph, predicted bill and alerts
- Duplicate checks (one reading per meter per date, one alert per date)

## Screenshots

### Login
![Login page](Screenshots/login.png.png)

### Dashboard
![Dashboard cards](Screenshots/dashboard1-cards.png.png)
![Daily usage graph](Screenshots/dashboard2-graph.png.png)
![Alerts](Screenshots/dashboard3-alert.png.png)

## Tech Stack
- Java, Spring Boot 4.1.1, Spring Data JPA
- MySQL 8
- HTML, CSS, JavaScript, Chart.js
- Maven, Postman, IntelliJ IDEA

## How it works
- **Bill:** units are charged slab by slab, using the rates in the `tariff_slabs` table. Calculated amounts were checked against real EB bills for the 200-400 unit range.
- **Prediction:** average daily usage so far x 60 days, then passed to the bill calculation.
- **Spike detection:** the latest reading is compared with the average of the previous 7 readings.

## Project Structure
```
controller  - REST APIs
service     - bill, prediction and spike logic
repository  - database access (Spring Data JPA)
model       - entities (User, Meter, Reading, Alert, TariffSlab)
config      - tariff data loader
static      - login.html and index.html (dashboard)
```

## Setup
1. Install Java 17 or above and MySQL 8
2. Create the database:
   ```sql
   CREATE DATABASE energy_tracker;
   ```
3. Go to `src/main/resources`, copy `application.properties.example` and rename the copy to `application.properties`
4. Open it and set your MySQL password
5. Open the project in IntelliJ IDEA and run `EnergytrackerApplication`
6. Open `http://localhost:8080/login.html`, register, then login

## Main APIs
| Method | URL | Purpose |
|---|---|---|
| POST | `/api/auth/register` | Register |
| POST | `/api/auth/login` | Login |
| POST | `/api/meters` | Add meter |
| POST | `/api/readings` | Add daily reading |
| GET | `/api/readings/meter/{id}` | Readings of a meter |
| GET | `/api/bills/calculate?units=283` | Calculate bill |
| GET | `/api/predictions/meter/{id}?cycleStart=2026-10-10` | Bill prediction |
| POST | `/api/alerts/check/meter/{id}` | Check for spike |
| GET | `/api/alerts/meter/{id}` | List alerts |
| POST | `/api/simulator/meter/{id}?days=7` | Generate sample readings |

## Limitations and future work
- Slab rates for usage above 500 units are placeholder values and need to be verified with official TNPDCL tariff.
- Login is basic (no token). Next improvement: JWT authentication with Spring Security.
- Add unit tests and a scheduled simulator.

## Author
Harish Kumar N
