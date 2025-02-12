Project Objective


SQL Healthcare Data Analysis
The objective of this project is to analyze healthcare data using SQL to derive meaningful insights about patient admissions, doctor assignments, and provincial healthcare distribution. By leveraging SQL queries, we aim to:

Clean and preprocess raw healthcare data for accurate analysis.
Analyze patient admissions to identify trends in hospital visits, common diagnoses, and treatment durations.
Examine doctor-patient interactions to evaluate workload distribution and efficiency.
Assess regional healthcare performance by studying province-wise patient data.
Generate reports that provide actionable insights for healthcare management and decision-making.


--CREATE THE TABLES --


CREATE TABLE patients (
    patient_id INT PRIMARY KEY,
    first_name VARCHAR(30),
    last_name VARCHAR(30),
    gender CHAR(1),
    birth_date DATE,
    city VARCHAR(30),
    province_id CHAR(2),
    allergies VARCHAR(80),
    height INT,
    weight INT,
    FOREIGN KEY (province_id) REFERENCES province_names(province_id)
);

CREATE TABLE doctors (
    doctor_id INT PRIMARY KEY,
    first_name VARCHAR(30),
    last_name VARCHAR(30),
    specialty VARCHAR(25)
);

CREATE TABLE admissions (
    patient_id INT,
    admission_date DATE,
    discharge_date DATE,
    diagnosis VARCHAR(50),
    attending_doctor_id INT,
    PRIMARY KEY (patient_id, admission_date),
    FOREIGN KEY (patient_id) REFERENCES patients(patient_id),
    FOREIGN KEY (attending_doctor_id) REFERENCES doctors(doctor_id)
);

CREATE TABLE province_names (
    province_id CHAR(2) PRIMARY KEY,
    province_name VARCHAR(30)
);


#TASK 1 -- Show first name, last name, and gender of patients whose gender is 'M'
SELECT first_name,last_name,gender from patients where gender like 'M';

#TASK 2 -- Show first name and last name of patients who does not have allergies. (null)
select first_name,last_name from patients where allergies is null;

#TASK 3 -- Show first name of patients that start with the letter 'C'
select first_name from patients where first_name like 'c%';
select first_name from patients where first_name like '%c';
select first_name from patients where first_name like '%c%';

#TASK 4 --- Show first name and last name of patients that weight within the range of 100 to 120 (inclusive)
select first_name, last_name from patients where weight between 100 and 120;

#TASK 5 -- Update the patients table for the allergies column. If the patient's allergies is null then replace it with 'NKA'
set sql_safe_updates = 0;

update patients set allergies = 'NKA' where allergies is null;

#TASK 6 -- Show first name and last name concatinated into one column to show their full name.
 SELECT concat(first_name, " ",last_name) as full_name from patients;
 
 #TASK 7 -- Show first name, last name, and the full province name of each patient.
#Example: 'Ontario' instead of 'ON'
select p.first_name,p.last_name,pn.province_name 
from patients p inner join province_names pn 
on p.province_id = pn.province_id;

#TASK 8 -- Show how many patients have a birth_date with 2010 as the birth year.
select count(*) as birth_year from patients where year(birth_date) = 2010; 

#TASK 9 -- Show the first_name, last_name, and height of the patient with the greatest height.
select first_name,last_name,height from patients order by height desc;

#TASK 10 -- Show all columns for patients who have one of the following patient_ids: 1,45,100,264,480
select * from patients where patient_id in(1,45,100,264,480);

#TASK 11 -- Show the total number of admissions
SELECT COUNT(patient_id) as total_admissions from admissions;

#TASK 12 -- Show all the columns from admissions where the patient was admitted and discharged on the same day.
select * from admissions where admission_date = discharge_date;

#TASK 13 -- Show the patient id and the total number of admissions for patient_id 120.
SELECT patient_id,count(*) as total_admission from admissions where patient_id = 120;

#TASK 14 -- Based on the cities that our patients live in, show unique cities that are in province_id 'AB'.

select distinct city as unique_cities from patients where province_id = 'AB';

