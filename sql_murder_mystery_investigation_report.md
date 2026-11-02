# 🕵️ SQL Murder Mystery — From the Crime Scene to the Mastermind

> A SQL investigation into a murder in SQL City, exploring relational databases, witness statements, and hidden connections to uncover both the murderer and the mastermind behind the crime.

## 1. Project Overview

This project documents my investigation and solution to the **SQL Murder Mystery**, an interactive SQL challenge created by Knight Lab at Northwestern University.

The story begins with a murder in SQL City on **January 15, 2018**. Using the available police database, my objective was to investigate the crime, identify the witnesses, analyse their statements, find the murderer, and eventually uncover the person who orchestrated the entire operation.

Throughout this investigation, I explored different relational database tables, wrote SQL queries, and connected information from multiple sources to progressively narrow down the suspects.

* **Challenge Link:** <https://mystery.knightlab.com/>

## 2. Retrieving the Original Crime Scene Report

Every investigation needs a starting point. I began by querying the `crime_scene_report` table to retrieve murder reports associated with SQL City.

```
select *
from crime_scene_report 
where type = 'murder' 
and city = 'SQL City';

```

After examining the results, I focused on the murder that took place on **January 15, 2018**.

**Key Clues from the Report:**

* The murder took place in **SQL City**.

* Two witnesses were mentioned in the report.

* Their descriptions and locations could be used to identify them in the `person` table.

## 3. Identifying the Witnesses

The crime scene report described two witnesses:

1. The first witness lived in the **last house on Northwestern Drive**.

2. The second witness was named **Annabel** and lived somewhere on **Franklin Avenue**.

### 3.1. Finding Witness #1: Morty Schapiro

The first witness lived at the last house on Northwestern Drive.

```
select *
from person
where address_street_name = 'Northwestern Dr'
order by address_number desc
limit 1;

```

> **Identified Witness #1:** Morty Schapiro

### 3.2. Finding Witness #2: Annabel Miller

The second witness was named Annabel and lived on Franklin Avenue.

```
select *
from person
where address_street_name = 'Franklin Ave'
and name like '%Annabel%';

```

> **Identified Witness #2:** Annabel Miller

## 4. Analysing the Witness Statements

After identifying both witnesses, I retrieved their interview transcripts from the `interview` table.

```
select *
from interview
where person_id in (
    select id
    from person
    where name in ('Morty Schapiro', 'Annabel Miller')
);

```

### 4.1. Morty Schapiro's Testimony

Morty reported hearing a gunshot and seeing a man running away from the crime scene. He remembered several details:

* The man was carrying a **Get Fit Now Gym** bag.

* His gym membership number started with **48Z**.

* He had a **Gold** membership.

* His vehicle had a licence plate containing **H42W**.

### 4.2. Annabel Miller's Testimony

Annabel claimed to have witnessed the murder. She recognised the murderer from her gym and remembered seeing him working out on **January 9, 2018**.

## 5. Part One — Identifying the Murderer

To find the murderer, I needed to combine the witnesses' information with the available database records across four key tables:

| Table Name | Description / Information | 
| ----- | ----- | 
| `get_fit_now_member` | Gym membership information | 
| `get_fit_now_check_in` | Gym attendance records | 
| `person` | Personal information and identities | 
| `drivers_license` | Driving licence and vehicle information | 

### 5.1. Investigation Query

Using the clues collected from both witnesses, I cross-referenced gym memberships, check-in history, personal records, and vehicle registrations in a single query:

```
select *
from get_fit_now_member 
join get_fit_now_check_in
on get_fit_now_member.id = get_fit_now_check_in.membership_id
join person 
on person.id = get_fit_now_member.person_id
join drivers_license
on person.license_id = drivers_license.id
where get_fit_now_member.id like '48Z%'
and get_fit_now_member.membership_status = 'gold'
and check_in_date like '%0109'
and plate_number like '%H42W%';

```

### 5.2. Evidence Mapping

| Database Field | Witness Evidence / Condition | 
| ----- | ----- | 
| **Gym Membership ID** | Starts with `48Z` | 
| **Membership Status** | `gold` | 
| **Gym Check-in Date** | January 9, 2018 (`%0109`) | 
| **Vehicle Plate Number** | Contains `H42W` | 

> 🎯 **Murderer Identified:** **Jeremy Bowers**

## 6. The Unexpected Twist — Who Hired Jeremy Bowers?

After identifying Jeremy Bowers, I queried his interview transcript to confirm the case.

```
select *
from interview
where person_id = 67318;

```

His statement revealed that he was a hitman hired by someone else:

* **Gender:** Female

* **Height:** Between 65" and 67"

* **Hair Color:** Red

* **Vehicle:** Tesla Model S

* **Event History:** Attended the **SQL Symphony Concert** in December 2017 (`201712%`)

## 7. Part Two — Finding the Mastermind

To uncover the mastermind, I connected the physical description, vehicle details, and event check-ins by joining the `person`, `drivers_license`, and `facebook_event_checkin` tables directly.

### 7.1. Mastermind Identification Query

```
select * 
from person 
join drivers_license
on person.license_id = drivers_license.id
join facebook_event_checkin
on person.id = facebook_event_checkin.person_id
where hair_color = 'red' 
and car_make = 'Tesla' 
and car_model = 'Model S'
and facebook_event_checkin.date like '201712%';

```

This single combined query filtered suspects directly based on vehicle make/model, hair color, and December 2017 concert attendance.

## 8. Final Revelation

> 🧠 **Mastermind Identified:** **Miranda Priestly**

### Case Summary

| Role | Identity | Clues Matched | 
| ----- | ----- | ----- | 
| **The Murderer** | Jeremy Bowers | Gym ID `48Z*`, Gold member, plate `%H42W%`, check-in `%0109` | 
| **The Mastermind** | Miranda Priestly | Red hair, Tesla Model S, SQL Symphony Concert visits in December 2017 (`201712%`) | 

## 9. SQL Skills & Concepts Practised

| SQL Concept | Application in Investigation | 
| ----- | ----- | 
| `SELECT` | Retrieving database records | 
| `WHERE` | Filtering relevant information and specific criteria | 
| `LIKE` | Searching for partial matches (plate numbers, gym IDs, dates) | 
| `ORDER BY` / `LIMIT` | Finding specific edge cases (e.g., highest address number) | 
| `JOIN` | Multi-table joins connecting relational database entities | 
| **Subqueries** | Nesting personal IDs within interview lookups | 

## 10. Credits & References

* **Original Challenge Authors:** Joon Park and Cathy He (Knight Lab fellows at Northwestern University)

* **Web Version Producer:** Joe Germuska

* **Interactive Challenge:** <https://mystery.knightlab.com/>

* **GitHub Repository:** [NUKnightLab/sql-mysteries](https://github.com/NUKnightLab/sql-mysteries)

## 11. Conclusion

What initially appeared to be a straightforward murder investigation turned into a multi-layered criminal plot. Starting with a single crime scene report, SQL allowed me to follow witness leads, reconstruct timelines, identify the hitman, and ultimately unmask the mastermind behind the entire scheme.

*One murder. Two identities. One investigation.* **Case Closed.**