---
layout: post
title: "System Design: BookMyShow"
author: "Kevin Chang"
date: "2022-08-29 19:42:00"
# update_date: "2022-09-25 17:00:00"
excerpt_separator: <!--more-->
tag: System_Design
---

Design an online ticketing system that sells movie tickets like BookMyShow.
<!--more-->

**Functional Requirements**
1. The system should be able to list down cities where its cinemas are located.
2. Upon selecting the city, the system should display the movies released in that particular city.
3. Upon selecting the movie, the system should display the cinemas running that movie and its available showings.
4. The user should be able to select the show from a cinema and book their tickets.
5. The system should be able to show the user the seat arrangement of the cinema hall.
6. The user should be able to select multiple seats.
7. The user should be able to distinguish between available seats from booked ones.
8. The user shoule be able to put a hold on seats for 5-10 minutes before they finalize their booking.
9. The system should serve the tickets in First In First Out manner.

**Non-Functional Requirements**
1. The system should be scalable and highly available.
2. The system would need to be highly concurrent as there will be multiple booking requests for the same seat at any particular point in time.
3. Financial transactions systems must be secure and with ACID compliant databases.

---
**Traffic Estimates**
- Our service receives 3 billion page views per month.
- Our service sells 10 million tickets per month.
- 300:1 read-to-write ratio.

**Storage Estimates**
- There are 500 cities and each city has, on average, 10 cinemas.
- There are 2,000 seats in each cinema and each day has, on average, 2 showings.
- Each seat booking may store data such as bookingID, seatNumbers, movieID, showingID, timestamp, etc.
- All other entities (city, cinema, movie, showings, seats) may take ~50 bytes.
- Total storage per seat bookings: ~50 bytes
- Total storage for all possible seat bookings per day: $500 cities * 10 cinemas * 2,000 seats * 2 showings * 50 bytes$ = 1GB.
- Total storage for everything else per day: $500 cities * 10 cinemas * 2,000 seats * 2 showings * 50 bytes$ = 1GB.
- Total storage per year: $365 days * (1GB + 1GB)$ = 730GB.
- Total storage for 5 years: $730GB * 5$ = 3.65PB.

---

**System APIs**
- Search API
  - Cities by location
  - Movies by city
  - Cinemas by movie
  - Cinema halls by cinema

- Reservation API
  - Number of seats for showing of a movie

- Payments API
  - External payment processing service

---

**Database Design**

```python
class City:
    def __init__(self, cityId: int, state: str, zipcode: str, totalCinemas: int):
        self.cityId = cityId  # primary key
        self.state = state
        self.zipCode = zipCode
        # each city can have multiple cinemas
        self.totalCinemas = totalCinemas
```
```python
class Cinema:
    def __init__(self, cinemaID: int, name: str, cityID: int, totalCinemaHalls: int):
        self.cinemaID = cinemaID  # primary key
        self.name = name
        self.cityID = cityID  # foreign key
        # each cinema can have multiple cinema halls
        self.totalCinemaHalls = totalCinemaHalls
```

```python
class CinemaHall:
    def __init__(self, cinemaHallID: int, name: str, cinemaID: int, totalSeats: List[int]):
        self.cinemaHallID = cinemaHallID  # primary key
        self.name = name
        self.cinemaID = cinemaID  # foreign key
        # each cinema hall can have multiple seats
        self.totalSeats = totalSeats
```

```python
class CinemaSeat:
    def __init__(self, cinemaSeatID: int, seatType: int, status: int, cinemaHallID: int):
        self.cinemaSeatID = cinemaSeatID  # primary key
        self.seatNumber = seatNumber
        self.seatType = seatType  # enum
        self.cinemaHallID = cinemaHallID  # foreign key
```

```python
class Showing:
    def __init__(self, showingID: int, date: datetime, startTime: datetime, endTime: datetime, cinemaHallID: int, movieID: int):
        self.showingID = showingID  # primary key
        self.date = date
        self.startTime = startTime
        self.endTime = endTime
        self.cinemaHallID = cinemaHallID  # foreign key
        self.movieID = movieID  # foreign key
```

```python
class Movie:
    def __init__(self, movieID: int, title: str, description: str, director: str, releaseDate: datetime):
        self.movieID = movieID  # primary key
        self.title = title
        self.description = description
        self.director = director
        self.releaseDate = releaseDate
```

```python
class BookingSeat:
    def __init__(self, bookingSeatID: int, price: float, status: int,  cinemaSeatID: int, showID: int, bookingID: int):
        self.bookingSeatID = bookingSeatID  # primary key
        self.price = price
        self.status = status  # enum
        self.cinemaSeatID = cinemaSeatID  # foreign key
        self.showID = showID  # foreign key
        self.bookingID = bookingID  # foreign key
```

