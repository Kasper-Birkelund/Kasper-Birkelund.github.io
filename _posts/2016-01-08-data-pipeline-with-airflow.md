---
layout: post
title: Price difference project (Airflow data pipeline)
---

I prepared this project to show a data pipeline built with Airflow. It is a simple example of fetching some Uniswap swap data (WETH/USDC) of a single user from Dune using their API and comparing the prices with Crypto Compare's API.

## Project Components

- **Github repo**: See the full repo here: [Price difference project](https://github.com/Kasper-Birkelund/price_difference_project)
- **Airflow**: Orchestrates the pipeline's tasks on a adaily basis.
- **AWS PostgreSQL Database**: Stores all data.
- **Dune Analytics**: Data on swaps are fetched from a Dune query, allowing parameterized fetching to retrieve only the most recent data. [View the query here](https://dune.com/queries/4002884).
- **Crypto Compare API**: Provides near-real-time market prices to compare against trade execution prices.


## Repository Structure

In this repository please explore the following key directories and files:

- __dags/__: Contains Airflow DAGs defining the workflow and Python scripts for fetching data from Dune and Crypto Compare APIs and calculating price difference.
- __db_management/__: Includes scripts for database operations like creating views.
- __Dockerfile, docker-compose.yml, .env and requirements.txt__: These files are used to build the docker container.
- __notebooks__: If you want to try to interact with the code with a jupyter notebook. If so you need to active the .conda virtual environment or build your own using the requirements_local_conda.txt file.

## Running the Project

1. **Environment Setup**: Ensure Docker and VS Code are installed.
2. **Start the Application**:
   - Open the project folder in VS Code.
   - Run below docker command and wait for the services to initialize:
   ```bash
   docker compose up
   ```
3. **Access Airflow UI**:
   - Navigate to [http://localhost:8080/home](http://localhost:8080/home) in your web browser.
   - Log in with username __airflow__ and password __airflow__.
4. **Execute and Monitor Pipeline**:
   - Locate the __price_difference_dag__ and manually trigger the DAG by clicking the "play button".
   - Monitor task progress and also check out the dependencies in the DAG's Graph View by clicking the name of the DAG and then "Graph".
5. **View Results**:
   - Access results from the DAG's execution logs under __logs/dag_id=price_difference_dag/run_id=manual__[timestamp]/task_id=calculate_price_difference__. Look for the __attemp=1.log__ file in the folder with the latest timestamp. Inside the file please search for 3 hashtags "###" and finde a line like this:
   __[2024-07-29T13:04:29.342+0000] {calculate_price_difference.py:24} INFO - ### The average price difference for all the swaps was -0.175 percent ###__

## Stopping the Project and clean up

To halt the project and clean up Docker images, use:
```bash
docker compose down --rmi all
```

## Considerations and improvements for production readiness

Unfortunately it Crypto Compare only has the last 7 days of prices on the minute granularity. So I opted for Cryptoo Compare daily closing prices:

[Crypto Compare daily](https://min-api.cryptocompare.com/documentation?key=Historical&cat=dataHistoday)

These are obvioulsly not very acurate, but the idea of the project was to show a working pipeline not so much interesting analytical findings.

In transitioning to a production environment, I would consider the following:

- **Security**: Implement managed secret handling services like AWS Secrets Manager for sensitive data.
- **Scalability**: Utilize Celery with Airflow for distributed task execution.
- **Reliability**: Enhance error handling with alerts. Add comprehensive logging and monitoring.
- **Data Integrity**: Implement data quality checks and validations throughout the data pipeline.
- **Compliance and Documentation**: Maintain thorough documentation and ensure compliance with data protection regulations.