#TASK 15 -- Write a query to find the first_name, last name and birth date of patients who has height greater than 160 and weight greater than 70
select first_name,last_name,birth_date from patients where height > 160 and weight > 70;

#TASK 16 -- Write a query to find list of patients first_name, last_name, and allergies where allergies are not null and are from the city of 'lovehaven'
select first_name,last_name, allergies from patients where allergies is not null and city = 'lovehaven';

--
#TASK 17 Show unique birth years from patients and order them by ascending.

select distinct year(birth_date) as unique_year from patients order by unique_year;

#TASK 18 Show unique first names from the patients table which only occurs once in the list.

select distinct first_name from patients group by first_name having count(*) = 1;

#TASK 19 Show patient_id and first_name from patients where their first_name start and ends with 's' and is at least 6 characters long.

select patient_id,first_name from patients
where lenght(first_name) >=6 and first_name like 's%' and first_name like '%s';

#TASK 20 Show patient_id, first_name, last_name from patients whos diagnosis is 'COVID-19'--Primary diagnosis is stored in the admissions table.
select p.patient_id, p.first_name, p.last_name 
from patients p inner join admissions a 
on p.patient_id = a.patient_id 
where diagnosis = 'COVID-19'; 

#TASK 21 Display every patient's first_name. Order the list by the length of each name and then by alphabetically.
select first_name from patients order by lenght(first_name),first_name desc;

#TASK 22 Show the total amount of male patients and the total amount of female patients in the patients table Display the two results in the same row.
select(select count(*) as male from patients where gender = 'M') as male_count,
(select count(*) as female from patients where gender = 'F') as female_count;

#TASK 23 Show first and last name, allergies from patients which have allergies to either 'Dust' or 'pollen'.
# Show results ordered ascending by allergies then by first_name then by last_name.
SELECT first_name,last_name,allergies from patients 
where allergies In( 'Dust','pollen')
order by allergies,first_name,last_name;

#TASK 24 Show patient_id, diagnosis from admissions. Find patients admitted multiple times for the same diagnosis.
select patient_id,diagnosis as multi_d from admissions group by patient_id,diagnosis having count(*) > 1;

#TASK 25 Show the city and the total number of patients in the city.Order from most to least patients and then by city name ascending.
select city,count(*) as total_patients from patients 
group by city
order by total_patients desc,city asc;

#TASK 26 Show first name, last name and role of every person that is either patient or doctor.The roles are either "Patient" or "Doctor"
select first_name,last_name,'patient' as role from patients
union all 
select first_name,last_name,'Doctor' as role from doctors;

#TASK 27 Show all allergies ordered by popularity. Remove NULL values from query.
select allergies,count(allergies) as popularity 
from patients where allergies is not null
group by allergies 
order by popularity desc;

#TASK 28 Show all patient's first_name, last_name, and birth_date who were born in the 1970s decade. Sort the list starting from the earliest birth_date.
select first_name,last_name,birth_date 
from patients 
where year(birth_date) between 1970 and 1979 
order by birth_date;

#TASK 29 We want to display each patient's full name in a single column.
 #Their last_name in all upper letters must appear first, then first_name in all lower case letters.
 #Separate the last_name and first_name with a comma. Order the list by the first_name in decending order EX: SMITH,jane
 
 select concat(upper(last_name),',',lower(first_name)) as full_name 
 from patients order by first_name desc;
 
 #TASK 30 Show the province_id(s), sum of height; where the total sum of its patient's height is greater than or equal to 7,000.
select province_id, sum(height) as total_height 
from patients 
group by province_id 
having total_height > 7000;

#TASK 31 Show the difference between the largest weight and smallest weight for patients with the last name 'Maroni'
select max(weight) - min(weight) as difference from patients where last_name like '%Maroni';

#TASK 32 Show all of the days of the month (1-31) and how many admission_dates occurred on that day. Sort by the day with most admissions to least admissions.
select day(admission_date) as days, count(day(admission_date)) as number_of_admissions
from admissions group by days order by number_of_admissions desc;

