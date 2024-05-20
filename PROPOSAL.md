# Overview
For my capstone project, I'd like to fetch and store information IOT devices for debugging that would be valuable to both our clients and our internal support teams.  There are approximately 10,000 devices that I'd like to support with the number of devices increasing regularly.

# Data Sources
## Signal Strength Data
We are able to get readings of the signal strength of the connected IOT devices.  I'd like to fetch that information every 5-60 minutes depending on the resolution available to the device.  These devices use very little power and only wake up from time to time, so I need to track down the appropriate resolution to use for the data.

## Device Activity Logs
We also have access to an API of actions that we have attempted to take on the device.  These actions are things like connecting, updating configuration, timeouts reaching the device, etc.  Not sure how frequently we'd fetch this information yet, sometime between hourly and daily.

# Use Cases
1. Analytics on signal strength - data warehouse
 - I'd like to be able to produce reports of changing signal strength over time because the devices move around regularly
 - I'd like to be able to store a years worth of this data to help debug issues clients have with signal strength over time
 - I'd like to be able to roll up the data in intervals that meet the business needs.  Really accurate data for the last few days, summary data for the last month, and long term high level summary data for a year
 2. Debugging information in a web app - Relational database
 - I'd like to be able to save versions of this data (targetted at debugging recent issues) in a relational database, so that is easy and fast to query / present to customers alongside other inforamtion coming from that relational database 

 # Tech Stack
 ## Ingestion - Python Microservice / Kafka
 - The ingestion of this data would take place in a python micro service that would be responsible for polling for the data at interval
 - Python Microservice because we already have API utility in python that would make it easier to access this data
 - Put the data onto kafka to be picked up by Airflow for further processing
 ## Storage Processing - S3 / Airflow / Redshift
 - Airflow would be responsible for picking up the data from Kafka and storing the raw version of the data in our data lake
 - Aiflow would also manage inserting the data into appropriate tables in our existing data warehouse using redshift
 ## Long Term Storage - Airflow / Redshift
 - Airflow would be responsible for formatting older data into summary tables using SCDs at appropriate intervals depending on the nature of the data
 - These tables would be stored in our existing Redshift data warehouse
 ## Web App Analytics - Airflow / Django
 - Airflow would also be responsible for generating a version of the data that is appropriate to send to an existing Django webapp for more immediate use
