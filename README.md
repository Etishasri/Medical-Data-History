# `Medical Data History Queries`

## Alter Table
Change the column name `attending_doctor_id` to `doctor_id` in the `admissions` table to merge it with the `doctors` table.

```sql
ALTER TABLE admissions CHANGE attending_doctor_id doctor_id INT;
```

## Checking Table Data
View all records in the `admissions`, `doctors`, `patients`, and `province_names` tables.

```sql
USE project_medical_data_history;

SELECT * FROM admissions;
SELECT * FROM doctors;
SELECT * FROM patients;
SELECT * FROM province_names;
```

---

## Queries

1. **Show first name, last name, and gender of male patients.**  
   ```sql
   SELECT first_name, last_name, gender FROM patients WHERE gender = 'M';
   ```

2. **Show first name and last name of patients without allergies.**  
   ```sql
   SELECT first_name, last_name FROM patients WHERE allergies IS NULL;
   ```

3. **Show first name of patients that start with the letter 'C'.**  
   ```sql
   SELECT first_name FROM patients WHERE first_name LIKE 'C%';
   ```

4. **Show first name and last name of patients with weight between 100 and 120.**  
   ```sql
   SELECT first_name, last_name FROM patients WHERE weight BETWEEN 100 AND 120;
   ```

5. **Update allergies column: Replace `NULL` with `'NKA'`.**  
   ```sql
   UPDATE patients SET allergies = 'NKA' WHERE allergies IS NULL;
   ```

6. **Show full name as a single column.**  
   ```sql
   SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM patients;
   ```

7. **Show first name, last name, and province name of each patient.**  
   ```sql
   SELECT first_name, last_name, province_name FROM patients 
   JOIN province_names ON patients.province_id = province_names.province_id;
   ```

8. **Count patients born in 2010.**  
   ```sql
   SELECT COUNT(*) AS total_patients FROM patients WHERE YEAR(birth_date) = 2010;
   ```

9. **Show the tallest patient's details.**  
   ```sql
   SELECT first_name, last_name, height FROM patients 
   WHERE height = (SELECT MAX(height) FROM patients);
   ```

10. **Show all columns for specific patient IDs (1, 45, 534, 879, 1000).**  
    ```sql
    SELECT * FROM patients WHERE patient_id IN (1, 45, 534, 879, 1000);
    ```

11. **Show the total number of admissions.**  
    ```sql
    SELECT COUNT(*) AS number_of_admissions FROM admissions;
    ```

12. **Show all admissions where the patient was admitted and discharged on the same day.**  
    ```sql
    SELECT * FROM admissions WHERE admission_date = discharge_date;
    ```

13. **Show the total number of admissions for patient ID 579.**  
    ```sql
    SELECT COUNT(*) AS total_admissions FROM admissions WHERE patient_id = 579;
    ```

14. **Show unique cities in province ID 'NS'.**  
    ```sql
    SELECT DISTINCT city FROM patients WHERE province_id = 'NS';
    ```

15. **Find patients with height > 160 and weight > 70.**  
    ```sql
    SELECT first_name, last_name, birth_date FROM patients WHERE height > 160 AND weight > 70;
    ```

16. **Show unique birth years from patients, ordered in ascending order.**  
    ```sql
    SELECT DISTINCT YEAR(birth_date) AS birth_year FROM patients ORDER BY birth_year;
    ```

17. **Show unique first names that occur only once in the database.**  
    ```sql
    SELECT first_name FROM patients GROUP BY first_name HAVING COUNT(first_name) = 1;
    ```

18. **Show patients whose first name starts and ends with 's' and is at least 6 characters long.**  
    ```sql
    SELECT patient_id, first_name FROM patients WHERE first_name LIKE 's%s' AND LENGTH(first_name) = 6;
    ```

19. **Show patient_id, first_name, last_name for patients diagnosed with 'Dementia'.**  
    ```sql
    SELECT patients.patient_id, first_name, last_name, diagnosis 
    FROM patients 
    JOIN admissions ON patients.patient_id = admissions.patient_id 
    WHERE diagnosis = 'Dementia';
    ```

20. **Show total male and female patients in a single row.**  
    ```sql
    SELECT 
        COUNT(CASE WHEN gender = 'M' THEN 1 END) AS male_patients, 
        COUNT(CASE WHEN gender = 'F' THEN 1 END) AS female_patients 
    FROM patients;
    ```

21. **Show patient_id and first_name of patients admitted multiple times for the same diagnosis.**  
    ```sql
    SELECT diagnosis, patient_id, COUNT(*) AS admission_count 
    FROM admissions 
    GROUP BY diagnosis, patient_id 
    HAVING admission_count > 1;
    ```

22. **Show the city and total number of patients in each city, ordered by most to least patients.**  
    ```sql
    SELECT city, COUNT(*) AS total_patients 
    FROM patients 
    GROUP BY city 
    ORDER BY total_patients DESC, city ASC;
    ```

23. **Show all allergies ordered by popularity (excluding NULL values).**  
    ```sql
    SELECT allergies, COUNT(*) AS popularity 
    FROM patients 
    WHERE allergies IS NOT NULL 
    GROUP BY allergies 
    ORDER BY popularity DESC;
    ```

24. **Show patients born in the 1970s decade, sorted from earliest birth_date.**  
    ```sql
    SELECT first_name, last_name, birth_date 
    FROM patients 
    WHERE birth_date BETWEEN '1970-01-01' AND '1979-12-31' 
    ORDER BY birth_date ASC;
    ```

25. **Display patients' full names formatted as `LASTNAME,firstname`, ordered by first name descending.**  
    ```sql
    SELECT CONCAT(UPPER(last_name), ',', LOWER(first_name)) AS full_name 
    FROM patients 
    ORDER BY first_name DESC;
    ```

26. **Show province_id where total patient height is greater than or equal to 7,000.**  
    ```sql
    SELECT province_id, SUM(height) AS total_height 
    FROM patients 
    GROUP BY province_id 
    HAVING SUM(height) >= 7000;
    ```

27. **Show weight difference for patients with the last name 'Maroni'.**  
    ```sql
    SELECT MAX(weight) - MIN(weight) AS weight_difference 
    FROM patients 
    WHERE last_name = 'Maroni';
    ```

28. **Show number of admissions per day of the month (1-31), ordered by most to least.**  
    ```sql
    SELECT DAY(admission_date) AS day_of_month, COUNT(*) AS admissions_occurred 
    FROM admissions 
    GROUP BY day_of_month 
    ORDER BY admissions_occurred DESC;
    ```

29. **Group patients into weight ranges and count each group, ordered descending.**  
    ```sql
    SELECT 
        CASE 
            WHEN weight BETWEEN 0 AND 50 THEN '0-50'
            WHEN weight BETWEEN 51 AND 100 THEN '51 - 100'
            WHEN weight BETWEEN 101 AND 150 THEN '101 - 150'
            ELSE '150+' 
        END AS weight_group, 
        COUNT(*) AS total_patients 
    FROM patients 
    GROUP BY weight_group 
    ORDER BY weight_group DESC;
    ```

30. **Generate a temporary password for patients using their patient_id, last name length, and birth year.**  
    ```sql
    SELECT patients.patient_id, 
           CONCAT(patients.patient_id, LENGTH(patients.last_name), YEAR(birth_date)) AS temp_password 
    FROM patients 
    JOIN admissions ON patients.patient_id = admissions.patient_id 
    GROUP BY patients.patient_id;
    ```
