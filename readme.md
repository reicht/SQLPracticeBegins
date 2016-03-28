##How many users are there?

SELECT * FROM users;

50

##What are the 5 most expensive items?

SELECT * FROM items ORDER BY price LIMIT 5;

25|Small Cotton Gloves|Automotive, Shoes & Beauty|Multi-layered modular service-desk|9984
83|Small Wooden Computer|Health|Re-engineered fault-tolerant adapter|9859
100|Awesome Granite Pants|Toys & Books|Upgradable 24/7 access|9790
40|Sleek Wooden Hat|Music & Baby|Quality-focused heuristic info-mediaries|9390
60|Ergonomic Steel Car|Books & Outdoors|Enterprise-wide secondary firmware|9341


##What's the cheapest book? (Does that change for "category is exactly 'book'" versus "category contains 'book'"?)

sqlite> SELECT * FROM items WHERE category = "Books" ORDER BY price asc;
76|Ergonomic Granite Chair|Books|De-engineered bi-directional portal|1496 <---
98|Practical Plastic Hat|Books|Implemented non-volatile model|3056
21|Fantastic Rubber Shoes|Books|Reverse-engineered modular hierarchy|8904
4|Fantastic Steel Chair|Books|Advanced attitude-oriented encryption|9246


OR THE SECOND OPTION (No Difference in Target)

sqlite> SELECT * FROM items WHERE category LIKE "%Books%" ORDER BY price asc;
76|Ergonomic Granite Chair|Books|De-engineered bi-directional portal|1496 <---
95|Small Cotton Hat|Beauty & Books|Reduced regional instruction set|1727
97|Incredible Granite Computer|Books, Toys & Tools|De-engineered national policy|2377
98|Practical Plastic Hat|Books|Implemented non-volatile model|3056
77|Fantastic Plastic Gloves|Beauty, Movies & Books|Robust bandwidth-monitored local area network|6584
12|Fantastic Rubber Shirt|Toys & Books|Ergonomic impactful emulation|6720
21|Fantastic Rubber Shoes|Books|Reverse-engineered modular hierarchy|8904
4|Fantastic Steel Chair|Books|Advanced attitude-oriented encryption|9246
60|Ergonomic Steel Car|Books & Outdoors|Enterprise-wide secondary firmware|9341
100|Awesome Granite Pants|Toys & Books|Upgradable 24/7 access|9790



##Who lives at "6439 Zetta Hills, Willmouth, WY"? Do they have another address?

SELECT first_name,last_name FROM users JOIN addresses where users.id = addresses.user_id AND street LIKE "%hills%";
Corrine|Little

sqlite> SELECT * FROM addresses WHERE user_id IS 40;
43|40|6439 Zetta Hills|Willmouth|WY|15029
44|40|54369 Wolff Forges|Lake Bryon|CA|31587

Or to more efficiently find her addresses..

sqlite> SELECT street, city, state FROM addresses WHERE user_id IN (SELECT user_id FROM addresses WHERE street LIKE"%hills%");
6439 Zetta Hills|Willmouth|WY
54369 Wolff Forges|Lake Bryon|CA




##Correct Virginie Mitchell's address to "New York, NY, 10108".

sqlite> UPDATE addresses SET city = "New York", state = "NY", zip = "10108" WHERE user_id = (SELECT id FROM users WHERE first_name IS "Virginie" AND last_name IS "Mitchell");

RESULT =

sqlite> SELECT * from addresses where user_id = 39;
41|39|12263 Jake Crossing|New York|NY|10108
42|39|83221 Mafalda Canyon|New York|NY|10108

##How much would it cost to buy one of each tool?

sqlite> select SUM(price) FROM items WHERE category = "Tools";
SUM(
----
7383

##How many total items did we sell?

sqlite> select sum(quantity) from orders;
sum(
----
2125

##How much was spent on books?

sqlite> Select SUM(items.price * orders.quantity) FROM orders JOIN items ON orders.item_id = items.id WHERE category = "Books";
SUM(
----
420566

##Simulate buying an item by inserting a User for yourself and an Order for that User.

sqlite> INSERT INTO users (first_name, last_name, email) VALUES ("Jordan","Corley","rainwolfj@gmail.com")
   ...> ;
sqlite> SELECT * FROM users WHERE first_name = "Jordan"
   ...> ;
id    first_name     last  emai
----  -------------  ----  ----
51    Jordan         Corley  rainwolfj@gmail.com

sqlite> INSERT INTO orders (user_id, item_id, quantity, created_at) VALUES (51,12,4,CURRENT_TImESTAMP );
sqlite> SELECT * FROM orders WHERE user_id = 51;                                    id    user_id        item  quan  crea
----  -------------  ----  ----  ----
378   51             12    4
379   51             12    4     2016-03-28 19:04:10


##What item was ordered most often? Grossed the most money?

sqlite> SELECT title, SUM(quantity) FROM orders join items WHERE items.id = orders.item_id GROUP BY item_id ORDER by SUM(quantity) desc LIMIT 1;
titl  SUM(quantity)
----  -------------
Incredible Granite Car  72

sqlite> SELECT title, SUM(quantity), SUM(price), (SUM(quantity) * SUM(price)) FROM orders join items WHERE items.id = orders.item_id GROUP BY item_id ORDER by (SUM(quantity) * SUM(price)) desc;
titl  SUM(quantity)  SUM(  (SUM
----  -------------  ----  ----
Incredible Granite Car  72             65655  4727160

##What user spent the most?

sqlite> SELECT first_name, last_name, (SUM(orders.quantity) * AVG(items.price)) FROM users JOIN orders ON users.id = orders.user_id JOIN items on items.id = orders.item_id  GROUP BY user_id ORDER BY (SUM(orders.quantity) * AVG(items.price)) desc LIMIT 3;
firs  last_name      (SUM
----  -------------  ----
Hassan  Runte          636864.8


##What were the top 3 highest grossing categories?

sqlite> SELECT category, SUM(quantity), SUM(price), SUM(quantity * price) FROM orders join items WHERE items.id = orders.item_id GROUP BY category ORDER by SUM(quantity * price) desc LIMIT 3;

Music, Sports & Clothing|72|65655|525240
Beauty, Toys & Sports|54|58268|449496
Sports|102|73879|448410
