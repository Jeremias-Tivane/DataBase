
# NoSQL Project - Data Engineering

As a data engineer at a Data Analytics Consulting Company. A company prides itself in being able to efficiently handle data in any format on any database on any platform. Analysts in the offices need to work with data on different databases, and with data in different formats. While they are good at analyzing data, they count on me to be able to move data from external sources into various databases, move data from one type of database to another, and be able to run basic queries on various databases.



## In this mini project i used 3 non-relational databases:

 - [MongoDB Docs](https://docs.mongodb.com/)
 - [Cassandra Docs](https://cassandra.apache.org/_/index.html)
 - [IBM Cloudant - Overview](https://www.ibm.com/cloud/cloudant)


## Initial Task - Geting prepare the environment ready to use

#### I. We need the 'couchimport' and 'couchexport' tools to move data in and out of the Cloudant database.

I.I: To install these tools i run the below commands on the terminal.

```javascript
sudo npm install -g couchimport
```

I.II: Verify that the tool got installed, by running the below command on the terminal.

```javascript
couchimport --version
```

#### II: We need the 'mongoimport' and 'mongoexport' tools to move data in and out of the mongodb database.

II.I: To install these tools i run the below commands on the terminal.

```javascript
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-ubuntu1804-x86_64-100.3.1.tgz
tar -xf mongodb-database-tools-ubuntu1804-x86_64-100.3.1.tgz
export PATH=$PATH:/home/project/mongodb-database-tools-ubuntu1804-x86_64-100.3.1/bin
echo "done"
```
II.II: Verify that the tools got installed, by running the below command on the terminal.

```javascript
mongoimport --version
```

## Main Project Tasks

### Task 1: I replicated a remote database into Cloudant instance.

![App Screenshot](https://github.com/Jeremias-Tivane/DataBase/blob/main/NoSQL%20Project/1-replication.png)

### Task 2: I created an index for key "director", on the database movies using the HTTP API.

```javascript
curl -X POST $CLOUDANTURL/movies/_index \
-H"Content-Type: application/json" \
-d'{
    "index": {
        "fields": ["director"]
    }
}'
```

### Task 3: I writed a query to find all movies directed by Richard Gage using the HTTP API. 

```javascript
curl -X POST $CLOUDANTURL/movies/_find \
-H"Content-Type: application/json" \
-d'{ "selector":
        {
            "director":"Richard Gage"

        }
    }'
	
```

### Task 4: I created an index for key "title", on the database movies using the HTTP API.

```javascript
curl -X POST $CLOUDANTURL/movies/_index \
-H"Content-Type: application/json" \
-d'{
    "index": {
        "fields": ["title"]
    }
}'
```

### Task 5: I writed a query to list only the keys year and director for the movie `Top Dog` using the HTTP API.

```javascript
curl -X POST $CLOUDANTURL/movies/_find \
-H"Content-Type: application/json" \
-d'{
   "selector": {
      "title": "Top Dog"
   },
   "fields": [
      "year",
      "director"
   ]
   }'
```

### Task 6: I exported the data from movies database into a file named movies.json using my Cloudant instance.

```javascript
couchexport --url ($MyCLOUDANTURL) --db movies --type jsonl > movies.json
```

### Task 7: I imported movies.json into mongodb server into a database named entertainment and collection named movies using my mongo bd credentials. 

```javascript
mongoimport -u "MY USER" -p "MY PASS" --authenticationDatabase admin --db entertainment --collection movies --file movies.json
```

### Task 8: I writed a mongodb query to find the year in which most number of movies were released.

```javascript
db.movies.aggregate([
{
    "$group":{
        "_id":"$director",
        "moviecount":{"$sum":1}
        }
}
])
```

### Task 9: I writed a mongodb query to find the count of movies released after the year 1999. 

```javascript
db.movies.aggregate([
	{
		"$match":{"year":{"$gt":1999}}
	},
	{
		"$group":{"_id":"$year", "count":{"$sum":1} }
	},
	{	
		"$sort":{"count":"-1"}
	},
	{"$limit":1}}	
])
{"_id": 2013, "count" : 593}
```

### Task 10. I writed a query to find out the average votes for movies released in 2007. 

```javascript
db.movies.aggregate([
{
	$match: { year: 2007} 
 },
{
    "$group":{
        "_id":"$year",
        "average":{$avg: "$votes"}
        }
}
])
{"_id" :2007, "average" : null}

```

### Task 11 - I exported the fields _id, title, year, rating and director from movies collection into a file named partial_data.csv using my mongo bd credentials.

```javascript
mongoexport -u "MY USER" -p "MY PASS" --authenticationDatabase admin --db entertainment --collection movies --out partial_data.csv --type=csv --fields _id,title,year,rating,director
```

### Task 12 - I imported partial_data.csv into cassandra server into a keyspace named entertainment and table named movies. 

```javascript
CREATE KEYSPACE entertainment  
WITH replication = {'class':'SimpleStrategy', 'replication_factor' : 3};
```
```javascript
use entertainment;
CREATE TABLE movies(
id text PRIMARY KEY,
title text,
year text,
rating text,
genre text,
director text,
);
```
```javascript
use entertainment;
COPY entertainment.movies(id,title,year,rating,director) FROM 'partial_data.csv' WITH DELIMITER=',' AND HEADER=TRUE;
```

### Task 13 - I writed a cql query to count the number of rows in the movies table. 

```javascript
select Count(*) from movies;
```

### Task 14 - I created an index for the column rating in the movies table using cql.

```javascript
CREATE INDEX on movies(rating);
```

### Task 15 - I writed a cql query to count the number of in the movies that are rated 'G'. 

```javascript
SELECT count(*) FROM movies WHERE rating = 'G';
```

## Copyight (C)

**Copyright:**  IBM Skills Network | All rights reserved. 2021

**Author:** [Jeremias Tivane](https://www.linkedin.com/in/jeremiastivane/)
