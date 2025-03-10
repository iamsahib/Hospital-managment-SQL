**Project Objective**


**SQL Healthcare Data Analysis**
The objective of this project is to analyze healthcare data using SQL to derive meaningful insights about patient admissions, doctor assignments, and provincial healthcare distribution. By leveraging SQL queries, we aim to:

Clean and preprocess raw healthcare data for accurate analysis.

Analyze patient admissions to identify trends in hospital visits, common diagnoses, and treatment durations.

Examine doctor-patient interactions to evaluate workload distribution and efficiency.

Assess regional healthcare performance by studying province-wise patient data.

Generate reports that provide actionable insights for healthcare management and decision-making.



**Relationships**

patients ↔ province_names (Many-to-One via province_id)

patients ↔ admissions (One-to-Many via patient_id)

doctors ↔ admissions (One-to-Many via doctor_id)

patients ↔ images (One-to-Many via patient_id)

doctors ↔ images (One-to-Many via doctor_id)