#TASK 33 Show all columns for patient_id 342's most recent admission_date.
select * from admissions 
where patient_id = 342
group by admission_date
order by admission_date desc limit 1;

#TASK 34 Show patient_id, attending_doctor_id, and diagnosis for admissions that match one of the two criteria:
#1. patient_id is an odd number and attending_doctor_id is either 1, 5, or 19.
#2. attending_doctor_id contains a 2 and the length of patient_id is 3 characters.

select patient_id,attending_doctor_id,diagnosis
from admissions
where 
patient_id%2!=0 and attending_doctor_id in(1,5,19)
or
attending_doctor_id like '%2%' and length(patient_id)=3;

#TASK 35 Show first_name, last_name, and the total number of admissions attended for each doctor.Every admission has been attended by a doctor.
select first_name,last_name,count(admission_date) as total_admission
from admissions inner join doctors on admissions.attending_doctor_id = doctors.doctor_id
group by attending_doctor_id;

#TASK 36 For each doctor, display their id, full name, and the first and last admission date they attended.
select doctor_id,concat(first_name,' ',last_name) as full_name,
min(admission_date) as first_admission_date, max(admission_date) as last_admission_date
from admissions
inner join
doctors on admissions.attending_doctor_id = doctors.doctor_id
group by doctor_id;

#TASK 37 Display the total amount of patients for each province. Order by descending.
select pn.province_name,count(p.province_id) as total_patients 
from patients p inner join province_names pn
on p.province_id = pn.province_id
group by province_name order by total_patients desc;

#TASK 38 For every admission, display the patient's full name, their admission diagnosis, and their doctor's full name who diagnosed their problem.
select concat(p.first_name,' ',p.last_name) as patient_full_name,
a.diagnosis,concat(d.first_name,' ',d.last_name) as doctor_full_name 
from patients p inner join admissions a using(patient_id)
inner join doctors d 
on d.doctor_id = a.attending_doctor_id;

#TASK 39 display the first name, last name and number of duplicate patients based on their first name and last name.
#Ex: A patient with an identical name can be considered a duplicate.
select first_name,last_name,count(*) as duplicates 
from patients group by first_name,last_name 
having count(*) > 1;

#TASK 40 Display patient's full name,height in the units feet rounded to 1 decimal,weight in the unit pounds rounded to 0 decimals,birth_date,gender non abbreviated.
#Convert CM to feet by dividing by 30.48.
#Convert KG to pounds by multiplying by 2.205.

select concat(first_name,' ',last_name) as full_name,
round(height/30.48,1) as height,
round(weight*2.205,0) as weight,
birth_date,
case 
	when gender = 'M' then 'Male'
    when gender = 'F' then 'Female'
    end
as gender_type from patients;    

#TASK 41 Show patient_id, first_name, last_name from patients whose does not have any records in the admissions table. 
#(Their patient_id does not exist in any admissions.patient_id rows.)

select p.patient_id,p.first_name,p.last_name 
from patients p left join admissions a 
on p.patient_id = a.patient_id 
where a.patient_id is null;

#TASK 42 Display a single row with max_visits, min_visits, average_visits where the maximum, minimum and average number of admissions per day is calculated.
#Average is rounded to 2 decimal places.

SELECT
    MAX(daily_admissions) AS max_visits,
    MIN(daily_admissions) AS min_visits,
    ROUND(AVG(daily_admissions), 2) AS average_visits
FROM (
    SELECT COUNT(patient_id) AS daily_admissions
    FROM admissions
    GROUP BY admission_date
) AS subquery;

#TASK 43 Show all of the patients grouped into weight groups.Show the total amount of patients in each weight group,Order the list by the weight group decending.
#For example, if they weight 100 to 109 they are placed in the 100 weight group, 110-119 = 110 weight group, etc.
SELECT
  count(weight) as patient_group,ROUND((weight / 10) * 10,0) AS grouped_number
FROM patients group by grouped_number order by grouped_number desc;

