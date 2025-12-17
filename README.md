# Weather Workflow

## Overview
This workflow automates daily weather monitoring, storage, and alerting using **n8n Community Edition**.  
It fetches weather data from **OpenWeatherMap**, enriches it with additional metrics, stores results in **Supabase**, and optionally sends email alerts based on conditions.  

The workflow demonstrates use of **Code nodes** for custom logic, plus branching with **IF/Switch nodes** for alert handling.

---

## 1. API Setup & Key Configuration

### OpenWeatherMap API
- Sign up at [OpenWeatherMap](https://openweathermap.org/api) and generate an API key.
- Insert the key in the Function (Code) node:
  ```js
  const OPENWEATHER_API_KEY = "your_openweather_api_key";
Configure units:

TEMP_UNIT = "metric" → Celsius/km/h

TEMP_UNIT = "imperial" → Fahrenheit/mph

Add cities in the cityList array. Multi‑city support is implemented via looping in the Code node.

Supabase API
Create a Supabase project and obtain your Project URL and API key (Service Role or anon key).

In the Supabase Insert node (or HTTP Request node), configure headers:

http
apikey: <your_supabase_api_key>
Authorization: Bearer <your_supabase_api_key>
This authenticates inserts into your Supabase database.

2. Supabase Details
Create a table (e.g., weather_logs) with columns matching the workflow output:

Code
run_at, city, temperature, temperature_unit, condition, humidity,
wind_speed, wind_direction, cloudiness, feels_like, temp_min, temp_max,
pressure, visibility, sunrise, sunset, rain_1h, snow_1h,
alert_type, summary, raw_response
Types:

numeric → temperatures and precipitation

integer → pressure, visibility, wind direction

text → summary, sunrise, sunset, raw_response

Ensure Row Level Security (RLS) is disabled or policies allow inserts.

3. Email Configuration
Add an Email node (SMTP, Gmail, or other provider).

Configure credentials (SMTP server, port, username/password or OAuth).

Map workflow output:

Subject: include alert_type (e.g., Weather Alert: Frost in Boston)

Body: insert the summary field for a human‑readable daily report

Optional: use an IF node to send emails only when alert_type ≠ none.

4. How to Import & Run the Workflow
Export the workflow JSON from n8n (Deliverable #1).

In n8n:

Go to Workflows → Import from File

Upload the JSON file

Verify connections between nodes (Code → Supabase → Email)

Run manually or schedule with a Cron node for daily execution.

Check:

Supabase table for inserted records

Email inbox for alerts

Workflow execution logs for debugging

5. Node Usage
Code node → custom JavaScript fetches weather data, applies retry logic, enriches with metrics, and outputs JSON

IF/Switch node → optional branching based on alert_type (e.g., frost, precipitation, heat)

Supabase Insert node → stores structured weather logs

Email node → delivers daily summaries and alerts

Deliverables
Exported n8n workflow (.json)

Short README (this document)

Screenshot of the workflow

Notes
Workflow tested on n8n Community Edition (self‑hosted). Equivalent functionality is available on n8n Cloud (14‑day trial).

Multi‑city support is implemented via the cityList array in the Code node, replicating cloud functionality.

Supabase API key and OpenWeatherMap API key must be configured before running.
