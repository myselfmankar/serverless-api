﻿# Serverless-api-Movies

# **Overview**
This project focuses on developing a serverless movie API using Python within the context of the [6 Month Cloud Study Plan](https://learntocloud.guide/). The goal is to gain practical experience in
building cloud-native applications leveraging serverless architecture and AWS services. The project aims to simplify movie data access using serverless functions, with a setup including a NoSQL 
database, cloud storage, and serverless functions.

# **Introduction**
With this project, you can efficiently retrieve movie details using serverless functions.

## **Technologies Used**
- AWS Lambda
- API Gateway
- Amazon DyanomoDB
- Python 3

## **Functions**
### fetch_and_format_Movies
The function fetch_and_format_movies() gets movie data from DynamoDB and organizes it neatly. It starts by creating an empty list. Then, it fetches movie data from DynamoDB through an API link. If
the data is successfully fetched, it processes it into a list of dictionaries, with each dictionary representing a movie. Finally, it sorts the movies by their IDs and returns the list. This 
function makes it easy to handle movie data from DynamoDB.

```python
formatted_movies = [] 
    response = requests.get('YOUR_GATEWAY_API_LINK')
    
    # Check if the request was successful
    if response.status_code == 200:
        dynamodb_movies = response.json()

        # Construct formatted_movies list based on DynamoDB data
        for dynamodb_movie in dynamodb_movies:
            movie = {
                'MovieID': int(dynamodb_movie['MovieID']['S']),
                'title': dynamodb_movie['title']['S'],
                'releaseYear': int(dynamodb_movie['releaseYear']['S']),
                'genre': dynamodb_movie['genre']['S'],
                'coverimage' : dynamodb_movie['coverimage']['S']
            }
            formatted_movies.append(movie)
        # Sort the movies by MovieID
        formatted_movies = sorted(formatted_movies, key=lambda x: x['MovieID'])
        
    return formatted_movies
```

### **getmovies**
This snippet establishes a route '/getmovies' for a Flask web app. When accessed via a GET request, it calls get_movies_json(). This function fetches and formats movie data, then returns it 
as JSON. In short, '/getmovies' provides movie data in JSON format.
```python
def get_movies_json():
    formatted_movies = fetch_and_format_movies()
    return jsonify(formatted_movies) 
```

### **getmoviesbyyear**
This code, get_movies_by_year(), filters movie data based on the user's provided year. It fetches and formats the movie data, then extracts the user-provided year from the request parameters. If no year is provided or the year is not between 2000 and 2010, it returns an error message with a 404 status code. Otherwise, it filters the movies by the provided year and returns them in JSON format. In short, it helps users find movies released in a specific year between 2000 and 2010.
```python
def get_movies_by_year():
    formatted_movies = fetch_and_format_movies()
    user_provided_year = request.args.get('year', type=int)

    # Return a response based on the filtered movies
    if user_provided_year is None:
        return "You need to choose a year!", 404
    elif user_provided_year < 2000 or user_provided_year > 2010:
        return "Please Choose a movie year between 2000 and 2010!", 404
    filtered_movies = [movie for movie in formatted_movies if movie["releaseYear"] == user_provided_year]
    return jsonify (filtered_movies)

```

## Considerations
- make sure you have installed all the necessary packages to enable the code to run, this includes: Flask, Boto3, and Requests
- ensure the permissions on your application are adjusted appropriately to allow access    
- Verify the accuracy of your AWS account configurations.

