A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database. 

---

```
SELECT * FROM crime_scene_report 
WHERE type = 'murder' 
AND city = 'SQL City' 
AND date = '20180115';
```
Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".

---
```
SELECT * FROM person 
WHERE address_street_name = 'Northwestern Dr' 
ORDER BY address_number DESC 
LIMIT 1;
```
name     	address_street_name	max(address_number)	license_id	ssn          id
Morty Schapiro	Northwestern Dr	          4919	                  118009	111564949   14887

```

SELECT * FROM person 
WHERE name LIKE 'Annabel%' 
AND address_street_name = 'Franklin Ave';
```
Annabel Miller	Franklin Ave	           103	                  490173	318771143   16371

---
```
SELECT * FROM interview 
WHERE person_id = '14887';
```
Morty 14887: I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".
"Get Fit Now Gym" gold membership s"48Z" , car plate i"H42W"
id	person_id	name	membership_start_date	membership_status
48Z7A	28819	Joe Germuska	20160305	gold          license_id: 173289
48Z55	67318	Jeremy Bowers	20160101	gold	      license_id: 423327 murder
```

SELECT * FROM interview 
WHERE person_id = '16371';
```
Annabel 16371: I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. 
20180109
___________________________________________
```

SELECT * FROM interview 
WHERE person_id = '67318';
```
I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. 
```
SELECT * FROM get_fit_now_check_in 
WHERE check_in_date = '20180109' 
AND membership_id LIKE '48Z%';
```
```
SELECT * FROM drivers_license 
WHERE hair_color = 'red' 
AND gender = 'female' 
AND car_make = 'Tesla' 
AND car_model = 'Model S';
```
```

SELECT * FROM person
WHERE license_id in (202298,291182,918773);
	```
id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
202298	68	66	green	red	female	500123	Tesla	Model S
291182	65	66	blue	red	female	08CM64	Tesla	Model S
918773	48	65	black	red	female	917UU3	Tesla	Model S
```

SELECT * FROM person 
WHERE id IN (28819, 67318);
```
```

SELECT * FROM drivers_license 
WHERE id IN (423327, 173289);
```

id	name	ssn
99716	Miranda Priestly	987756388  SUS
90700	Regina George	337169072
78881	Red Korb	961388910


event_id	event_name	date
1143	SQL Symphony Concert	20171206
1143	SQL Symphony Concert	20171212
1143	SQL Symphony Concert	20171229