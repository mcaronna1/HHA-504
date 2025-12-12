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

## Service Mapping: 

<img width="468" height="519" alt="image" src="https://github.com/user-attachments/assets/6ac95fcd-8761-4254-86af-e82f3e987e7d" />






