# AWS S3 Event-Driven Automation Using Lambda

## Project Overview

This project demonstrates an event-driven automation workflow using **Amazon S3 and AWS Lambda**. When an employee CSV file named `employees.csv` is uploaded to an S3 bucket, an S3 event notification automatically triggers a Lambda function.

The Lambda function reads the employee data, categorizes employees based on their salary, and saves the processed CSV file back to the same S3 bucket.

This project demonstrates serverless computing, event-driven architecture, automated data processing, and AWS permissions management.

## Project Objectives

- Configure an Amazon S3 bucket to store employee data.
- Automatically trigger an AWS Lambda function when a CSV file is uploaded.
- Read and process employee data using Python.
- Categorize employees based on their salary.
- Save the processed CSV output in the same S3 bucket.
- Configure IAM permissions for secure access to S3 objects.

## Technologies Used

- **Amazon S3** – Stores input and processed CSV files.
- **AWS Lambda** – Executes the Python processing function.
- **Python** – Reads, validates, and transforms employee data.
- **AWS IAM** – Manages permissions for Lambda execution.
- **Amazon CloudWatch** – Provides Lambda execution logs and troubleshooting information.
- **CSV** – Input and output data format.
- **GitHub** – Source code management and project documentation.

## Architecture

```text
             Employee CSV Upload
                     |
                     v
              Amazon S3 Bucket
               employees.csv
                     |
                     v
             S3 Object-Created Event
                     |
                     v
              AWS Lambda Function
            EmployeeCSVProcessor
                     |
                     v
          Read and Process Employee Data
                     |
                     v
           Categorize Employee Salaries
                     |
                     v
              Amazon S3 Bucket
                  processed/
                     |
                     v
          employees_processed_<timestamp>.csv
```

**Note:** This project uses a single S3 bucket for both input and output files.

## Project Workflow

### 1. Create an S3 Bucket

Create a general-purpose S3 bucket in the AWS Management Console.

Keep Block Public Access enabled because this project does not require public website access.

### 2. Upload the Employee CSV File

Upload a file named `employees.csv` to the root of the S3 bucket.

Example input data:

```csv
employee_id,employee_name,salary,city
101,Rahul,45000,Bengaluru
102,Priya,60000,Hyderabad
103,Arjun,35000,Chennai
104,Ananya,75000,Pune
105,Kiran,50000,Bengaluru
```

### 3. Create the Lambda Function

Create an AWS Lambda function named `EmployeeCSVProcessor` using a supported Python runtime.

The function uses the AWS SDK for Python, Boto3, to interact with Amazon S3.

### 4. Configure IAM Permissions

Attach an IAM permissions policy to the Lambda execution role.

The role requires:
- `s3:GetObject` permission to read `employees.csv`.
- `s3:PutObject` permission to write processed files under the `processed/` prefix.
- CloudWatch Logs permissions to record execution details.

Follow the principle of least privilege by granting only the access required by the function.

### 5. Configure the S3 Event Trigger

Configure an S3 trigger on the Lambda function.

- **Event type:** All object create events.
- **Bucket:** The project S3 bucket.
- **Suffix filter:** `employees.csv`.

The suffix filter matches the input filename while avoiding triggering the function for its timestamped output files.

### 6. Process Employee Data

When the input CSV is uploaded, Lambda:

1. Receives the S3 event notification.
2. Reads the uploaded employee CSV file.
3. Validates the required column names.
4. Calculates a salary category for each employee.
5. Generates a new CSV file containing the original columns and the additional `salary_category` column.
6. Saves the output under the `processed/` prefix in the same bucket.

### 7. Salary Categorization Logic

The project uses the following rules:

| Salary | Category |
|---|---|
| 60,000 or above | High |
| 40,000 to 59,999.99 | Medium |
| Below 40,000 | Low |

These thresholds are configurable in the Lambda function.

## Example Output

The processed CSV includes an additional column named `salary_category`.

```csv
employee_id,employee_name,salary,city,salary_category
101,Rahul,45000,Bengaluru,Medium
102,Priya,60000,Hyderabad,High
103,Arjun,35000,Chennai,Low
104,Ananya,75000,Pune,High
105,Kiran,50000,Bengaluru,Medium
```

The values above illustrate the expected result for the sample input data.

## S3 Bucket Structure

```text
your-s3-bucket/
├── employees.csv
└── processed/
    └── employees_processed_<timestamp>.csv
```

The `processed/` prefix is created automatically when Lambda writes the first output file.

## Testing and Validation

To test the workflow:

1. Upload `employees.csv` to the S3 bucket.
2. Verify that the Lambda function is invoked.
3. Check the Lambda execution logs in Amazon CloudWatch.
4. Open the `processed/` prefix in S3.
5. Download the processed CSV.
6. Verify the salary categories and output columns.

## Error Handling and Troubleshooting

**AccessDenied error**
- Check the Lambda execution role's S3 permissions.
- Verify the bucket name and object paths in the IAM policy.

**No output file generated**
- Review the Lambda execution logs in CloudWatch.
- Verify that the input file is named exactly `employees.csv`.
- Confirm that the CSV contains the required columns.

**Lambda is not triggered**
- Check the S3 event notification configuration.
- Verify the selected bucket and object-created event type.
- Confirm that the suffix filter matches `employees.csv`.

**Repeated Lambda invocations**
- Ensure that the output files use a different prefix, such as `processed/`.
- Keep the trigger suffix set to `employees.csv` and upload the input file at the bucket root.

## Security Best Practices

- Keep S3 Block Public Access enabled.
- Never store AWS access keys, passwords, or confidential employee information in the GitHub repository.
- Use IAM roles rather than hardcoded credentials.
- Restrict S3 permissions to the required input object and output prefix.
- Avoid including real employee personal data in public demonstrations.

## Key Learnings

- Understanding event-driven architecture in AWS.
- Configuring S3 event notifications.
- Building serverless applications with AWS Lambda.
- Processing CSV data using Python and Boto3.
- Managing AWS resources through IAM policies.
- Monitoring Lambda execution using CloudWatch.
- Automating data transformation without maintaining a server.

## Future Enhancements

- Send email notifications through Amazon SNS after successful processing.
- Validate employee records and handle malformed CSV rows.
- Add automated tests for the Python processing function.
- Build a reporting pipeline using Amazon Athena.
- Automate deployment using AWS SAM or CI/CD workflows.

## Author

**CVV ANAND**

B.Tech Student – Artificial Intelligence and Machine Learning

GitHub: [Anandcvv05](https://github.com/Anandcvv05)

---

*This project was developed for educational purposes to demonstrate AWS serverless automation and event-driven data processing.*
