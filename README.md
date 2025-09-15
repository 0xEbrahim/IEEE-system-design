# IEEE-system-design

### Functional requirements
- Recommendation system
- Pushing notifications
  
- Admin
  - add new movies
  - handle user interactions and monitoring bad actions
  - has the authority to delete / update / get users/reviews
    
- User
    - Rate a moview and write a review
    - Share a review or a moview with friends
    - Search for a movie

### Non-functional requirements
- (Availability >>> Consistency) I want to site to be up as much as i can to serve more users that search for a moview so i will go for availablity over consistency
    - If the user's rating lagged a few seconds before applying it to the movie
- I need to reduce latency and enhance response time especially with the seaching and listing functionalities
- as our system can be huge and serve millions of users so I need to achieve scalablity and maintaince to the system making it easy to be scaled
- Monitoring and logging to make it easy to debug and monitor the website interactions

### Starter design
  - As the system will serve users (Clients) so we need a server and data base to store the data
  - This design will serve and works well but it will be a bottleneck when scale grows up
<img width="727" height="275" alt="image" src="https://github.com/user-attachments/assets/bbffb7bf-7f7c-4f96-a04a-fb7a3267b50e" />


### Databse modeling
<img width="1367" height="530" alt="image" src="https://github.com/user-attachments/assets/d5a0076a-bcb2-46f0-8301-878dc1394bd6" />

- Assuming this is our database modeling as a high level.
- Now i will dive into the size of each of it
  - Assuming:
    - Movie data arround 1500 byte
    - User data around 600 byte
    - review data around 500 byte
    - notification data around 400 byte
  - I assumed the number based on the columns on  each table, as the moview has a vide trailer url, and cast members so it can be big.
  - User can have a password hashed and name, email
  - review can have a big review text
  - nitification also have text



### Scaling assuamptaions and back Back-of-the-envelope Estimation
- Assuming it is a huge movies platform like IMDB/Letterboxd I will go with Daily active users of 20M
  - DAO = 20M
- as the movie can be 2 hours long, i will assume average user can rate and review 2 movies per day
  - Review per day = 2 * 20M = 40M
- One user can share 4 to 6 movie or review per day
  - Share review or moview per day = 5 * 20M = 100M
- There will be over head to due the read operation on the home page, user opening a movie or list a collection
  - Assuming this to be 10 per user
  - Reads per day = 10 * 20M = 200M
  - Over all reads per second = 200M + 100M (Sharing) = 300M
- Creating and adding new movies is only an admin operation so I will assume he will add 5 movies per day
  - Movies per day = 5
- Query per second (QPS)
  - One user -> 20M Write, 300M Read = 320M Request per day
    - QPS
      - Reads per second = 300M/24*60*60=3472
      - Writes per second = 40M/24*60*60=460
    - QPS = 460+3472 = 3932
    - On the peak we can multiply this by 5 = 5 * 3702 = 19660
  - Calculating sizes
    - 5 movies can be added per day = 5 Notifications, 40M review per day
        - 5 * 1500 + 5 * 400 = 9500 byte per day
        - Reviews: 40M/day * 500B = 20 GB/day
    - Movie and notification can be neglected due to high review
    - over 10 years one single database won't fit as storage can be more +70 TB
- we can see read >>> Writes

  ### API design
  - POST /api/v1/movies
  body:
    name
    trailer
    members, ...
 -  PATCH /api/v1/movies/{id}
    

    
