# Weather Data Pipeline

## Overview
This project is a **weather data pipeline** that fetches weather information from an external API, processes it, and stores it in a **PostgreSQL** database. It utilizes **FastAPI** for API interactions and **Apache Airflow** for task scheduling.


**Clone the Repository:**
```bash
git clone "https://github.com/Diehareder03/QUANTCO_PROJECT.git"
```

**Note:** We are running this setup using **WSL, PostgreSQL, FastAPI, Airflow**, and coding in **VS Code**.




## Features
- **FastAPI**: Exposes an API to fetch and store weather data.
- **PostgreSQL**: Stores venues and weather data.
- **Airflow**: Automates the data fetching process.
- **Pydantic & SQLAlchemy**: Handles data validation and ORM mapping.

---
## Installation & Setup

### **1. Install Dependencies**
Ensure you have **Python 3.9+** and **PostgreSQL** installed. Then, create a virtual environment:

```bash
python3 -m venv quantco_env
source quantco_env/bin/activate  # On Windows: quantco_env\Scripts\activate
```

Install required Python packages:

```bash
pip install fastapi uvicorn psycopg2-binary sqlalchemy alembic pydantic requests airflow
```

---
### **2. Setup PostgreSQL Database**
**Start PostgreSQL and create a database:**

```sql
CREATE DATABASE weather_db;
```

Switch to the database:

```bash
\c weather_db;
```

Create the `venues` table:

```sql
CREATE TABLE venues (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    latitude FLOAT,
    longitude FLOAT
);
```

Insert sample venues:

```sql
INSERT INTO venues (name, latitude, longitude) VALUES
('Venue1', 52.52, 13.41),
('Venue2', -30, 153.125),
('Venue3', 44.4375, 26.125);
```

Create the `weather_data` table:

```sql
CREATE TABLE weather_data (
    id SERIAL PRIMARY KEY,
    venue_id INT REFERENCES venues(id),
    timestamp TIMESTAMP,
    temperature FLOAT,
    humidity FLOAT,
    dewpoint FLOAT,
    apparent_temperature FLOAT,
    precipitation_probability FLOAT,
    precipitation FLOAT,
    rain FLOAT,
    showers FLOAT,
    snowfall FLOAT,
    snow_depth FLOAT
);
```

---
### **Project Directory Structure**
Create a directory named `api/` and add the following files:

```
weather_pipeline/
│── airflow/
│   ├── dags/weather_dag.py
│── api/
│   ├── main.py
│   ├── schemas.py
│   ├── services.py
│   ├── database.py
│── requirements.txt
```

#### **Start Airflow Services**
```bash
airflow webserver --port 8080 &
airflow scheduler &
```

---
## **Usage**
1. **Start FastAPI**: `uvicorn api.main:app --host 127.0.0.1 --port 9000 --reload`
2. **Manually Fetch Weather Data**: Use Postman or cURL:
   ```bash
   curl -X POST "http://127.0.0.1:9000/fetch-weather/" -H "Content-Type: application/json" -d '{"venue_id": 1, "start_date": "2025-02-25", "end_date": "2025-02-26"}'
   ```
3. **Check Airflow UI**: Open `http://127.0.0.1:8080` and trigger `fetch_weather_dag`.

---
## **Troubleshooting**
- **Port 5432 already in use?** Stop any running PostgreSQL instance.
- **Database connection errors?** Ensure PostgreSQL is running and credentials are correct.
- **FastAPI server not starting?** Activate the virtual environment before running `uvicorn`.

---
## **Additional Setup and Commands**

### **Check and Initialize PostgreSQL**
```bash
sudo service postgresql start
psql --version
psql -h localhost -U postgres -d weather_db
\dt
SELECT * FROM venues;
SELECT * FROM weather;
\dt weather;
```

### **Running Services in Different Terminals**
- **Terminal 1**: `uvicorn api.main:app --host 127.0.0.1 --port 9000 --reload`
  - Access API at: [http://127.0.0.1:9000/docs](http://127.0.0.1:9000/docs)
- **Terminal 2**: `airflow webserver --port 8080`
  - Access Airflow UI at: [http://localhost:8080/](http://localhost:8080/)
- **Terminal 3**: `airflow scheduler`

### **Running All Services in Background**
```bash
uvicorn api.main:app --host 127.0.0.1 --port 9000 --reload &
airflow webserver --port 8080 -D
airflow scheduler -D
```

### **Handling Airflow Issues**
```bash
airflow db init  # Reinitialize DB
airflow db upgrade  # Upgrade DB schema
airflow webserver --port 8080 -D
airflow scheduler -D
```

### **Check Airflow Status**
```bash
ps aux | grep airflow
```

### **Creating an Airflow Admin User**
```bash
airflow users create \
    --username airflow \
    --firstname airflow \
    --lastname airflow \
    --role Admin \
    --email airflow@gmail.com \
    --password airflow
```

### **Final Verification**
After setup, manually trigger DAGs from the Airflow UI. Ensure FastAPI, Airflow webserver, and scheduler are running.
```sql
SELECT * FROM weather;  -- To verify weather data
SELECT * FROM weather LIMIT 10;
```

---
## **Conclusion**
This project efficiently collects and stores weather data using FastAPI, PostgreSQL, and Apache Airflow. 🚀
