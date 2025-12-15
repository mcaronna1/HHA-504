
# Architecture & Implementation Plan 

## High-Level Architecture Diagram

[Architecture Diagram](images/architecture_diagram.png)



## Overview

To begin, each cloud service in this system will be described in terms of its specific role, highlighting how it relates to concepts covered in prior coursework. The accompanying visual diagram provides a clear mapping of these services and their interactions. Below is a service mapping of the plan, which gives a clear visualization of the layer, which service cloud could be used, the solution, and which module we learned in class.

## 1. Service Mapping: 


| Layer            | Service (Cloud)              | Role in Solution                                                                 | Related Assignment / Module |
|------------------|------------------------------|----------------------------------------------------------------------------------|-----------------------------|
| Storage          | Google Cloud Storage         | Stores raw JSON/CSV check-in and patient status data securely uploaded from the EHR system prior to processing. | Module 6 |
| Compute          | Cloud Functions              | Triggered by new files in Cloud Storage to perform ETL tasks, including data cleaning, transformation, de-identification, and time interval calculations. | Module 5 |
| Compute          | Cloud Run                    | Runs the containerized prediction service that calculates real-time patient wait times and exposes endpoints that let other applications retrieve the information. | Module 3 |
| Database / SQL   | Cloud SQL (PostgreSQL)       | Stores cleaned patient flow data, historical records, and predicted wait times for operational queries. | Module 8 |
| Analytics / AI   | BigQuery                     | Performs aggregated analytics and reporting on historical wait time and clinic flow data. | Module 9 |




## 2. Data Flow Narrative

**Step 1** 
      Patient Check-in (Data Ingestion): The EHR System sends a patient status update ("Checked In") to Cloud Storage.
          
**Step 2**     
      Cleaning and Securing the Data (ETL): A Cloud Function is instantly triggered by the new file. It cleans the data and removes all identifying information (PHI), replacing it with a secure, non-reversible ID.

**Step 3**   
      Predicting the Wait Time (AI Calculation):
        
o	The Cloud Run service (which holds the ML model) runs the prediction, querying Cloud SQL for the current waiting list.
o	It calculates the Predicted Wait Time and updates the result back in the Cloud SQL database.

**Step 4**   
        Displaying the Result (Presentation):
        
o	The Patient App and Staff Dashboard retrieve the final, real-time, from the database via the secure API Gateway for immediate display.

## 3. Security, Identity, and Governance basics

This serverless GCP system uses IAM and Service Accounts to manage credentials, so no passwords or API keys are stored in code or environment variables. Each service, such as Cloud Functions and Cloud Run, has its own Service Account for secure authentication. Any external secrets (like third-party API keys) are stored in Secret Manager and only accessible to the services that need them.

Access is tightly controlled using least-privilege roles. Cloud Functions can read input files and write to Cloud SQL, while Cloud Run can read and write only the data required for predictions. Broad roles like Owner or Editor are not used.

To protect patient data, all identifying information is removed early in the process. Sensitive fields (such as names and birthdates) are immediately hashed into non-reversible IDs. Only data with all personal identifiers removed is stored and used, keeping patient information private and the system compliant with regulations.
## 4. Cost and Operational Considerations

The biggest steady expense, or fixed cost, will be the Cloud SQL Database. We need it running 24/7 with high performance for the system to work. The cost that changes the most, or variable cost, comes from Cloud Run (our AI prediction service) because it scales up heavily during busy clinic hours.To save money, we rely on serverless tools instead of traditional, always-on servers (VMs). Cloud Functions only charge us when a patient file actually arrives. The best cost saver is configuring Cloud Run to scale down to zero when the clinic is closed, so we stop paying for compute power when it's not needed. To fit a small budget, we would use the smallest possible Cloud SQL size and aggressively use the free tiers provided by Cloud Storage and Cloud Functions.








