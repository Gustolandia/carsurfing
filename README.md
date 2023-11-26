# Team B Project

## Carsurfing App

- Maven project with three modules, all being Spring boot apps.
- Functionality can be tested using docker-compose.
- To make changes, checkout local branch.
- Push to master only after making sure the functionality is working after testing manually with the services deployed in Docker containers.

## Testing with Docker

To test with Docker, run the following commands:

- Clone the project from [original GitHub](https://github.com/catalinpatularu78/carsurfing.git) (private repo) or from [here](https://github.com/Gustolandia/carsurfing.git).
- `cd carsurfing`
- Checkout your local working branch
- Run `mvn clean install` (this will build and package jars for three services: `userservice`, `reviewservice` and `rideservice`)
- Build images for each service:
  - `docker build -t carsurfing-mysql .`
    (to build mysql custom image which includes SQL statements to create the tables and populate the users table. See details in `/sql/tables.sql`)
  - `docker build -f ride-service/Dockerfile -t rideservice .`
    (to build the rideservice image)
  - `docker build -f user-service/Dockerfile -t userservice .`
    (to build the userservice image)
  - `docker build -f review-service/Dockerfile -t reviewservice .`
    (to build the userservice image)
  - `cd web-app && docker build -f Dockerfile -t web-app .`
    (to build the web-app image)
- Run `docker-compose up` (to start the containers). Wait around 1 min until the containers are fully started and healthy.
- To test the back-end functionality, open an HTTP client (e.g., Postman), or for the integrated front-end with back-end, go to [http://localhost:3000/join](http://localhost:3000/join) in a browser.

## UserService Use Cases

**1. Add User**
- To add a user, send a POST request to the URL `http://localhost:9092/api/auth/signup` with the following body:
  ```json
  {
    "name": "Alan",
    "description": "",
    "email": "alanstorm@gmail.com",
    "phone": "0875675845",
    "password": ""
  }

**2. Sign In**
- To sign in, send a POST request to `http://localhost:9092/api/auth/signin` with the following body:
  ```json
  {
    "email": "alanstorm@gmail.com",
    "password": ""
  }
**For All Other Actions**
- User must send an authentication bearer with JWT.

**3. Display All Users**
- To display all the users, send a GET request to `http://localhost:9092/api/user/users`.

**4. Delete a User**
- To delete a user, send a DELETE request to `http://localhost:9092/api/user/{id}`.

**5. Edit a User**
- To edit a user, send a PUT request to `http://localhost:9092/api/user/{id}`.

## RideService Use Cases

**1. Add a Ride**
- To add a ride, send a POST request to `http://localhost:9091/rideapi/rides` with the following body:
  ```json
  {
    "driverId": 1,
    "fromLocation": "Ashbourne",
    "toLocation": "Cork",
    "dateOfDeparture": "2024-11-11",
    "estimatedDepartureTime": "10:00:00",
    "spacesLeft": 1,
    "stop1": "Bray",
    "stop2": "",
    "stop3": ""
  }
**2. See All Rides**
- To see all rides, send a GET request to `http://localhost:9091/rideapi/rides`.

**3. Delete a Ride**
- To delete a ride, send a DELETE request to `http://localhost:9091/rideapi/rides/<rideId>`. Replace `<rideId>` with the actual ID of the ride you want to delete.

**4. Search for a Ride by Location and Date**
- To search for a ride by from/to location and date, send a POST request to `http://localhost:9091/rideapi/ridesForRouteAndDate` with the following body:
  ```json
  {
    "fromLocation": "Cork",
    "toLocation": "Dublin",
    "dateOfDeparture": "2022-05-15"
  }

**5. Search for a Ride by Location**
- To search for a ride by from/to location, send a GET request to `http://localhost:9091/rideapi/rides/Athlone/Kinnegad`.

**6. Book a Ride**
- To book a ride, send a POST request to `http://localhost:9091/rideapi/rides/booking` with the following body:
  ```json
  {
    "rideId": 1,
    "passengerId": 3
  }

**Note**: The booking will only be successful if there are available spaces, the ride exists, and the same booking for the same passenger does not already exist.

**7. Edit a Ride**
- To edit a ride, send a PUT request to `http://localhost:9091/rideapi/rides/{rideId}` with the updated ride details in the request body. Replace `{rideId}` with the actual ID of the ride.

**8. Get a Ride**
- To get details of a specific ride, send a GET request to `http://localhost:9091/rideapi/rides/{rideId}`. Replace `{rideId}` with the actual ID of the ride.

## ReviewService Use Cases

**1. Add a Review**
- To add a review, send a POST request to `http://localhost:9099/reviewapi/reviews` with the following body:
  ```json
  {
    "reviewedId": "1",
    "rating": "4",
    "comment": "Good Ride"
  }

**2. Get Reviews for a User**
- To get the reviews for a specific user, send a GET request to `http://localhost:9099/reviewapi/reviews/{reviewedId}`. Replace `{reviewedId}` with the ID of the user for whom you want to see reviews.

**3. Delete a Review**
- To delete a specific review, send a DELETE request to `http://localhost:9099/reviewapi/reviews/{Id}`. Replace `{Id}` with the ID of the review you want to delete.

