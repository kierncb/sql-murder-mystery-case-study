## **EXECUTIVE SUMMARY**
This project is from [SQL Murder Mystery](https://mystery.knightlab.com/). It enhances my problem-solving and critical thinking using SQL in exploratory data analysis, especially in constructing efficient query in finding out that the killer is ***Jeremy Bowers*** and the mastermind is ***Miranda Priestly***.


## **SITUATION**

A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database.

## **SCHEMA**

![alt text](images/schema.png)

## **APPROACH**
1. **Used the initial given information**

```sql
SELECT *
FROM crime_scene_report
WHERE
    city = "SQL City"
    AND type = "murder"
    AND date = 20180115
```

2. **Looked for the witness named "Annabel"**

```sql
SELECT *
FROM person
WHERE
    address_street_name = "Franklin Ave"
    AND name LIKE = "%Annabel%"
```

3. **Looked for Annabels's interview transcript**

```sql
SELECT *
FROM person
JOIN interview
    ON interview.person_id = person.id
WHERE
    address_street_name = "Franklin Ave"
    AND name LIKE "%Annabel%"
```

4. **Since she recognized the killer from her gym, looked at her gym details**

```sql
SELECT
    person.name,
    person.id,
    membership_status,
    membership_start_date
FROM person
JOIN interview
    ON interview.person_id = person.id
JOIN get_fit_now_member
    ON get_fit_now_member.person_id = person.id
WHERE
    person.address_street_name = "Franklin Ave"
    AND perso.name LIKE "%Annabel%"
```
5. **Since she saw the killer when she was working out on January 19th**

```sql
SELECT *
FROM get_fit_now_check_in
JOIN get_fit_now_member
ON get_fit_now_member.id = get_fit_now_check_in.membership_id
WHERE
membership_status = "gold"
AND check_in_date IN (20170109, 20180109)
ORDER BY get_fit_now_check_in.check_in_date
```

6. **Looked for the interview transcript of the 2nd witness**

```sql
SELECT *
FROM person
JOIN interview
ON interview.person_id = person.id
WHERE address_street_name = "Northwestern Dr"
ORDER BY address_number DESC
```

7. **Checked the given plate number of the suspect**

```sql
SELECT
person.id,
person.name,
drivers_license.plate_number
FROM person
JOIN drivers_license
ON person.license_id = drivers_license.id
WHERE person.id IN (28819, 67318)
```


8. **Checked also the membership id of the possible suspect**

```sql
SELECT *
FROM get_fit_now_check_in
JOIN get_fit_now_member
ON get_fit_now_member.id = get_fit_now_check_in.membership_id
WHERE membership_id LIKE "%48Z%"
```

9. **Verify if we already figured out who the suspect was**

```sql
INSERT INTO solution VALUES (1, "Jeremy Bowers");
SELECT values FROM solution;
```

10. **Now, check for the mastermind according to ther suspect**

```sql
SELECT *
FROM interview
WHERE person_id = 67318
```

11. **Challenged myself to use only one query in finding the mastermind based on suspect's transcript**

```sql
WITH mastermind AS (
    SELECT
    person.id AS person_id,
    person.name AS person_name,
    person.license_id AS person_license_id,
    CAST(facebook_event_checkin.date AS TEXT) AS event_date
FROM person
JOIN facebook_event_chechkin
ON peron.id = facebook_event_checkin.person_id
WHERE
facebook_event_checkin.event_name = "SQL Symphony Concert"
AS date LIKE "201712%"
)

SELECT
person_id,
person_name,
drivers_license.height,
drivers_license.hair_color,
drivers_license.car_make,
drivers_license.car_model
FROM mastermind
JOIN drivers_license
ON drivers_license.id = mastermind.person_license_id
GROUP BY
person_id,
person_name
HAVING 
COUNT(person_id) = 3
```

12. **Verified if I found the mastermind**

```sql
INSERT INTO solution VALUES (1, "Miranda Priestly")
SELECT value FROM solution;
```
