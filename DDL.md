## Data Definition Language for MySQL

```sql
CREATE TABLE IF NOT EXISTS airlines (
        airline_id INT PRIMARY KEY, -- Unique ID for each airline
        name VARCHAR(255) NOT NULL, -- Airline name
        alias VARCHAR(255), -- Alternative name or abbreviation
        iata VARCHAR(10) UNIQUE, -- Unique IATA airline code
        icao VARCHAR(10) UNIQUE, -- Unique ICAO airline code
        country VARCHAR(100), -- Country where the airline is based
        active CHAR(1) DEFAULT 'Y' CHECK (active IN ('Y', 'N')) -- Whether the airline is active (Y/N)
    );
    """,

    # 2. Airports
    """
    CREATE TABLE IF NOT EXISTS airports (
        airport_id INT PRIMARY KEY, -- Unique ID for each airport
        name VARCHAR(255) NOT NULL, -- Airport name
        city VARCHAR(255) NOT NULL, -- City where the airport is located
        country VARCHAR(255) NOT NULL, -- Country where the airport is located
        iata VARCHAR(10) UNIQUE, -- Unique IATA airport code
        icao VARCHAR(10) UNIQUE, -- Unique ICAO airport code
        latitude FLOAT NOT NULL, -- Latitude coordinate
        longitude FLOAT NOT NULL, -- Longitude coordinate
        altitude INT, -- Altitude in feet
        timezone FLOAT, -- Timezone offset from UTC
        dst CHAR(1), -- Daylight saving time code
        tz_database_time_zone VARCHAR(100) -- Timezone database name
    );
    """,

    # 3. Aircrafts
    """
    CREATE TABLE IF NOT EXISTS aircrafts (
        aircraft_id INT PRIMARY KEY, -- Unique ID for each aircraft type
        model VARCHAR(255) NOT NULL, -- Aircraft model (e.g., A320, B737)
        manufacturer VARCHAR(255) NOT NULL, -- Manufacturer name (e.g., Airbus, Boeing)
        iata_code VARCHAR(10) UNIQUE, -- Unique IATA aircraft code
        icao_code VARCHAR(10) UNIQUE, -- Unique ICAO aircraft code
        seats INT NOT NULL -- Number of seats available
    );
    """,

    # 4. Routes
    """
    CREATE TABLE IF NOT EXISTS routes (
        route_id INT PRIMARY KEY AUTO_INCREMENT, -- Unique ID for each route
        airline_id INT NOT NULL, -- Airline operating the route
        source_airport_id INT NOT NULL, -- Departure airport ID
        destination_airport_id INT NOT NULL, -- Arrival airport ID
        codeshare CHAR(1), -- Indicates if the route is a codeshare
        stops INT DEFAULT 0, -- Number of stops in the route
        equipment VARCHAR(50), -- Equipment or aircraft type used
        FOREIGN KEY (airline_id) REFERENCES airlines(airline_id)
            ON DELETE RESTRICT
            ON UPDATE CASCADE,
        FOREIGN KEY (source_airport_id) REFERENCES airports(airport_id)
            ON DELETE RESTRICT
            ON UPDATE CASCADE,
        FOREIGN KEY (destination_airport_id) REFERENCES airports(airport_id)
            ON DELETE RESTRICT
            ON UPDATE CASCADE
    );
    """,

    # 5. Flights
    """
    CREATE TABLE IF NOT EXISTS flights (
        flight_id INT PRIMARY KEY AUTO_INCREMENT, -- Unique ID for each flight
        route_id INT NOT NULL, -- Associated route ID
        flight_number VARCHAR(20) NOT NULL, -- Flight number (e.g., AA100)
        departure_time TIME NOT NULL, -- Scheduled departure time
        arrival_time TIME NOT NULL, -- Scheduled arrival time
        flight_date DATE NOT NULL, -- Date of the flight
        aircraft_id INT NOT NULL, -- Aircraft assigned to the flight
        status VARCHAR(50) DEFAULT 'Scheduled', -- Flight status (Scheduled, Delayed, etc.)
        FOREIGN KEY (route_id) REFERENCES routes(route_id)
            ON DELETE CASCADE
            ON UPDATE CASCADE,
        FOREIGN KEY (aircraft_id) REFERENCES aircrafts(aircraft_id)
            ON DELETE RESTRICT
            ON UPDATE CASCADE
    );
    """,

    # 6. Passengers
    """
    CREATE TABLE IF NOT EXISTS passengers (
        passenger_id INT PRIMARY KEY, -- Unique ID for each passenger
        first_name VARCHAR(100) NOT NULL, -- Passenger's first name
        last_name VARCHAR(100) NOT NULL, -- Passenger's last name
        passport_number VARCHAR(50) UNIQUE NOT NULL, -- Unique passport number
        nationality VARCHAR(100) NOT NULL, -- Nationality of the passenger
        date_of_birth DATE NOT NULL -- Passenger's date of birth
    );
    """,

    # 7. Bookings
    """
    CREATE TABLE IF NOT EXISTS bookings (
        booking_id INT PRIMARY KEY AUTO_INCREMENT, -- Unique ID for each booking
        passenger_id INT NOT NULL, -- Passenger who made the booking
        flight_id INT NOT NULL, -- Flight booked
        booking_date DATE NOT NULL, -- Date when the booking was made
        seat_number VARCHAR(10) NOT NULL, -- Seat number assigned
        ticket_price DECIMAL(10,2) NOT NULL, -- Price paid for the ticket
        FOREIGN KEY (passenger_id) REFERENCES passengers(passenger_id)
            ON DELETE CASCADE
            ON UPDATE CASCADE,
        FOREIGN KEY (flight_id) REFERENCES flights(flight_id)
            ON DELETE CASCADE
            ON UPDATE CASCADE
    );
```