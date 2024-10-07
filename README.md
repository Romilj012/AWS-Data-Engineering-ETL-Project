# AWS Data Engineering ETL Project (S3, Glue, RedShift, Athena, Lambda, Workflow)
# AWS Data Engineering Project

This project demonstrates a complete ETL (Extract, Transform, Load) process using AWS Glue to load data from AWS S3 to Amazon Redshift.

![AWS ETL Architecture](AWS%20ETL%20Architecture.png)

## AWS Glue

AWS Glue provides a console and API operations to set up and manage your extract, transform, and load (ETL) workload.

### Key Features

- **Define AWS Glue Objects**: Create jobs, tables, crawlers, and connections.
- **Schedule Crawlers**: Control when crawlers run.
- **Job Triggers**: Define events or schedules for job execution.

## AWS Crawler

AWS Glue crawlers connect to your data store, determine the schema, and create metadata in your AWS Glue Data Catalog.

- **Metadata Creation**: Allows services like Athena to view S3 data as a database with tables.
- **Incremental Updates**: Only appends new or modified files to existing catalog tables.

## AWS Glue Job (ETL)

AWS Glue offers two ETL options for processing data:

1. **Python Shell**: Best for smaller datasets that don't require distributed processing.
2. **Apache Spark**: Supports distributed data processing for high performance and scalability.

## Project Structure

### Crawlers

1. MyCrawlerFetchFromS3
2. MyCrawlerFetchFromRedshift

### ETL Jobs

1. MyGlueJobReadFromS3
2. MyGlueInsertRedshift

## Product Data

| Marketplace | Customer ID | Product ID   | Seller ID | Sell Date  | Quantity |
|-------------|-------------|--------------|-----------|------------|----------|
| US          | 49033728    | A6302503213  | 1111      | 31-08-2021 | 10       |
| US          | 17857748    | B000059PET1  | 2222      | 20-09-2021 | 20       |
| US          | 25551507    | S7888128071  | 3333      | 31-08-2021 | 10       |
| US          | 21025041    | W630250993   | 4444      | 20-09-2021 | 20       |
| US          | 40943563    | B00JENS2BI   | 5555      | 31-08-2021 | 10       |
| US          | 17013969    | J6305761302  | 6666      | 05-09-2021 | 30       |
| US          | 47611685    | K6300157555  | 7777      | 06-09-2021 | 30       |
| US          | 35680737    | H6300189570  | xxxx      | 07-09-2021 | 40       |
| US          | 10747909    | B000SXQ5US   | yyyy      | 08-09-2021 | 20       |

Additional data for 2022:

| Marketplace | Customer ID | Product ID   | Seller ID | Sell Date  | Quantity |
|-------------|-------------|--------------|-----------|------------|----------|
| US          | 7777728     | F6A02503213  | 8888      | 31-08-2022 | 10       |
| US          | 8888848     | HK90059PET1  | 9999      | 20-09-2022 | 20       |

## Steps for Crawler MyCrawlerFetchFromS3

1. Create partitions in S3 and upload the files:
   - `s3://myglue-etl-project/input/product/year=2021/product_data.csv`
   - `s3://myglue-etl-project/input/product/year=2022/product_data_2.csv`
2. Create a crawler referencing the path:
   - `s3://myglue-etl-project/input/product/`
   - Ensure IAM role has permissions for S3 and CloudWatch.
3. The crawler will automatically create a year column as a partition column.
4. Run the crawler to catalog both 2021 and 2022 data.

## Create Glue ETL MyGlueJobReadFromS3 Job

1. Create a Glue ETL job using PySpark.
2. Read data from the product table created by MyCrawlerFetchFromS3.
3. Convert the data into Parquet format.
4. Create output file in `s3://myglue-etl-project/output/`.

### ETL Job Operations

- **apply_mapping**: Used to rename, cast, or add columns.
- **ResolveChoice**: Handles column type conflicts when combining data from different sources.

## Example Code Snippet for Logging

```python
import logging
logger = logging.getLogger('my_logger')
logger.setLevel(logging.INFO)
handler = logging.StreamHandler()
handler.setLevel(logging.INFO)
logger.addHandler(handler)
logger.info('My log message')
```

## Create Crawler MyCrawlerFetchFromRedshift

1. Create a table in Redshift:
   ```sql
   CREATE TABLE public.product_tab_def (
       year varchar(100),
       noofcustomer BIGINT,
       quantity BIGINT
   );
   ```
2. Create a crawler referencing the Redshift connection and table definition.

## Create MyGlueInsertRedshift Job

This job reads Parquet files from `s3://myglue-etl-project/output/` and inserts data into Redshift.

## Job Scheduling using Lambda

```python
import json
import os
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)
client = boto3.client('glue')
glueJobName = "MyTestJob"

def lambda_handler(event, context):
    logger.info('## INITIATED BY EVENT: ')
    logger.info(event['detail'])
    response = client.start_job_run(JobName=glueJobName)
    logger.info('## STARTED GLUE JOB: ' + glueJobName)
    logger.info('## GLUE JOB RUN ID: ' + response['JobRunId'])
    return response
```

## Advanced Properties and Triggers

### Workflow Overview

![Screenshot 2024-10-06 at 10.46.37 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/400e6f1e-eadb-4279-8837-8d11de40efd1/7a3542da-b839-4a58-86bf-195f976e3776/Screenshot_2024-10-06_at_10.46.37_PM.png)

### Triggers Overview

A trigger starts a job when it fires.

This README provides an overview of the AWS Data Engineering project, including the setup of AWS Glue, crawlers, ETL jobs, and job scheduling. For detailed implementation, refer to the individual script files in this repository.
