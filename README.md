## www.sql-practice.com

This repository is dedicated to students and professionals who would like to brush up and advance their SQL skills for their careers. I found (https://www.sql-practice.com) educational and the web site user friendly however there wasn’t the complete solution book shared on the web site neither on GitHub. So now you can get the answers with alternative solutions for each question 

I have used PostgreSQL as main Database Management Systems (DBMS) and therefore you may encounter some queries that work in PostgreSQL may not work in
other DBMS such as SQL Server or MySQL as some implementations slight vary in syntax. For such cases, for example, in date and time operations 
I included standart SQL queries as well.

I populated mockdata using www.mockaroo.com to effectively practise the queries and get some output while working locally. Note that it is not the same large dataset used on the web site, so I suggest you use the web site itself as long as you have internet connection, which is also good for tracking your progress. You may use the sql database files given in the repository for convenience and a rapid start in creating schemas and inserting datasets.



The hospital test contains 50 questions and consists of 3 category:

- Easy
- Medium
- Hard



## Quastions & Answers

> Want to help this repository? Feel free to contribute by submitting a custom solution to be added to the questions.

---

### Section 1 : Easy

---

Questions 1- 16

1. Show first name, last name, and gender of patients who's gender is 'M'

```sql
select
	first_name,
	last_name,
	gender
from patients
where gender = 'M';
```

2. Show first name and last name of patients who does not have allergies. (null)

 ```sql
select
	first_name,
	last_name
from patients
where allergies is null;

```

3. Show first name of patients that start with the letter 'C'

```sql
select
	first_name
from patients
where first_name like 'C%';
```

4. Show first name and last name of patients that weight within the range of 100 to 120 (inclusive)

```sql
select
	first_name,
	last_name
from patients
where weight between 100 and 120;
```

5. Update the patients table for the allergies column. If the patient's allergies is null then replace it with 'NKA'

```sql
update patients
set allergies = 'NKA'
where allergies is null;
```

6. Show first name and last name concatenated into one column to show their full name.

```sql
select concat(first_name,' ',last_name) as full_name 
from patients;

--or

SELECT first_name || ' ' || last_name
FROM patients;
```

7. Show first name, last name, and the full province name of each patient.

```sql
select
	p1.first_name,
	p1.last_name,
	p2.province_name
from patients p1
	inner join province_names p2 on p1.province_id = p2.province_id
```

8. Show how many patients have a birth_date with 2010 as the birth year.

```sql
select count(*) as total_patients
from patients
where year(birth_date) = 2010
```

9. Show the first_name, last_name, and height of the patient with the greatest height.

```sql
select
	first_name,
	last_name ,
	height
from patients
order by 3 desc
limit 1;

--or 
 
select
	first_name,
	last_name ,
	height
from patients
where height = (
			select max(height) from patients
		);
--or               
                
SELECT
	first_name,
	last_name,
	MAX(height) AS height
FROM patients;
```

10. Show all columns for patients who have one of the following patient_ids:
    1,45,534,879,1000

```sql
select *
from patients
where patient_id in (1,45,534,879,1000);
```

11. Show the total number of admissions

```sql
select count(*) as total_admission
from admissions
```

12. Show all the columns from admissions where the patient was admitted and discharged on the same day.

```sql
select *
from admissions
where admission_date = discharge_date;
```

13. Show the total number of admissions for patient_id 579.

```sql
select
	patient_id,
	count(*) total_addmisions
from admissions
where patient_id = 579
```

14. Based on the cities that our patients live in, show unique cities that are in province_id 'NS'?

```sql
select  distinct (city) as unique_cites 
from patients p1
	join province_names p2 on p1.province_id = p2.province_id
where p2.province_id = 'NS';

--or

SELECT city
FROM patients
GROUP BY city
HAVING province_id = 'NS';
```

15. Write a query to find the first_name, last name and birth date of patients who have height more than 160 and weight more than 70

```sql
select
	first_name,
	last_name,
	birth_date 
 from patients
 where height > 160 and weight > 70

```

16. Write a query to find list of patients first_name, last_name, and allergies from Hamilton where allergies are not null

```sql
select
	first_name,
	last_name,
	allergies
from patients
where allergies is not null and city = 'Hamilton';
```

### Section 2 : Medium

---

Questions 1- 25

1. Show unique birth years from patients and order them by ascending.

```sql
-- PostgreSQL
SELECT DISTINCT (EXTRACT(YEAR FROM birth_date)) AS birth_year
FROM patients
ORDER BY birth_year ASC;

-- SQL
select distinct year(birth_date) as unique_year
from patients
order by 1 asc;
```

2. Show unique first names from the patients table which only occurs once in the list.
   For example, if two or more people are named 'John' in the first_name column then don't include their name in the output list.
   If only 1 person is named 'Leo' then include them in the output.

```sql
select
	first_name
from patients
group by 1
having count(first_name) = 1;

--or

select first_name
from (
    select
      first_name,
      count(first_name) AS occurrencies
    from patients
    group by first_name
  )
where occurrencies = 1
```

    Tip: HAVING clause was added to SQL because the WHERE keyword cannot be used with aggregate functions.

3. Show patient_id and first_name from patients where their first_name start and ends with 's' and is at least 6 characters long.

```sql
SELECT
	patient_id,
	first_name
FROM patients
WHERE first_name like '%s' and first_name like 's%' and len(first_name) >= 6;
```

4. Show patient_id, first_name, last_name from patients whos diagnosis is 'Dementia'.
   Primary diagnosis is stored in the admissions table.

```sql
select
p.patient_id,
p.first_name,
p.last_name
from patients p
	join admissions a on p.patient_id = a.patient_id
where diagnosis = 'Dementia';

--or

SELECT
	patient_id,
	first_name,
	last_name
FROM patients
WHERE patient_id IN (
    SELECT patient_id
    FROM admissions
    WHERE diagnosis = 'Dementia'
  );

```

5. Display every patient's first_name.
   Order the list by the length of each name and then by alphbetically.

```sql
select first_name
from patients
order by len(first_name) , first_name;
```

6. Show the total amount of male patients and the total amount of female patients in the patients table.
   Display the two results in the same row.

```sql
-- Postgresql
SELECT (SELECT COUNT(*) FROM patients WHERE gender = 'M') AS male_count,
       (SELECT COUNT(*) FROM patients WHERE gender = 'F') AS female_count;

-- SQL
SELECT 
  SUM(Gender = 'M') as male_count, 
  SUM(Gender = 'F') AS female_count
FROM patients
```

7. show first and last name, allergies from patients which have allergies to either 'Penicillin' or 'Morphine'.
   Show results ordered ascending by allergies then by first_name then by last_name.

```sql
select
	first_name,
	last_name,
	allergies
from patients
where allergies = 'Penicillin' or allergies = 'Morphine'
order by 3, 1,2;
```

8. Show patient_id, diagnosis from admissions. Find patients admitted multiple times for the same diagnosis.

```sql
select
	patient_id,
	diagnosis
from admissions
GROUP BY patient_id, diagnosis
HAVING COUNT(*) > 1;

```

9. Show the city and the total number of patients in the city.
   Order from most to least patients and then by city name ascending.

```sql
SELECT
	city,
	count(*) as total_patients
from patients
group by city
order by 2 desc,1;
```

10. Show first name, last name and role of every person that is either patient or doctor.
    The roles are either "Patient" or "Doctor"

```sql
SELECT
	first_name,
	last_name,
	'Patient' AS role
FROM patients

UNION ALL

SELECT
	first_name,
	last_name,
	'Doctor' AS role
FROM doctors;
```

11. Show all allergies ordered by popularity. Remove NULL values from query.

```sql
select
	allergies,
	count(*) as total_diagnosis
from patients 
group by 1
having allergies is not null
order by 2 desc
```

12. Show all patient's first_name, last_name, and birth_date who were born in the 1970s decade.
    Sort the list starting from the earliest birth_date.

```sql
--PostgreSQL version
SELECT first_name, last_name, birth_date
FROM patients
WHERE EXTRACT(YEAR FROM birth_date) BETWEEN 1970 AND 1979;

--SQL
select
	first_name,
	last_name,
	birth_date
from patients
where year(birth_date) >= 1970 and year(birth_date) <= 1979 
order by birth_date desc;

-- SQL Regex
SELECT first_name,
       last_name,
       birth_date
FROM patients
WHERE year(birth_date) LIKE '197%'
ORDER BY birth_date ASC
```

13. We want to display each patient's full name in a single column.
    Their last_name in all upper letters must appear first, then first_name in all lower case letters.
    Separate the last_name and first_name with a comma. Order the list by the first_name in decending order
    EX: SMITH,jane

```sql
select concat(upper(last_name),',',lower(first_name)) as new_Fromatted_name
from patients
order by first_name desc;
```

14. Show the province_id(s), sum of height; where the total sum of its patient's height is greater than or equal to 7,000.

```sql
select
	province_id,
	sum(height) as total_height
from patients
group by 1
having sum(height) >= 7000;

```

15. Show the difference between the largest weight and smallest weight for patients with the last name 'Maroni'

```sql
select (max(weight) - min(weight)) as weight_diff
from patients
where last_name like '%Maroni';
```

16. Show all of the days of the month (1-31) and how many admission_dates occurred on that day.
    Sort by the day with most admissions to least admissions.

```sql
-- PostgreSQL
SELECT EXTRACT(DAY FROM admission_date) AS day_number, COUNT(patient_id) AS number_of_admissions
FROM admissions
GROUP BY day_number
ORDER BY number_of_admissions DESC;

-- SQL
Sselect day(admission_date),count(*) as total_addmissions 
from admissions
group by 1
order by 2 desc;
```

17. Show all columns for patient_id 542's most recent admission_date.

```sql
-- PostgreSQL
SELECT *
FROM admissions
WHERE patient_id = 542
GROUP BY patient_id, admission_date, discharge_date, diagnosis, attending_doctor_id
HAVING admission_date = MAX(admission_date);

-- sql
select * 
from admissions
where patient_id = 542
order by admission_date desc
limit 1;

```

18. Show patient_id, attending_doctor_id, and diagnosis for admissions that match one of the two criteria:
    - patient_id is an odd number and attending_doctor_id is either 1, 5, or 19.
    - attending_doctor_id contains a 2 and the length of patient_id is 3 characters.

```sql
-- PostgreSQL (we would need to explicity cast the type attending_doctor_id is integer whereas LIKE method performs operations on Strings
SELECT patient_id, attending_doctor_id, diagnosis
FROM admissions
WHERE patient_id % 2 = 1 AND attending_doctor_id IN (1, 5, 19)
   OR CONCAT(attending_doctor_id) LIKE '%2%' AND LENGTH(CONCAT(patient_id)) = 3;

-- SQL
select
	patient_id,
	attending_doctor_id,
	diagnosis
from admissions
where patient_id % 2 = 1
	and attending_doctor_id in(1,5,19) 
	or attending_doctor_id like '%2%' 
	and len(patient_id) = 3

```

19. Show first_name, last_name, and the total number of admissions attended for each doctor.
    Every admission has been attended by a doctor.

```sql
select
d.first_name,
d.last_name,
count(a.patient_id) as total_admission
from doctors d
	join admissions a on d.doctor_id = a.attending_doctor_id
group by d.first_name,d.last_name
```

20. For each doctor, display their id, full name, and the first and last admission date they attended.

```sql
select 
	d.doctor_id as id,
	concat(d.first_name , ' ', d.last_name) as full_name,
	min(a.admission_date) as first_date ,
	max(a.admission_date) as last_date
from admissions a 
	join doctors d on a.attending_doctor_id = d.doctor_id
group by 1
```

21. Display the total amount of patients for each province. Order by descending.

```sql
select 
	2.province_name,
	count(*) as patient_count
from province_names p2
	join patients p1 on p1.province_id = p2.province_id
group by 1
order by 2 desc
```

22. For every admission, display the patient's full name, their admission diagnosis, and their doctor's full name who diagnosed their problem.

```sql
select
	concat(p.first_name,' ',p.last_name) as full_name,
	a.diagnosis as diagnosis,
	concat(d.first_name,' ', d.last_name) as doctor_name
from patients p
	join admissions a on a.patient_id = p.patient_id
	join doctors d on a.attending_doctor_id = d.doctor_id
```

23. Display the number of duplicate patients based on their first_name and last_name.

```sql

select
	first_name,
	last_name ,count(*)
from patients
group by 1,2
having count(*)>1

```

24. Display patient's full name,height in the units feet rounded to 1 decimal,weight in the unit pounds
    rounded to 0 decimals,birth_date,gender non abbreviated.Convert CM to feet by dividing by 30.48. Convert KG to pounds by multiplying by 2.205.
```sql

select
    concat(first_name,' ', last_name) as full_name,
    round(height/30.48,1) as height_in_feet,
    round(weight * 2.205,0) as weight_in_pound,
    birth_date,
    case 
    	when gender = 'M' then 'MALE'
    	else 'FEMALE'
    end as gender
from patients

```

25. Show patient_id, first_name, last_name from patients whose does not have any records in the admissions table. 
    (Their patient_id does not exist in any admissions.patient_id rows.)
```sql

select
	p.patient_id,
	p.first_name,
	p.last_name 
from patients as p  
	Left join admissions as a on p.patient_id = a.patient_id 
where a.patient_id is null; 

```

---

### Section 3 : Hard

---

Questions 1- 10

1. Show all of the patients grouped into weight groups.
   Show the total amount of patients in each weight group.
   Order the list by the weight group decending. e.g. if they weight 100 to 109 they are placed in the 100 weight group, 110-119 = 110 weight group,
   etc.

```sql
SELECT (weight / 10) * 10 AS weight_group, COUNT(*) AS patients_in_this_weight_group
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
```

2. Show patient_id, weight, height, isObese from the patients table. Display isObese as a boolean 0 or 1. Obese is defined as weight(kg)/(height(m)
   . Weight is in units kg. Height is in units cm.

```sql
SELECT patient_id,
       weight,
       height,
       CASE
           WHEN weight / POWER(height / 100.00, 2) >= 30
               THEN 1
           ELSE 0
           END AS isobese
FROM patients;
-- we need to divide by 100.00 as if we divide int by int, the decimals will be truncated, ex: 100/3 = 33 BUT 100/3.0 = 33.333333333
```

3. Show patient_id, first_name, last_name, and attending doctor's specialty.
   Show only the patients who has a diagnosis as 'Epilepsy' and the doctor's first name is 'Lisa'
   Check patients, admissions, and doctors tables for required information.

```sql
SELECT p.patient_id, p.first_name, p.last_name, d.speciality
FROM patients p
         JOIN admissions a ON p.patient_id = a.patient_id
         JOIN doctors d ON a.attending_doctor_id = d.doctor_id
WHERE a.diagnosis = 'Epilepsy'
  AND d.first_name = 'Lisa';
```

4. All patients who have gone through admissions, can see their medical documents on our site. Those patients are given a temporary password after
   their first admission. Show the patient_id and temp_password.

   The password must be the following, in order:
    - patient_id
    - the numerical length of patient's last_name
    - year of patient's birth_date

```sql
--PostgreSQL
SELECT DISTINCT p.patient_id, CONCAT(a.patient_id, LENGTH(p.last_name), EXTRACT(YEAR FROM p.birth_date)) AS temp_password
FROM patients p
         JOIN admissions a ON p.patient_id = a.patient_id;

--SQL
SELECT DISTINCT p.patient_id, CONCAT(a.patient_id, LENGTH(p.last_name), year(p.birth_date)) AS temp_password
FROM patients p
         JOIN admissions a ON p.patient_id = a.patient_id;
```

5. Each admission costs $50 for patients without insurance, and $10 for patients with insurance. All patients with an even patient_id have insurance.
   Give each patient a 'Yes' if they have insurance, and a 'No' if they don't have insurance. Add up the admission_total cost for each has_insurance
   group.

```sql
SELECT CASE WHEN a.patient_id % 2 = 0 THEN 'Yes' ELSE 'No' END AS has_insurance,
       SUM(CASE WHEN a.patient_id % 2 = 0 THEN 10 ELSE 50 END) AS cost_after_insurance
FROM admissions a
GROUP BY has_insurance;
```

6. Show the provinces that has more patients identified as 'M' than 'F'. Must only show full province_name

```sql
SELECT pr.province_name
FROM province_names pr
         JOIN patients p ON pr.province_id = p.province_id
GROUP BY province_name
HAVING SUM(CASE WHEN p.gender = 'M' THEN 1 ELSE 0 END) > SUM(CASE WHEN p.gender = 'F' THEN 1 ELSE 0 END);

/*
COUNT is used to count the number of rows that meet a certain criteria, 
while SUM is used to add up the values in a particular column for rows that meet a certain criteria.
*/
```

7. We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:

- First_name contains an 'r' after the first two letters.
- Identifies their gender as 'F'
- Born in February, May, or December
- Their weight would be between 60kg and 80kg
- Their patient_id is an odd number
- They are from the city 'Kingston'

```sql
-- PostgreSQL
SELECT *
FROM patients
WHERE first_name SIMILAR TO '^[a-zA-Z]{2}r'
  AND gender = 'F'
  AND EXTRACT(MONTH FROM (birth_date)) IN (2, 5, 12)
  AND weight BETWEEN 60 AND 80
  AND patient_id % 2 = 1
  AND city = 'Kingston';

-- SQL
SELECT *
FROM patients
WHERE first_name LIKE '__r%' --first_name LIKE '^[a-zA-Z]{2}r'
  AND gender = 'F'
  AND month(birth_date) IN (2, 5, 12)
  AND weight BETWEEN 60 AND 80
  AND patient_id % 2 = 1
  AND city = 'Kingston';

```

8. Show the percent of patients that have 'M' as their gender. Round the answer to the nearest hundreth number and in percent form.

```sql
SELECT CONCAT(ROUND(SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2), '%')
FROM patients;

SELECT ROUND(100 * AVG(gender = 'M'), 2) || '%' AS percent_of_male_patients
FROM patients;
```

9. For each day display the total amount of admissions on that day. Display the amount changed from the previous date.

```sql
WITH temp_table AS (SELECT admission_date,
                           COUNT(*) AS total_admissions
                    FROM admissions
                    GROUP BY admission_date
                    ORDER BY admission_date DESC)
SELECT admission_date,
       total_admissions,
       total_admissions - LAG(total_admissions, 1, NULL) OVER (ORDER BY admission_date DESC) AS change
FROM temp_table;

-- Alternative 
SELECT admission_date,
       COUNT(admission_date)                                                             AS admission_day,
       COUNT(admission_date) - LAG(COUNT(admission_date)) OVER (ORDER BY admission_date) AS admission_count_change
FROM admissions
GROUP BY admission_date;
```

10. Sort the province names in ascending order in such a way that the province 'Ontario' is always on top.

```sql
WITH temp_table AS (SELECT province_name FROM province_names ORDER BY (province_name = 'Ontario') DESC, province_name ASC)
SELECT province_name
FROM temp_table;

/*
This query uses a combination of the ORDER BY clause and a boolean expression to prioritize the 'Ontario' record.
The boolean expression (name = 'Ontario') returns TRUE for the 'Ontario' record and FALSE for all other records.
When this expression is used in the ORDER BY clause with the DESC keyword, it causes the 'Ontario' record to be sorted first in the resulting dataset.
The rest of the records are then sorted in ascending order by the name column.
*/
```

11. We need a breakdown for the total amount of admissions each doctor has started each year. Show the doctor_id, doctor_full_name, specialty, year, total_admissions for that year.

```sql
SELECT d.doctor_id, CONCAT(d.first_name,' ',d.last_name) AS doctor_full_name,
		 d.specialty,
       YEAR(a.admission_date) AS selected_year,
       COUNT(*) AS total_admissions
FROM admissions a
LEFT JOIN doctors d ON a.attending_doctor_id = d.doctor_id
GROUP BY doctor_id, selected_year
```
