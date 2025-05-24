# ETL Weather Data Pipeline with Apache Airflow

## Overview

This project implements an Extract, Transform, Load (ETL) pipeline to fetch weather data from a specified source (e.g., OpenWeatherMap API, a public dataset, etc.), process it, and load it into a target system (e.g., a database, data warehouse, or flat files). The pipeline is orchestrated using Apache Airflow and is designed to run within a Dockerized environment managed by the Astro CLI (or can be adapted for other Airflow setups).

**Purpose:**
*To retrieve hourly weather conditions (temperature, humidity, wind speed) for Boston, MA from the OpenWeatherMap API, clean the data, and load it into a PostgreSQL table for trend analysis.*

## Key Features

*   **Data Extraction:** Fetches weather data from open_meteo_api
*   **Data Transformation:** Converts temperature, Flattens nested JSON structures, and Aggregates hourly data into daily averages for temperature.
*   **Data Loading:** Loads the transformed data into  PostgreSQL database.
*   **Scheduled Execution:** Uses Apache Airflow for scheduling and monitoring the ETL pipeline.
*   **Containerized Environment:** Runs within Docker containers for consistency and portability.

## Technologies Used

*   **Orchestration:** Apache Airflow
*   **Containerization:** Docker
*   **Language:** Python
*   **Key Python Libraries:** [e.g., `requests` for API calls, `pandas` for data manipulation, `psycopg2` for Postgres interaction, etc. List what you actually use in your DAGs]

## Project Structure

The project follows a standard Astro CLI/Airflow structure:

*   `dags/`: Contains the Python files for your Airflow DAGs.
    *   `etlweather.py`: This DAG defines the tasks for extracting, transforming, and loading weather data.
*   `Dockerfile`: Defines the Docker image based on Astro Runtime, including any custom dependencies.
*   `include/`: For any additional files or scripts needed by your DAGs (e.g., SQL query files, helper Python modules).
*   `packages.txt`: Lists OS-level packages to be installed in the Docker image.
*   `requirements.txt`: Lists Python packages to be installed apache-airflow-providers-postgres`, `requests`, `pandas`.
*   `airflow_settings.yaml`: For defining Airflow Connections, Variables, and Pools without using the UI.

## Setup and Running the Project Locally

These instructions assume you are using the Astro CLI.

1.  **Prerequisites:**
    *   Docker Desktop installed and running.
    *   Astro CLI installed.

2.  **Clone the Repository:**
    ```
    git clone (https://github.com/Adarsh121005/Weather_ETL/)
    cd ETLWeather
    ```

3.  **Configure Connections/Variables (if needed):**
    *   If your DAG requires API keys or specific connection details (e.g., for the weather API or a target database), define them. You can add them to `airflow_settings.yaml` for local development or set them up via the Airflow UI once it's running.
    *   *Example `airflow_settings.yaml` entry for an API key variable:*
        ```
        airflow:
          connections: []
          variables:
            - variable:
                key: weather_api_key
                value: YOUR_API_KEY_HERE
          pools: []
        ``

4.  **Start Airflow Locally:**
    ```
    astro dev start
    ```
    This command builds the Docker image and starts the Airflow components (Postgres, Webserver, Scheduler, Triggerer).

5.  **Access Airflow UI:**
    *   Open your browser and go to `http://localhost:8080/`.
    *   Log in with username `admin` and password `admin`.
    *   You should see your `etlweather\` listed. You can unpause it and trigger a run.

6.  **Verify Docker Containers:**
    You can check if all containers are running with:
    ```
    docker ps
    ```

7.  **Access Local Postgres Database (Airflow Metastore):**
    The Airflow metadata database is accessible at `localhost:5432/postgres` (Username: `postgres`, Password: `postgres`).

## Understanding the DAG(s)

*   **`eltweather.py`** 
    *   **Purpose:** Orchestrates the end-to-end process of fetching, transforming, and loading current weather data for a predefined list of cities (London, Paris, New York). The pipeline creates a separate CSV file for each city's weather data per run.
    *   **Schedule:** This pipeline is scheduled to run daily. Airflow's `@daily` preset typically means it runs once per day at 00:00 UTC at the beginning of the scheduled interval.
    *   **Tasks:**
        *   `is_weather_api_ready`: Checks if the OpenWeatherMap API endpoint is accessible before starting the data processing for any city.
        *   `extract_weather_data_[city_name]`: (Repeated for each city) Calls the OpenWeatherMap API to fetch current weather data for the specific city. The raw JSON response is pushed to Airflow XComs.
        *   `transform_weather_data_[city_name]`: (Repeated for each city) Retrieves raw JSON data from XComs, extracts key fields, converts temperatures from Kelvin to Fahrenheit, and formats timestamps. The structured data is pushed back to XComs.
        *   `load_weather_data_[city_name]`: (Repeated for each city) Pulls transformed data from XComs, converts it to a Pandas DataFrame, and saves it as a city-specific CSV file (e.g., `London_weather_data_YYYYMMDDHHMMSS.csv`).


## Author

*   **Adarsh Pentapati**
*   https://www.linkedin.com/in/adarsh-pentapati/
*   https://github.com/Adarsh121005/

---
