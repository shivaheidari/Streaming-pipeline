# AWS Tweet Stream Pipeline

A real-time data pipeline designed to ingest, stream, process, and store Twitter (X) data using AWS cloud services.

## Architecture Overview

The pipeline operates in the following sequential stages:

1. **Data Ingestion (Twitter Client)**
   - Uses `tweepy` to fetch real-time tweets based on specific queries/hashtags.
   - Data can be streamed directly or saved to local JSON files for batch processing.

2. **Streaming Layer (AWS Kinesis Data Streams)**
   - Acts as the central nervous system of the pipeline.
   - Receives data from the Twitter client either as a direct live stream or uploaded from historical JSON files in batches.

3. **Processing Layer (AWS Lambda & Dockerized Flask API)**
   - **Lambda Event Trigger:** An AWS Lambda function is configured to trigger upon receiving new records in the Kinesis stream.
   - **Flask API:** Lambda extracts the tweet payload and sends an HTTP request to an external, Dockerized Flask API for specialized processing (e.g., sentiment analysis, classification, or filtering).

4. **Storage Layer (AWS S3)**
   - Once the Flask API responds with the processed data, the Lambda function writes the final enriched payload into an Amazon S3 bucket for long-term storage and downstream analytics.

## Repository Structure

- **`twitter_client.py`**: Contains the `TwitterClient` class for authenticating with the Twitter v2 API and saving recent tweets to local JSON files.
- **`kinesis_data_stream.py`**: Contains the `KinesisDataStream` class using `boto3`. Responsible for creating streams, putting single/batch records into Kinesis, and reading from shards.
- **`0_get_tweets_to_Kinesis_DS.py`**: An integration script that combines the Twitter client and Kinesis to search for recent tweets and immediately stream them to AWS.
- **`*_tweets.json`**: Sample datasets containing fetched tweets (e.g., Samsung, Trump, deep seek) used for testing the batch file upload functionality to Kinesis.

## Prerequisites

- **Python 3.8+**
- **AWS Credentials:** Configured with permissions for Kinesis, Lambda, and S3 (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`).
- **Twitter Developer Account:** A valid Bearer Token for API v2 access.
- **Docker:** For running the local Flask API processor.

## Setup & Usage

1. **Install Dependencies:**
   ```bash
   pip install boto3 tweepy
   ```
2. **Set up AWS Configuration:**
   Ensure your AWS credentials and region are correctly injected when initializing the `KinesisDataStream` class.
3. **Run the Ingestion Script:**
   Execute `0_get_tweets_to_Kinesis_DS.py` with your Twitter Bearer token and AWS keys to start fetching and streaming tweets.