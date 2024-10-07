# AWS Data Engineering ETL Project (S3, Glue, RedShift, Athena, Lambda, Workflow)

## Overview
This project demonstrates a complete ETL process using AWS Glue to load product data from AWS S3 into Amazon Redshift. The project involves creating AWS Glue crawlers, ETL jobs, logging to AWS CloudWatch, and managing data transformations.

## Technologies Used
- **AWS Glue**: ETL service for extracting, transforming, and loading data.
- **AWS S3**: Cloud storage service used as the data source and target.
- **Amazon Redshift**: Data warehousing service where the transformed data is loaded.
- **Python**: Script for AWS Glue jobs and data transformation.
- **Spark**: Utilized in the Glue job for processing large data sets.

## Project Structure
- **scripts/**: Contains the Python script used for the Glue job (`MyGlueReadFromS3_job.py`).
- **logs/**: Directory where example logs from AWS CloudWatch are stored.
- **requirements.txt**: Python dependencies.

## Prerequisites
- AWS Account with the necessary IAM roles.
- AWS Glue, S3, and Redshift services set up.

## Steps to Run the Project

### 1. AWS Setup
1. **Create a Crawler**: 
   - Set up an AWS Glue crawler (e.g., `MyCrawlerFetchFromS3`) to fetch product data from S3.
   - Define your data source in an S3 bucket.

2. **Create an AWS Glue Job**: 
   - Set up the job (e.g., `MyGlueJobReadFromS3`) that reads data from S3, processes it, and writes the results to a specified S3 bucket.

### 2. Running the Glue Job
1. Upload the Python script (`MyGlueReadFromS3_job.py`) in the AWS Glue console under the script editor section.
2. Configure the input and output S3 buckets within the Glue script.

### 3. Scheduling and Monitoring
- **VPC Setup**: Ensure you have the necessary VPC configuration to resolve any Redshift connection issues.
- **Job Scheduling**: Use AWS Lambda to schedule jobs or AWS Glue’s built-in triggers.
- **Logging**: Logs can be monitored in AWS CloudWatch. Example logs are provided in the `logs` directory.

### 4. Sample Glue Job Script
A sample script (`MyGlueReadFromS3_job.py`) can be found in the `scripts/` folder, showing how to map data, transform it, and upload it to S3.

### Error Handling
If you encounter connection issues with Amazon Redshift, ensure your VPC and security groups are correctly configured.

## Project Script
The Python script used for logging and data transformations is in the `scripts/` folder. Here's a sample snippet:

```python
import logging
logger = logging.getLogger('my_logger')
logger.setLevel(logging.INFO)

handler = logging.StreamHandler()
handler.setLevel(logging.INFO)
logger.addHandler(handler)

logger.info('ETL job started')

# Apply mapping and transformations
ApplyMapping_node2 = ApplyMapping.apply(frame=S3bucket_node1, mappings=[
    ("marketplace", "string", "new_marketplace", "string"),
    ("customer_id", "long", "new_customer_id", "long"),
    # Additional mappings...
])

logger.info('Data mapped successfully')
