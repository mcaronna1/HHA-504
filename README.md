# HHA-504 Final Project: Cloud Integration Mini-Capstone

## TITLE: Optimizing Patient Experience Through Wait Time Prediction 

## Case Description 

Outpatient healthcare facilities are always scrutinized by patient experience, where wait time stands out as a critical especially for patients with a scheduled doctor's appointment. Long wait times are often frustrating and diminish patient satisfaction, lead to missed appointments, and contribute to operational inefficiencies within the clinic. In today’s healthcare environment, optimizing resources and prioritizing patient engagement are important, and relying on historical data and informal insights is no longer enough. This paper proposes to attempt to build a comprehensive cloud-based solution architecture designed to address this challenge by providing a prediction of patient wait times from check-in to provider start time. The primary goal is to provide patients with accurate, real-time estimates of their expected wait times so they can better plan their day, while also equipping clinic leadership with actionable insights to support continuous improvement and a more effective resource for scheduling. 

  
The core problem is the unpredictable and often excessive delay experienced by patients waiting to see a provider after checking in for a scheduled appointment. The patient has built up anxiety, worried about results or what will be told to them, only to end up waiting long period of time, leaving the patient dissatisfied. The problem that is trying to be solved is the lack of transparency and predictability in patient wait times by building a system to predict the time a patient will see their doctor in real time, communicating this estimate to the patient, as well as providing insight analysis to the staff.

Users who will be the patients who will receive real-time estimated wait times, all in a mobile app. Clinic staff, such as the front desk, as well as the clinical team. They will have input check-in times and monitor clinic flow by using a dashboard to proactively manage delays. Clinic Management that relies on aggregated daily/weekly data to streamline optimized provider schedules and staffing levels.The system relies on three distinct types of data, securely sourced from the outpatient to facility system, primarily from the Electronic Health Record (EHR).  Data source summary organized by the clinic’s EHR system. The most critical data for a wait time prediction is real-time data, which will help capture patient timestamps, such as when the patient checked in and when the provider saw the patient, all by using API events to calculate status and historical wait times. This would be supplemented by scheduled batch data that provides appointment context, such as appointment types or expected duration. Then, reference data, such as using a provider schedule, background data, will help support planning. The data will be stripped of its identifiers as soon as it is uploaded to the cloud, making sure it is in compliance while supporting real-time predictions.

## Architecture Diagram

A basic workflow through the system streamlining real time all by real-time events, such as first the patient being checked in, and what follows.  Once the patient is checked in, the EHR system records the check-in timestamp. Data ingestion or an integration layer will securely send the new update patient flow data (JSON/event message) to the cloud environment. The raw data will be stored in a secure cloud storage bucket. Now it has moved into a processing and transformation stage, and the arrival of new data triggers a serverless data pipeline, which calculates the necessary time, such as wait times.  Database load structured and cleaned data is loaded into the managed SQL database for persistent storage as the source for the prediction model. Prediction- containerized computer service that hosts a machine learning model, which retrieves the current waiting list and data from the SQL database, and calculates the predicted wait time for each active patient. The API gateway exposes a secure endpoint.
The architecture diagram that has been created uses a serverless-first approach for scalability and cost efficiency, with a managed relational database for structured data.
 
To begin, each cloud service in this system will be described in terms of its specific role, highlighting how it relates to concepts covered in prior coursework. The accompanying visual diagram provides a clear mapping of these services and their interactions. Below is a service mapping of the plan, which gives a clear visualization of the layer, which service cloud could be used, the solution, and which module we learned in class.

<img width="426" height="212" alt="image" src="https://github.com/user-attachments/assets/1e2c2eb6-986f-4d28-a780-f2bf57158a82" />

## Architecture & Implementation Plan 


To begin, each cloud service in this system will be described in terms of its specific role, highlighting how it relates to concepts covered in prior coursework. The accompanying visual diagram provides a clear mapping of these services and their interactions. Below is a service mapping of the plan, which gives a clear visualization of the layer, which service cloud could be used, the solution, and which module we learned in class.

## Service Mapping: 

<img width="468" height="519" alt="image" src="https://github.com/user-attachments/assets/6ac95fcd-8761-4254-86af-e82f3e987e7d" />

## Data Flow Narrative

The end-to-end flow of this data is securely moved, processed, and utilized for real-time prediction of chosen services from GCP, Azure, and OCI:

