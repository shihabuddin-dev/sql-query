-- CREATE USERS TABLE

 CREATE TABLE IF NOT EXISTS users (
        id SERIAL PRIMARY KEY,
        name VARCHAR(100) NOT NULL,
        email VARCHAR(150) UNIQUE NOT NULL,
        password TEXT NOT NULL,
        phone VARCHAR(15) NOT NULL,
       role VARCHAR(20) NOT NULL CHECK (role IN ('Admin', 'Customer'))
);
drop table users

-- INSERT DATA INTO USERS TABLE 

INSERT INTO users (id, name, email, password, phone, role) VALUES
(1, 'Alice', 'alice@example.com', 'pass123', '1234567890', 'Customer'),
(2, 'Bob', 'bob@example.com', 'pass123', '0987654321', 'Admin'),
(3, 'Charlie', 'charlie@example.com', 'pass123', '1122334455', 'Customer');


    
-- CREATE VEHICLES TABLE 

CREATE TABLE IF NOT EXISTS vehicles (
        vehicle_id SERIAL PRIMARY KEY,	
        name VARCHAR(100) NOT NULL,
        type VARCHAR(15) NOT NULL CHECK (type IN('car','bike','truck')),
        model INT NOT NULL,
        registration_number VARCHAR(20)	UNIQUE NOT NULL,
        rental_price INT NOT NULL,
        status VARCHAR(50) NOT NULL CHECK (status IN('available', 'rented', 'maintenance'))
  )

  -- INSERT DATA INTO VEHICELS TABLE

  INSERT INTO vehicles (
  vehicle_id, name, type, model, registration_number, rental_price, status
) VALUES
(1, 'Toyota Corolla', 'car', 2022, 'ABC-123', 50, 'available'),
(2, 'Honda Civic', 'car', 2021, 'DEF-456', 60, 'rented'),
(3, 'Yamaha R15', 'bike', 2023, 'GHI-789', 30, 'available'),
(4, 'Ford F-150', 'truck', 2020, 'JKL-012', 100, 'maintenance');


-- CREATE BOOKINGS TABLE

CREATE TABLE IF NOT EXISTS bookings(
        booking_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(id) ON DELETE CASCADE,
        vehicle_id INT REFERENCES vehicles(vehicle_id) ON DELETE CASCADE,
        start_date DATE NOT NULL,
        end_date DATE NOT NULL,
        status VARCHAR(15) NOT NULL CHECK (status IN('completed','confirmed','pending')) DEFAULT 'pending',
        total_cost INT NOT NULL
)


  -- INSERT DATA INTO BOOKINGS TABLE

INSERT INTO bookings (
  booking_id, user_id, vehicle_id, start_date, end_date, status, total_cost
) VALUES
(1, 1, 2, '2023-10-01', '2023-10-05', 'completed', 240),
(2, 1, 2, '2023-11-01', '2023-11-03', 'completed', 120),
(3, 3, 2, '2023-12-01', '2023-12-02', 'confirmed', 60),
(4, 1, 1, '2023-12-10', '2023-12-12', 'pending', 100);



  -- Query 1: INNER JOIN

SELECT
  b.booking_id,
  u.name AS customer_name,
  v.name AS vehicle_name,
  b.start_date,
  b.end_date,
  b.status
FROM bookings b
INNER JOIN users u
  ON b.user_id = u.id
INNER JOIN vehicles v
  ON b.vehicle_id = v.vehicle_id
ORDER BY b.booking_id;


-- Query 2: EXISTS

SELECT *
FROM vehicles v
WHERE NOT EXISTS (
  SELECT 1
  FROM bookings b
  WHERE b.vehicle_id = v.vehicle_id
);


-- Query 3: WHERE

SELECT *
FROM vehicles
WHERE status = 'available'
  AND type = 'car';


-- Query 4: GROUP BY and HAVING

SELECT
  v.name AS vehicle_name,
  COUNT(b.booking_id) AS total_bookings
FROM bookings b
INNER JOIN vehicles v
  ON b.vehicle_id = v.vehicle_id
GROUP BY v.name
HAVING COUNT(b.booking_id) > 2;