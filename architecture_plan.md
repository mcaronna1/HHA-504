## Architecture & Implementation Plan 


To begin, each cloud service in this system will be described in terms of its specific role, highlighting how it relates to concepts covered in prior coursework. The accompanying visual diagram provides a clear mapping of these services and their interactions. Below is a service mapping of the plan, which gives a clear visualization of the layer, which service cloud could be used, the solution, and which module we learned in class.

## 1. Service Mapping: 
<img width="468" height="307" alt="image" src="https://github.com/user-attachments/assets/03550331-c72a-4d26-ab51-5a55b085d325" />

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








