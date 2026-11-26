# Serverless Data Lake on AWS (Medallion Architecture)

An automated, fully serverless data pipeline on AWS. It collects **Hacker News** and Twitter data, processes it through a **bronze → silver → gold** medallion data lake, and serves analytics dashboards in **Apache Superset**. All infrastructure is defined as code with **Terraform**.

## Architecture

```
EventBridge (daily) ─► Step Functions pipeline
                          │
   Bronze  ─ Lambda: parallel, paginated API ingestion ─► S3 raw JSON (partitioned by date)
   Silver  ─ Lambdas: normalization and cleaning ─► S3 Parquet (year/month/day)
   Gold    ─ Lambda: aggregations (e.g. top and least active users) ─► S3
   Sync    ─ Lambda ─► PostgreSQL ─► Superset dashboards (EC2)

Pipeline failures ─► EventBridge ─► Discord notifier Lambda
```

## Highlights

- **Ingestion:** fetches the previous day's data in time chunks, with parallel requests and pagination
- **Storage:** date-partitioned S3 layout, Parquet in the silver and gold layers
- **Orchestration:** an AWS Step Functions state machine, triggered on a daily schedule
- **Networking:** VPC, security groups and an S3 VPC endpoint
- **Monitoring:** CloudWatch logs and Discord alerts on job failures
- **Infrastructure as code:** modular Terraform (Lambda, IAM, EventBridge, Step Functions, EC2, VPC)

## Tech stack

Python · AWS Lambda · S3 · Step Functions · EventBridge · CloudWatch · EC2 · VPC · Terraform · pandas / AWS Wrangler · Parquet · PostgreSQL · Apache Superset

## Deploying

```bash
cd infrastructure
terraform init
terraform apply
```

## Team

- Petar Popović
- Luka Prlinčević
- Aleksa Dejanović