```python
class Booking:
    def __init__(self, bookingID: int, numBookingSeats: int, status: int, showID: int, userID: int):
        self.bookingID = bookingID  # primary key
        self.numCinemaSeats = numBookingSeats
        self.status = status  # enum
        self.showID = showID  # foreign key
        self.userID = userID  # foreign key
```

```python
class User:
    def __init__(self, userID: int, name: str, email: str, password: str, phone: str,):
        self.userID = userID  # primary key
        self.name = name
        self.email = email
        self.password = password
        self.phone = phone
```

```python
class Payment:
    def __init__(self, paymentID: int, amount: float, bookingID: int):
        self.paymentID = paymentID  # primary key
        self.amount = amount
        self.bookingID = bookingID  # foreign key
```

**Relationships**
- A movie can have multiple shows.
- A show can have multiple bookings.
- A user can have multiple bookings.

---

**High Level Design**
- The system's web servers will handle:
  - user sessions
- The systems's application servers will handle:
  - ticket management
  - storing data in the databases
  - work with cache servers to process reservations

```
Client <---> Load Balancers <---> Web Servers <---> Application Servers <-Cache-> Databases
```

---

**Component Design**

We need two background processes (`daemons`) to 
1. keep track of active reservations and
2. keep track of waiting users.

**ActiveReservationService** - Keeps track of all active reservations and remove any expired reservations.

- Store active reservations in a hashmap (linked list values).
  - The hashmap key will be the `showingID` and the value will be a linked list reservation node containing a `bookingID` and creation `timestamp`.
  - The oldest reservation node will be at the head of each linked list.
  - The reservation status in the `Booking` table will be listed as `Reserved`.
  - The seat availability status in the `BookingSeat` table will be listed as `Reserved`.
- Successful reservation booking
  - Update seat availability in the `BookingSeat` table as `Booked`.
  - Update reservation status in the `Booking` table as `Booked`.
  - Access hashmap at `showingID` and remove reservation node with `bookingID` from the linked list.
  - Interact with external financial services to process user payments.
  - Notify the `WaitingUserService` with updated information.
- Unsuccessful reservation booking
  - Update seat availability in the `BookingSeat` table as `Available`.
  - Update reservation status in the `Booking` table as `Expired`.
  - Access hashmap at `showingID` and remove reservation node with `bookingID` from the linked list.
  - Notify the `WaitingUserService` with updated information.

**WaitingUserService** - Keeps track of all waiting users and allows the longest waiting user to choose their seats as one becomes available.

- Store waiting users in a hashmap (linked list values).
  - The hashmap key will be `showingID` and the value will be a linked list waiting user node containing a `userID`, waiting start `timestamp`, and required seats `numSeats`.
  - The longest waiting user will be at the head of each linked list.
- Seats are available
  - Upon reservation expiry, the `ActiveReservationService` will notify this service with updated information.
  - This service will notify the longest waiting user for `showingID` and `numSeats`.
- Seats are unavailable
  - Upon successful booking, the `ActiveReservationService` will notify this service with updated information.
  - This service will iterate through and notify all waiting users for `showingID` who want to reserve more seats than are available.

---

**Concurrency**

SQL servers can use `Transaction Isolation Levels` to lock the rows when accessing them.
- `Serializable` is the highest isolation level and guarantees safety from `dirty`, `non-repeatable`, and `phantom` reads.
- Create a write lock while reading the rows to prevent others from updating the rows.
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN TRANSACTION;
    -- We want to reserve 3 seats (ID: 54, 55, 56) for showID=99
    SELECT * FROM BookingSeat WHERE showID=99 && bookingSeatID in (54, 55, 56) && status='AVAILABLE'

    -- If the number of rows returned by the above statment is three,
    -- we can update and return SUCCESS, otherwise return FAILURE
    UPDATE BookingSeat ...
    UPDATE Booking ...
COMMIT TRANSACTION
```

---

**Fault Tolerance**

To tolerate failures in the `ActiveReservationService` or `WaitingUserService`, we can either store state information in a database (slow but fault tolerant) or use active-passive configurations (added complexity but fault tolerant). 

---

**Partitioning**

**Database Partitioning**
- Partitioning based on `MovieID` will place all movie showings on the same database server.
- This may lead to uneven load distribution for a popular movie.
- Partitioning based on `ShowingID` may distribute the load more evenly.

**Application Service Partitioning**
- The web servers will manage all active user sessions and handle communication with the users.
- Use consistent hashing to allocate application servers for both `ActiveReservationService` and `WaitingUserService` based on `ShowingID`.
- All reservations and waiting users of a particular showing will be handled by a certain set of servers.
