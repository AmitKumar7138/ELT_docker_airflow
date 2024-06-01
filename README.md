# Docker Compose ELT Project with Airflow

This project uses Docker Compose to orchestrate multiple Docker containers, facilitating an ELT (Extract, Load, Transform) process between two PostgreSQL databases. Airflow is employed to schedule and manage the ELT tasks.

## Services

### source_postgres
The source PostgreSQL database initialized with sample data.

### destination_postgres
The destination PostgreSQL database to which data will be loaded.

### elt_script
The service that runs the ELT script.

## File Structure

### docker-compose.yaml
This file contains the configuration for Docker Compose, defining three services: `source_postgres`, `destination_postgres`, and `elt_script`.

### elt_script/Dockerfile
This Dockerfile sets up a Python environment, installs the PostgreSQL client, copies the ELT script into the container, and sets it as the default command.

### elt_script/elt_script.py
This Python script performs the ELT process. It waits for the source PostgreSQL database to become available, then dumps its data to a SQL file and loads this data into the destination PostgreSQL database.

### source_db_init/init.sql
This SQL script initializes the source database with sample data. It creates tables for users, films, film categories, actors, and film actors, and inserts sample data into these tables.

## How It Works

### Docker Compose
Using the `docker-compose.yaml` file, three Docker containers are spun up:
- A source PostgreSQL database with sample data.
- A destination PostgreSQL database.
- A Python environment that runs the ELT script.

### ELT Process
The `elt_script.py` waits for the source PostgreSQL database to become available. Once it's available, the script uses `pg_dump` to dump the source database to a SQL file. Then, it uses `psql` to load this SQL file into the destination PostgreSQL database.

### Database Initialization
The `init.sql` script initializes the source database with sample data. It creates several tables and populates them with sample data.

## Airflow Implementation
In this project, Airflow is used to automate and manage the ELT process. The Airflow scheduler runs the ELT script at specified intervals, ensuring that the data in the destination PostgreSQL database is regularly updated with the latest data from the source database.

To configure Airflow:
- Ensure Airflow is set up and configured within the Docker environment.
- Define a DAG (Directed Acyclic Graph) in Airflow to schedule the ELT script.

### Getting Started

1. Ensure you have Docker and Docker Compose installed on your machine.
2. Clone this repository.
3. Navigate to the repository directory and run `docker-compose up`.
4. Access the Airflow web interface to monitor and manage the ELT process.

Once all containers are up and running, the ELT process will start automatically as scheduled by Airflow. After the ELT process completes, you can access the source and destination PostgreSQL databases on ports 5433 and 5434, respectively.

## Example Files

### docker-compose.yaml

```yaml
version: '3.8'

services:
  source_postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: source_user
      POSTGRES_PASSWORD: source_password
      POSTGRES_DB: source_db
    ports:
      - "5433:5432"
    volumes:
      - source_db_data:/var/lib/postgresql/data
      - ./source_db_init:/docker-entrypoint-initdb.d

  destination_postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: dest_user
      POSTGRES_PASSWORD: dest_password
      POSTGRES_DB: dest_db
    ports:
      - "5434:5432"
    volumes:
      - dest_db_data:/var/lib/postgresql/data

  elt_script:
    build: ./elt_script
    environment:
      SOURCE_DB_URL: postgres://source_user:source_password@source_postgres:5432/source_db
      DEST_DB_URL: postgres://dest_user:dest_password@destination_postgres:5432/dest_db

volumes:
  source_db_data:
  dest_db_data:
