![Ekran görüntüsü 2024-01-22 213039](https://github.com/emirakyer/Data-Analysis-and-Data-Transfer-Terraform-Docker-PostgreSQL-pgAdmin-/assets/124304427/6df18b85-562f-43a6-9545-cabb231f00ee)﻿


Docker + Postgres

Docker and Postgres are important technologies for data engineers.
Docker increases portability and scalability by packaging data and applications into containers.
Postgres is a powerful, performant, flexible, and secure RDBMS.
By using these two technologies together, you can manage and analyze your data more efficiently and effectively.
Docker and Postgres are essential technologies for data engineers to manage and analyze data.



![Ekran görüntüsü 2024-01-22 220752](https://github.com/emirakyer/dbt-new/assets/124304427/28b23bca-a43e-4cf1-8c17-d5f81c994556)


GCP + Terraform

Prerequisites
Install gcloud CLI, https://cloud.google.com/sdk/docs/install
Install Terraform, https://developer.hashicorp.com/terraform/tutorials/gcp-get-started/install-cli

You can Refresh service-account's auth-token for this session
gcloud auth application-default login

1. Initialization:
First, run terraform init within your Terraform configuration directory. This initializes the working directory, downloads any necessary modules, and verifies the configuration syntax.

2. Planning:
Once initialized, use terraform plan to see the changes Terraform will make to your infrastructure based on your configuration. This allows you to review the planned actions and confirm everything is as expected before actually applying them.

3. Deployment:
When you're confident with the planned changes, execute terraform apply to enact them. This will create or update your infrastructure resources in the target cloud environment according to your Terraform configuration.

4. Cleanup (Optional):
After completing your cloud storage tasks, if you need to tear down the deployed infrastructure, you can use terraform destroy to safely and efficiently remove the resources.


Data Engineering with GCP and Terraform

Google Cloud Platform (GCP) is a cloud-based platform that offers a wide range of tools and services for data engineers. Terraform is a tool that is used to automate cloud infrastructure.

By using these two technologies together, data engineers can manage and analyze their data more efficiently and effectively.

Advantages of GCP:

Wide range of tools and services
High performance and scalability
Security and compliance
Advantages of Terraform:

Automation
Flexibility
Security
Data engineers can use these technologies for the following data engineering tasks:

Data storage
Data processing
Data analysis
In conclusion, GCP and Terraform are important technologies for data engineers. Learning these technologies can help you develop your data engineering skills and increase your chances of finding a job.



![Ekran görüntüsü 2024-01-22 224354](https://github.com/emirakyer/dbt-new/assets/124304427/69b1a95d-161a-4426-b454-94e31ebe0e20)


For the project you'll need:

* Python 3 (e.g. installed with Anaconda)
* Google Cloud SDK
* Docker with docker-compose
* Terraform


Fundamental Technologies for Data Engineering

Python: A programming language used for data collection, cleaning, processing, and analysis.
Google Cloud SDK: A tool used to access and use Google Cloud Platform.
Docker: A technology that packages applications and services into containers.
Terraform: A tool used to automate cloud infrastructure.

How can you use these technologies?
Python: You can use Python for data collection, cleaning, processing, and analysis.
Google Cloud SDK: You can use the Google Cloud SDK to create, manage, and deploy resources on GCP.
Docker: You can use Docker to package and deploy applications and services into containers.
Terraform: You can use Terraform to automate cloud infrastructure.
For example:

You can use Python to extract, clean, and analyze data from a database.
You can use the Google Cloud SDK to create a data set, train a machine learning model, and deploy a web application.
You can use Docker to package and deploy a data processing application into containers.
You can use Terraform to deploy, update, and scale a cloud infrastructure.
Learning these technologies can help you develop your data engineering skills and increase your chances of finding a job.


Project Setup and Data Ingestion
First, let's build a Docker Compose file to configure our environment. Here's the provided configuration:

YAML
version: '3.8'

services:
  pg-database:
    image: postgres:13
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      POSTGRES_DB: ny_taxi
    volumes:
      - /your/path/to/data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql/ny-taxi-volume:/var/lib/postgresql/data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - pg-network
    hostname: pg-database

  pgadmin:
    image: dpage/pgadmin4
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: root
    volumes:
      - /your/path/to/data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql/ny-taxi-volume:/var/lib/postgresql/data:/var/lib/postgresql/data
    ports:
      - "8080:80"
    networks:
      - pg-network

networks:
  pg-network:
    driver: bridge

Once the Docker containers are running, you can ingest the NYC taxi data using the provided Python script with the URL and database parameters:



![Ekran görüntüsü 2024-01-22 220752](https://github.com/emirakyer/Data-Analysis-and-Data-Transfer-Terraform-Docker-PostgreSQL-pgAdmin-/assets/124304427/dd0bbc29-5840-4641-9ad5-9d303d40903c)


Bash

$URL = "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-09.csv.gz"
python ingest_data.py --user root --password root --host localhost --port 5432 --db ny_taxi --table_name green_taxi_trips --url $URL



Let's do some analysis!


1. Checking Data Sample:

SQL
SELECT * FROM greentripdata201909 LIMIT 10;

This query simply retrieves the first 10 rows from the greentripdata201909 table. This lets you get a glimpse of the data structure, including column names and sample values.



2. Finding Date Range:

SQL
select min("lpep_pickup_datetime"),max("lpep_pickup_datetime")
from public.greentripdata201909

This query finds the minimum and maximum values of the lpep_pickup_datetime column. This tells you the earliest and latest timestamps present for taxi pickups in the dataset.



3. Analyzing Tips by Zone and Distance:

SQL
SELECT
  zones."Zone",
  trip_distance,
  SUM(tip_amount) AS total_tip_amount
FROM
  greentripdata201909
INNER JOIN
  zones ON greentripdata201909."PULocationID" = zones."LocationID"
WHERE
  lpep_dropoff_datetime::DATE BETWEEN '2019-09-01' AND '2019-09-30'
  AND zones."Zone" != 'Unknown'
GROUP BY
  zones."Zone", trip_distance
ORDER BY
  trip_distance DESC, total_tip_amount DESC;

This query performs a more complex analysis. It:
Joins the greentripdata201909 table with the zones table based on the PULocationID field.
Filters the data for September 2019 and excludes pick-up areas labelled "Unknown".
Groups the data by zone and trip distance.
Calculates the total tip amount for each zone and distance combination.
Orders the results in descending order of both trip distance (longest first) and total tip amount (highest first).
This analysis can reveal how much passengers tend to tip based on the pick-up zone and distance they travel.



4. Finding Highest Tip from Astoria:
   
SQL
SELECT
  z2."Zone",
  MAX(tip_amount) AS Max_tip
FROM
  greentripdata201909 
INNER JOIN
  zones AS z ON greentripdata201909."PULocationID" = z."LocationID"
INNER JOIN
  zones AS z2 ON greentripdata201909."DOLocationID" = z2."LocationID"
WHERE
  z."Zone" = 'Astoria'
GROUP BY
	z2."Zone"
ORDER BY
  Max_tip DESC
LIMIT 1;

This query focuses on pick-up zones within the "Astoria" area. It:

Uses a double join similar to the previous query but based on both pick-up and drop-off zones.
Filters the data to only include pick-ups from Astoria.
Groups the data by drop-off zone.
Calculates the maximum tip amount for each drop-off zone.
Orders the results in descending order of maximum tip amount.
Uses LIMIT 1 to show only the zone with the highest overall tip.
This analysis identifies the drop-off area receiving the highest tip from passengers originating in Astoria.



5. Top Tipped Zone for September:

SQL
WITH ranked_zones AS (
 SELECT
  zones."Borough",
  zones."Zone" AS dropoff_zone,
  SUM(tip_amount) AS total_tip_amount,
  SUM(trip_distance) AS total_trip_distance,
  RANK() OVER (ORDER BY SUM(tip_amount) DESC) AS tip_rank
 FROM greentripdata201909
 INNER JOIN zones ON greentripdata201909."DOLocationID" = zones."LocationID"
 WHERE
  greentripdata201909.lpep_dropoff_datetime::DATE BETWEEN '2019-09-01' AND '2019-09-30'
  AND zones."Zone" != 'Unknown'
 GROUP BY zones."Borough", zones."Zone"
)
SELECT * FROM ranked_zones WHERE tip_rank = 1;


