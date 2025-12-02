A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database. 

Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".

name     	address_street_name	max(address_number)	license_id	ssn          id
Morty Schapiro	Northwestern Dr	          4919	                  118009	111564949   14887

Annabel Miller	Franklin Ave	           103	                  490173	318771143   16371


Morty 14887: I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".
"Get Fit Now Gym" gold membership s"48Z" , car plate i"H42W"
id	person_id	name	membership_start_date	membership_status
48Z7A	28819	Joe Germuska	20160305	gold          license_id: 173289
48Z55	67318	Jeremy Bowers	20160101	gold	      license_id: 423327 murder

Annabel 16371: I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. 
20180109
___________________________________________
I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. 
id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
202298	68	66	green	red	female	500123	Tesla	Model S
291182	65	66	blue	red	female	08CM64	Tesla	Model S
918773	48	65	black	red	female	917UU3	Tesla	Model S

id	name	ssn
99716	Miranda Priestly	987756388  SUS
90700	Regina George	337169072
78881	Red Korb	961388910


event_id	event_name	date
1143	SQL Symphony Concert	20171206
1143	SQL Symphony Concert	20171212
1143	SQL Symphony Concert	20171229