* **Step 1**: 
        Data Ingestion (GCP): The EHR system sends patient flow data (JSON/CSV) securely to Cloud Storage (GCP).
* **Step 2**: 
        ETL Trigger (Azure): The arrival of the new file in GCP Storage triggers the Azure Functions service.
* **Step 3**: 
        Transformation & Security (Azure): Azure Functions performs ETL, validating data and completing the mandatory hashing and de-identification of PHI.
* **Step 4**:
        Database Load (OCI): The Azure Function writes the cleaned, de-identified data into the Autonomous Database (OCI).
* **Step 5**:
        AI Calculation (Azure): Azure ML queries OCI, executes the ML model to calculate the predicted wait time, and writes the results back to the OCI Database.
* **Step 6**:
        Presentation: Frontend applications (Patient App/Staff Dashboard) query the OCI Database via the Access Layer to display real-time predictions and metrics.

## Security, Identity, and Governance basics

Managing credentials in this multi-cloud environment requires reliable, centralized methods. We will implement cloud-based authentication without storing sensitive credentials: GCP Service Accounts (for Cloud Storage), Azure Managed Identities (for Azure Functions and Azure ML), and OCI IAM Dynamic Groups (for Autonomous Database access). These services assign only the minimum permissions needed, so there’s no need to store API keys or passwords in environment variables.
Strict Role-Based Access Control (RBAC) is applied to control data access. The Azure Function will only have read permission on the GCP Storage bucket and write permission on specific tables in the OCI Autonomous Database. The Azure ML service will only have read access to the input data tables and write access to the prediction results table within OCI. From a high level, real Protected Health Information (PHI) is prevented from entering the public cloud environment by enforcing a mandatory de-identification step within the Azure Functions ETL code. Patient IDs are immediately hashed and salted; only these non-reversible identifiers are ever stored in the OCI Database.

## Cost and Operational Considerations

The OCI database is expected to have the highest fixed cost because it is a managed, highly available system that needs reliable uptime and consistent performance. The second most significant cost will be Compute (Azure ML) due to the need for dedicated resources to train and continuously run the machine learning inference model. Cloud Storage (GCP) is typically low-cost, following a pay-per-use model.
To reduce costs, we use serverless services whenever possible. Azure Functions are perfect for ETL tasks because they only run (and charge) when new data arrives, so there’s no cost for always-on servers. Although the Azure ML model may need a persistent endpoint, its resources can be scaled down during slow clinic hours to save money. To stay within a "student budget," we choose the free or smallest usage-based plans for Azure Functions and Cloud Storage and pick the lowest-resource option available for the OCI Autonomous Database.

## Reflection:
I feel most confident about the ETL and De-identification Pipeline (GCP Storage Azure Functions $\rightarrow$ OCI Database). The combination of event-triggered serverless compute (Azure Functions) responding to file storage (GCP) is a mature and highly reliable cloud pattern. Furthermore, placing the crucial de-identification logic within this serverless function ensures security and compliance are handled immediately upon ingestion
 I’m most concerned about the delay caused by using multiple cloud providers and the risk of being tied to one vendor. Since the ETL runs on Azure, the raw data is stored in GCP, and the database is in OCI, data has to travel between these different clouds. This can slow things down, which is not good for a system that needs to work in real-time. Also, using OCI’s Autonomous Database for the main data means we depend heavily on one provider for a very important part of the system.
An alternative architecture considered was a Single-Cloud Solution, such as GCP, which was one of my favorites to use during our assignments, but a single cloud solution was not chosen for this purpose, for this exercise was to demonstrate the ability to integrate services across multiple distinct cloud providers (GCP, Azure, OCI) for strategic vendor diversification and leveraging provider-specific strengths.
If given extra time and resources, the primary goal is to use Azure ML to automate daily model re-training with new data from OCI and implement testing for smooth model updates. For better system architecture, we would add a secure OCI API Gateway layer in front of the Autonomous Database. This protects the database, enforces security rules, and provides a faster, standardized access point for all applications. To improve patient care, we would implement Proactive Patient Communication by integrating a notification service (like Azure or OCI Notifications). This allows the system to send automatic text push alerts to patients if their predicted wait time changes significantly, so they will be seen much earlier or much later. We would establish better Governance and Reporting. This involves building an Analytics Dashboard to track three key things in one place: the model's accuracy, the system's performance, and the aggregated operational billing costs across all three clouds (GCP, Azure, OCI).













