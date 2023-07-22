# nosql_challenge
### overview 

The UK Food Standards Agency evaluates various establishments across the United Kingdom, and gives them a food hygiene rating. I will be helping food magazine editors to evaluate some of the ratings data in order to help their journalists and food critics decide where to focus future articles. 

This challenge has been divided into 3 main parts. 
- Part 1: Database and Jupyter Notebook Set Upß
- Part 2: Update the Database
- Part 3: Exploratory Analysis

1. **Part 1: Database and Jupyter Notebook Set Up**
In this part, I imported the data provided in the `establishments.json` file from the Terminal. Named the database `uk_food` and the collection `establishments`. Then copied the text used to import establishments data from the Terminal to a markdown cell in notebook. Used `print(mongo.list_database_names())` to confirm if the database was created. To explore the establishemtns data I used `pprint(db.establishments.find_one())`.

2. **Part 2: Update the Database**
In this part of the analysis I have used different logic to clean the data in the database. I have used `establishments.insert_one(new_restaurant)` to insert a data, to change the data I have used `updated_business_type_id = establishments.replace_one`, deleted the unwanted data from the database using `establishments.delete_many(query)`, I used to change all the rows's object into float `establishments.update_many({}, [ {'$set':{ "RatingValue" : {'$toInt': "$RatingValue"}}} ])`, and `establishments.update_many({"RatingValue": {"$in": non_ratings}}, [ {'$set':{ "RatingValue" : None}} ])` to chagne in to string. Update_many function applies changes to all the info of that particular columns.

3. **Part 3: Exploratory Analysis**
In part 3, I started with the notebook setup by importing the dependancies like `from pymongo import MongoClient` and `from pprint import pprint`. Created an instance of MongoClient and assign the uk_food database to a variable name. 

**1. Which establishments have a hygiene score equal to 20?**

* Use count_documents to display the number of documents  contained in the result.
  `number_of_documents =establishments.count_documents(query)`
* Display the first document in the results using pprint.
  `documents_with_hygiene20 = db.establishments.find_one(query)`
* Convert the result to a Pandas DataFrame, print the number of rows in the DataFrame, and display the first 10 rows.
  `documents_with_hygiene20_df  = pd.DataFrame(results)`
   `documents_with_hygiene20_df.head(10)`

**2. Which establishments in London have a RatingValue greater than or equal to 4?**
I established the query to filter out the collection using regex `query2 = {'LocalAuthorityName': {'$regex': 'London'}, 'RatingValue': {'$gte': 4}}`. Used `count_document` method to count the number of result after the filter. Finally,  used `find_one` mention to print out the first result. 

**3. What are the top 5 establishments with a RatingValue rating value of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?**
Used the filter ` pprint(establishments.find_one({'BusinessName': 'Penang Flavours'}, {'geocode.latitude', 'geocode.longitude'})) ` to find out the latitude and longitude info for Penang Flavours. Created a filter and printed out the 5 results from the filter.
`query3 = {'RatingValue': '5', 
         'geocode.latitude': {'$lte': (latitude + degree_search), '$gte': (latitude - degree_search)}, 
         'geocode.longitude': {'$lte': (longitude + degree_search), '$gte': (longitude - degree_search)}}`
`sort =  [('scores.Hygiene', 1)]`
`limit = 5` 
and printed `pprint(list(establishments.find(query3).sort(sort).limit(limit)))`out the 5 results from the filter using pprint.

**4. How many establishments in each Local Authority area have a hygiene score of 0?**
I created the pipeline to perform the aggregation. Created 3 variables `match_query = {'$match': {'scores.Hygiene': 0}}`, `group_query = {'$group': {'_id': '$LocalAuthorityName', 'count': {'$sum': 1}}}` , `sort_values = {'$sort': {'count': -1}}`. Sent those variable inside the pipeline `pipeline =[match_query, group_query, sort_values]` and performed the aggregation `results = list(establishments.aggregate(pipeline))`

This challenge has different files, queries for the data analysis part, json file (**establishments.json**) used in this challenge and the readme file. They are saved as:

1. [README.md](https://github.com/shikhasitaula/nosql_challenge/blob/main/README.md): Provides orientation for this challenge.
2. [NoSQL_analysis_starter.ipynb](https://github.com/shikhasitaula/nosql_challenge/blob/main/NoSQL_analysis_starter.ipynb): It is the Jupyter notebook containing code for this analysis.
3. [NoSQL_setup_starter.ipynb](https://github.com/shikhasitaula/nosql_challenge/blob/main/NoSQL_setup_starter.ipynb): It is the Jupyter notebook containing code for setup the mongo database.
4. [Resources](https://github.com/shikhasitaula/nosql_challenge/tree/main/Resources): It is the json file used in this analysis.





