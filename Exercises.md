# Getting started
This page is used to build up with different kind of exercises in order to become familiar with Wiremock.  
It will only give you a basis to explore more things more easily on your own tempo.

## Example(s)
In *stubs/mappings/xml* your can find a few examples of which kind of requests will stubbed, along with the reponse that will be returned.  
In that response, there is often a property *bodyFileName* with a filename as a value. This file can be found under *stubs/__files*

## Validate your stubs with Postman
Open *PostmanCollection/Wiremock.postman_collection.json* in Postman. 
The collection contains
- 2 requests to test your wiremock setup: getUsersXml and Display mappings. Run these requests and see if the body contains data.
- A folder for each exercise. Each folder contains 1 or more requests with tests. After you make an exercise, run the requests and validate that all tests pass

A collection variable is used for to indicate on which port Wiremock is listening. Default value of the port is 9002.
If you started Wiremock with a different port, please change the collection variable in Postman.

## Documentation

Here you find the documentation for all [request matching](https://wiremock.org/docs/request-matching/).
Documentation for [response matching](https://wiremock.org/docs/response-templating/) will also be required for later exercises.

# Exercises
## 1. Simple response of all users
Request:
- GET request /users

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- body to return in file users/users.json

## 2. Simple response of user1
Request:
- GET request /users/1

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- body to return in file users/user1.json

## 3. Simple response of user2 for any other user id being sent
Request:
- GET request /users/2
- GET request /users/3-100

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- body to return in file users/user2.json

Tip: keep in mind that user 1 is in our example a special user which we want to return when we request it. For all other values, we return user 2. Check 'Priority'.

## 4. We request user 99 which is not found and will return Not found error.
Request:
- GET request /users/99

Response:
- HTTP 404
- Header: "Content-Type": "application/json",
- body to return in file users/user99_NotFound.json

Tip: keep in mind that user 1 is in our example a special user which we want to return when we request it.  
For all other values, we return user 2.  
For 99 it's the not Found
Check 'Priority'.

## 5. We request user 'one' which will trigger the Bad Request error
Request:
- GET request /users/one

Response:
- HTTP 500
- Header: "Content-Type": "application/json",
- body to return:
  - error: 666666
  - description: Bad request! This is fur sure the devil!

Tip: keep in mind that user 1 is in our example a special user which we want to return when we request it.  
For all other values, we return user 2.  
For 99 it's the not Found
Check 'Priority'.

## 6. Response of user2 with fixed id: Extend it to return the id from the path
Request:
- GET request /users/3-100

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- id in the body needs to be the id of the url

Tip 1: Fetch the value from the url (which is a Path Parameter) and use this in your response. Have a look at the response-matching documentation   
Tip 2: Start wiremock with --global-response-templating

## 6B. Bonus exercise: instead of Path Paramerer, do the same exercise as query parameter
Request:
- GET request /users?user=3-1000

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- id in the body needs to be the id of the query parameter

Create a new stub-file, so both the path and query parameters work

Tip 1: Fetch the value from the url (which is a Query Parameter now!) and use this in your response. Have a look at the response-matching documentation   
Tip 2: Start wiremock with --global-response-templating

## 7. Create a mock for POST user
Request:
- POST request /users
- body:
<details>
    <summary>json request body</summary>
    {
        "id": 1,
        "name": "Leanne Graham",
        "username": "Bret",
        "email": "Sincere@april.biz",
        "address": {
            "street": "Kulas Light",
            "suite": "Apt. 556",
            "city": "Gwenborough",
            "zipcode": "92998-3874",
            "geo": {
                "lat": "-37.3159",
                "lng": "81.1496"
            }
        },
        "phone": "123456789",
        "website": "hildegard.org",
        "company": {
            "name": "Romaguera-Crona",
            "catchPhrase": "Multi-layered client-server neural-net",
            "bs": "harness real-time e-markets"
        }
    }
</details>  


Response:
- HTTP 201
- Header: "Content-Type": "application/json",
- body to return in file postUser/postUser_OK.json (returning fixed id)

## 8. Create a mock for POST user with returning input values
Request:
- POST request /users
- body: same as above

Response:
- HTTP 200
- Header: "Content-Type": "application/json",
- extend the body to return id from the request body
- extend the body to return name from the request body

Tip 1: Make sure to match on something in the body  
Tip 2: Fetch the value from the body now and use this in your response. Have a look at the response-matching documentation   
Tip 3: Start wiremock with --global-response-templating

## 9. Create a mock for POST user with error for username that exists
Request:
- POST request /users
- body: same as above
- If username equals "GroteSmurf" then send the error in file postUser_Error_UserNameExists.json

Response:
- HTTP 400
- Header: "Content-Type": "application/json",
- body to return:
  - error: 123
  - description: Username already exists

Make sure previous exercises still work.

## 10. Create a mock for POST user with error for invalid phone format
Request:
- POST request /users
- body: same as above
- The phone number can only contain digits. If any other value is in it, return an error

Response:
- HTTP 400
- Header: "Content-Type": "application/json",
- body to return:
  - error: 456
  - description: Invalid format of the phoneNr

Make sure previous exercises still work.