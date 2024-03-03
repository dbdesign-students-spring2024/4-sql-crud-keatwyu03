Note: An extension was used for this assignment, that is why I submitted late. Thank you!
# SQL CRUD

## Part 1: Restaurant Finder

### 5 Question Determines:
The structure of the resturants table is going to be a 1000 row table with 5 headers. The table will follow a 5 heading fixed scheme in the order of id (the primary key), category, price tier, neighborhood, opening hours, average rating, and good for kids. 

The data type of the 5 fields are as follows. The id field is a primary key that is an integer. The category field is a TEXT data type. The price tier field is a TEXT data type. The Neighborhood field is a TEXT data field. The Opening times field is a TEXT data type. The average rating field is an integer data type. The good for kids field is a boolean data type. 

The primary key for the resturant table is the id field. 

Since there is only one table, there is no linking and hence no foriegn key. 

[Data for Restaurants](data/Restaurants.csv)

### Queries:

The following code block shows how I was able to create a table and import the data into the fixed schema table.
```sql

.mode csv
.headers on

CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    Restaurant_name TEXT,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_time INTEGER,
    closing_time INTEGER,
    average_rating INTEGER,
    good_for_kids BOOLEAN
);

CREATE TABLE reviews (
    review_id INTEGER PRIMARY KEY,
    restaurantid INTEGER,
    review TEXT,
    FOREIGN KEY (restaurantid) REFERENCES restaurants(id)
);

.import ./OneDrive/School/Junior Year/Spring 2024/Databse Design/4-sql-crud-keatwyu03/data/Restaurants.csv restaurants

```

Query 1: Finding all the cheap restaurants in a particular neighborhood (Chelsea)
```sql
select Restaurant_name from restaurants where price_tier = "Cheap" and neighborhood = "Chelsea";
```

Query 2: Finding all the restaurants in a particular genre (Chinese food) with 3 stars or more, ordered byt the number of stars in descending order.

```sql
select Restaurant_name from restaurants where category = "Chinese" and average_rating >= 3 order by average_rating desc;
```

Query 3: Finding all restaurants that are open now
(We assumed that all the restaurants have the same opening times, from 9am to 8pm)
```sql
select Restaurant_name from restaurants where (select strftime('%H', 'now', 'localtime')) < closing_time and (select strftime('%H', 'now', 'localtime')) > starting_time;
```

Query 4: Leave a review for a restaurant (lhammarberg0, the first row restaurant in the restaurants table) 

```sql
insert into reviews (review_id, restaurantid, review) values (1, 1, "very nice would come back");
```

Query 5: Deleting all restaurants that are bad for kids
```sql
delete from restaurants where good_for_kids = "False";
```

Query 6: Find the number of restaurants in each NYC neighborhood
```sql 
select neighborhood, count(Restaurant_name) from restaurants group by neighborhood;
```


## Part 2: Social Media App

### 5 Question Determines:
The structure of the users table will be consisted of 4 fields - a primary key id, email, password, and username = handle. 

The data field for users tables will be integer for id, text for email, text for password, and text for handle. 

The primary key for the users table will be the id value. 

There is no use of the foriegn key in either of the table.

The structure for the post table is made up of post_id, which is the primary key, post type, mesage receiver id, messaged viewed, stories create time, creator id, content, is visible, and is deleted. 

The data type of post_id (which is the primary key) is an integer. The data type of the post_type is TEXT (and will either contain story or message). The data type of message reciever id is an integer. The data type for message viewed will be text, I decided to use string comparison instead of a boolean comparison. The data type for story create time will be the current datetime. The data type for the creater id is an integer. The data type for the content is text, the data type for is_visible is text, and the data type for is deletd is also text. The latter 2 are texts because I decide to use string comparisons in my queries instead of boolean comparisons. 

The primary key for the posts table is post_id. 

The following code block shows the table creation for users and posts. Then there are two lines of code that show the importing line in SQL.

[Data for users](data/users.csv)

[Data for posts](data/posts.csv)

```sql 
.headers on
.mode csv

CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    email TEXT,
    password TEXT,
    handle TEXT
);

CREATE TABLE posts (
    post_id INTEGER PRIMARY KEY,
    post_type TEXT,
    message_reciever_id INTEGER,
    message_viewed TEXT,
    stories_create_time DATETIME default CURRENT_TIMESTAMP,
    creator_id INTEGER,
    content TEXT,
    is_visible TEXT,
    is_deleted TEXT
);

.import ./OneDrive/School/Junior Year/Spring 2024/Databse Design/4-sql-crud-keatwyu03/data/users.csv users

.import ./OneDrive/School/Junior Year/Spring 2024/Databse Design/4-sql-crud-keatwyu03/data/posts.csv posts

```


Query 1: Registering for a new user. 

```sql
insert into users (email, password, handle) values ("tempemail@nyu.edu" , "temppassword", "tempunit");
```


Query 2: Creating a new message sent by user with id = 1 to id = 2.

```sql
insert into posts (post_type, message_reciever_id, message_viewed, creator_id, content, is_visible, is_deleted) values ("Message", 1, "False" ,2, "hello test", "True", "False");

```

Query 3: Creating a new story by a user id = 1.

```sql
insert into posts (post_type, creator_id, content, is_visible, is_deleted) values  ("Story", 1, "Story Test", "True", "False");

```

Query 4: Showing the 10 most recent visible messages and stories, in order of recency. 
```sql 
select content from posts where is_visible = "True" order by stories_create_time desc limit 10;
```

Query 5: Showing the 10 most recent visible messages sent by user = 11 to user = 30 in order of recency.
```sql 
select content from posts where message_reciever_id = 11 and creator_id = 30 and is_visible = "True" order by stories_create_time desc limit 10;
```

Query 6: Making all the stories that are more than 24 hours old invisible. 
```sql
update posts set is_visible = "False" where ROUND((JULIANDAY('now','localtime') - JULIANDAY(stories_create_time)) * 24) > 24;

```

Query 7: Show all the invisible messages and stories in order of recency.

```sql 
select content from posts where is_visible = "False" order by stories_create_time desc;
```

Query 8: Showing the number of posts by each user.
```sql 
select creator_id, count(content) from posts group by creator_id;

```

Query 9: SHowing the posts text (content) and email address of all posts and the user who made them within the last 24 hours. 
```sql

select posts.content, users.email from posts inner join users on posts.creator_id = users.id where ROUND((JULIANDAY('now','localtime') - JULIANDAY(stories_create_time)) * 24) < 24;

```

Query 10: Showing the email address of all users who have not posted anything. 
```sql
select users.email from users left join posts on posts.creator_id = users.id where posts.creator_id is NULL;
```