#TASK 44 Show patient_id, weight, height, isObese from the patients table.Display isObese as a boolean 0 or 1.Obese is defined as weight(kg)/(height(m)2) >= 30.
#weight is in units kg,height is in units cm.

select patient_id,weight,height,
case
	when weight/power(height/100,2) >=30 then 1
    else 0
    end
 as isObese from patients;

#TASK 45 Show patient_id, first_name, last_name, and attending doctor's specialty.
#Show only the patients who has a diagnosis as 'Epilepsy' and the doctor's first name is 'Lisa'
#Check patients, admissions, and doctors tables for required information.

select p.patient_id,p.first_name,p.last_name,d.specialty
from patients p inner join admissions a on p.patient_id = a.patient_id inner join doctors d  on
a.attending_doctor_id = d.doctor_id 
where diagnosis = 'Asthama' and d.first_name like 'lisa%';

#TASK 46 All patients who have gone through admissions, can see their medical documents on our site. 
#Those patients are given a temporary password after their first admission. Show the patient_id and temp_password.
#The password must be the following, in order:
#1. patient_id
#2. the numerical length of patient's last_name
#3. year of patient's birth_date

select distinct patient_id,concat(patient_id,length(last_name),year(birth_date)) as temp_password
from patients inner join admissions using(patient_id);

#TASK 47 Each admission costs $50 for patients without insurance, and $10 for patients with insurance. All patients with an even patient_id have insurance.
#Give each patient a 'Yes' if they have insurance, and a 'No' if they don't have insurance. Add up the admission_total cost for each has_insurance group.
 
select 'No' as has_insurance, count(*) * 50 as cost
from admissions where patient_id % 2 = 1 group by has_insurance
union
select 'Yes' as has_insurance, count(*) * 10 as cost
from admissions where patient_id % 2 = 0 group by has_insurance;

#TASK 48 Show the provinces that has more patients identified as 'M' than 'F'. Must only show full province_name
SELECT 
    province_name
FROM 
    patients inner join province_names on patients.province_id = province_names.province_id
GROUP BY 
    province_name
HAVING 
    COUNT(CASE WHEN gender = 'M' THEN 1 END) > COUNT(CASE WHEN gender = 'F' THEN 1 END);
    
    #And
SELECT pr.province_name
FROM patients AS pa
  JOIN province_names AS pr ON pa.province_id = pr.province_id
GROUP BY pr.province_name
HAVING
  SUM(gender = 'M') > SUM(gender = 'F');
  
#49 We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:
-- First_name contains an 'r' after the first two letters.
-- Identifies their gender as 'F'
-- Born in February, May, or December
-- Their weight would be between 60kg and 80kg
-- Their patient_id is an odd number
-- They are from the city 'Kingston'
select * from patients where first_name like '__r%' and
month(birth_date) in(2,5,12)
and weight between 60 and 80
and patient_id % 2 = 1
and city like 'kingston';

#TASK 50 Show the percent of patients that have 'M' as their gender. Round the answer to the nearest hundreth number and in percent form.
SELECT
  concat(ROUND((SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) * 100.0 / COUNT(*)), 2),'%') AS male_percentage
FROM
  patients;
  
#TASK 51 For each day display the total amount of admissions on that day. Display the amount changed from the previous date.
select admission_date,count(admission_date) as total_admission,
count(admission_date)-lag(count(admission_date))  over() as admission_count_change from
admissions group by admission_date;

#TASK 52 - Sort the province names in ascending order in such a way that the province 'Ontario' is always on top.
select province_name from province_names
order by 
case when province_name = 'Ontario' then 0
	 else 1
end;

#TASK 53 We need a breakdown for the total amount of admissions each doctor has started each year.
-- Show the doctor_id, doctor_full_name, specialty, year, total_admissions for that year.

select d.doctor_id,
concat(d.first_name,' ',d.last_name) as full_name,
d.specialty,
year(a.admission_date) as years,
count(a.admission_date) as total_admission 
from admissions a inner join doctors d
on a.attending_doctor_id = d.doctor_id 
group by d.specialty,years,full_name,d.doctor_id order by d.doctor_id;
