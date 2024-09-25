# Question
The CS50 Duck has been stolen! The town of Fiftyville has called upon you to solve the mystery of the stolen duck. Authorities believe that the thief stole the duck and then, shortly afterwards, took a flight out of town with the help of an accomplice. Your goal is to identify:

- Who the thief is,
- What city the thief escaped to, and
- Who the thief’s accomplice is who helped them escape

All you know is that the theft **took place on July 28, 2023** and that it **took place on Humphrey Street**.

How will you go about solving this mystery? The Fiftyville authorities have taken some of the town’s records from around the time of the theft and prepared a SQLite database for you, `fiftyville.db`, which contains tables of data from around the town. You can query that table using SQL `SELECT` queries to access the data of interest to you. Using just the information in the database, your task is to solve the mystery.

# Resource
Download the database file from [here.](https://github.com/ShivanshShukla01/Revision/raw/refs/heads/main/CS50x/SQL/fiftyville.zip) Try to do it without any help with only the information provided above.

# How this works ?
For the visual explanation, you can go [here.](https://youtu.be/sy1RvxhMpYk)

# Solution
```sql
------------------------------CASE INFORMATION----------------------------

SELECT *
FROM crime_scene_reports
WHERE day = 28
    AND month = 7
    AND street = 'Humphrey Street'
    AND description LIKE '%theft%';

---------------------------WITNESSES----------------------------------

--as per the description IN case info
SELECT *
FROM interviews
WHERE month = 7
    AND day = 28
    AND transcript LIKE '%bakery%';
--the wintesses are Ruth(161), Eugene(162) AND Raymond(163)

----------------------Investigation Started-----------------------

---as per ruth statement, car left within 10 minutes of theft hence left before 10:25 FROM bakery parking lot IN a car
--among these cars, thief must be IN one of them
SELECT *
FROM bakery_security_logs
WHERE day = 28
    AND month = 7
    AND hour = 10
    AND minute < 25
    AND activity = 'exit';

--as per eugene statement, he saw the thief IN morning at ATM on leggett street withdrawing some money
--one of the transaction by the done by the thief
SELECT *
FROM atm_transactions
WHERE day = 28
    AND month = 7
    AND atm_location = 'Leggett Street'
    AND transaction_type = 'withdraw';

--as per raymond, when thief was leaving, he called someone AND talked for less than a minute , he heard they were planning to leave the fiftyville tomorrow FROM the earliest flight AND thief asked the person on other end of call to purchase the ticket
--calls that we made for less than a minute that day
SELECT *
FROM phone_calls
WHERE day = 28
    AND month = 7
    AND duration < 60;


--since thief wanted to leave the fiftyville earliest on 29/07. He most probably has chosen flight among one of these
SELECT *
FROM flights
WHERE day = 29
    AND month = 7
    AND origin_airport_id = (
        SELECT id
        FROM airports
        WHERE city = 'Fiftyville')
    ORDER BY hour
    LIMIT 1;

--name of people whose license plate is captured within 10 minutes of theft
SELECT *
FROM people
WHERE license_plate IN (
    SELECT license_plate
    FROM bakery_security_logs
    WHERE day = 28
        AND month = 7
        AND hour = 10
        AND minute < 25
        AND activity = 'exit');

--people who have left the bakery within 10 minutes of theft AND boarded the earliest flight on next day means on 29/07
SELECT *
FROM passengers
WHERE passport_number IN (
    SELECT passport_number
    FROM people
    WHERE license_plate IN (
        SELECT license_plate
        FROM bakery_security_logs
        WHERE day = 28
            AND month = 7
            AND hour = 10
            AND minute < 25
            AND activity = 'exit'))
    AND flight_id = (
        SELECT id
        FROM flights
        WHERE day = 29
            AND month = 7
            AND origin_airport_id = (
                SELECT id
                FROM airports
                WHERE city = 'Fiftyville')
            ORDER BY hour
            LIMIT 1);

---------account number---
--person id of those who withdrew money FROM atm AND one of these is thief
SELECT *
FROM bank_accounts
WHERE account_number IN (
    SELECT account_number
    FROM atm_transactions
    WHERE day = 28
        AND month = 7
        AND atm_location = 'Leggett Street'
        AND transaction_type = 'withdraw');

--name of people who withdrew money early morning on the day of theft
SELECT *
FROM people
WHERE id IN (
    SELECT person_id
    FROM bank_accounts
    WHERE account_number IN (
        SELECT account_number
        FROM atm_transactions
        WHERE day = 28
            AND month = 7
            AND atm_location = 'Leggett Street'
            AND transaction_type = 'withdraw'));

--------THE PEOPLE WHO WITHDREW MONEY EARLY MORNING AND ALSO BOARDED THE EARLIEST FLIGHT OF TOMORROW
SELECT *
FROM people
WHERE id IN (
    SELECT person_id
    FROM bank_accounts
    WHERE account_number IN (
        SELECT account_number
        FROM atm_transactions
        WHERE day = 28
            AND month = 7
            AND atm_location = 'Leggett Street'
            AND transaction_type = 'withdraw'))
    AND passport_number IN (
        SELECT passport_number
        FROM passengers
        WHERE passport_number IN (
            SELECT passport_number
            FROM people
            WHERE license_plate IN (
                SELECT license_plate
                FROM bakery_security_logs
                WHERE day = 28
                    AND month = 7
                    AND hour = 10
                    AND minute < 25
                    AND activity = 'exit'))
            AND flight_id = (
                SELECT id
                FROM flights
                WHERE day = 29
                AND month = 7
                AND origin_airport_id = (
                    SELECT id
                    FROM airports
                    WHERE city = 'Fiftyville')
                ORDER BY hour
                LIMIT 1));

----CALL LOG of thief who talked to the person on the moment of theft AND witnessed by raymond

SELECT *
FROM phone_calls
WHERE day = 28
    AND month = 7
    AND duration < 60
    AND caller IN (
        SELECT phone_number
        FROM people
        WHERE id IN (
            SELECT person_id
            FROM bank_accounts
            WHERE account_number IN (
                SELECT account_number
                FROM atm_transactions
                WHERE day = 28
                    AND month = 7
                    AND atm_location = 'Leggett Street'
                    AND transaction_type = 'withdraw'))
            AND passport_number IN (
                SELECT passport_number
                FROM passengers
                WHERE passport_number IN (
                    SELECT passport_number
                    FROM people
                    WHERE license_plate IN (
                        SELECT license_plate
                        FROM bakery_security_logs
                        WHERE day = 28
                            AND month = 7
                            AND hour = 10
                            AND minute < 25
                            AND activity = 'exit'))
                    AND flight_id = (
                        SELECT id
                        FROM flights
                        WHERE day = 29
                            AND month = 7
                            AND origin_airport_id = (
                                SELECT id
                                FROM airports
                                WHERE city = 'Fiftyville')
                            ORDER BY hour
                            LIMIT 1)));


----------------------------IDENTIFICATION OF THIEF-------------------
SELECT *
FROM people
WHERE phone_number = (
    SELECT caller
    FROM phone_calls
    WHERE day = 28
        AND month = 7
        AND duration < 60
        AND caller IN (
            SELECT phone_number
            FROM people
            WHERE id IN (
                SELECT person_id
                FROM bank_accounts
                WHERE account_number IN (
                    SELECT account_number
                    FROM atm_transactions
                    WHERE day = 28
                        AND month = 7
                        AND atm_location = 'Leggett Street'
                        AND transaction_type = 'withdraw'))
                AND passport_number IN (
                    SELECT passport_number
                    FROM passengers
                    WHERE passport_number IN (
                        SELECT passport_number
                        FROM people
                        WHERE license_plate IN (
                            SELECT license_plate
                            FROM bakery_security_logs
                            WHERE day = 28
                                AND month = 7
                                AND hour = 10
                                AND minute < 25
                                AND activity = 'exit'))
                        AND flight_id = (
                            SELECT id
                            FROM flights
                            WHERE day = 29
                                AND month = 7
                                AND origin_airport_id = (
                                    SELECT id
                                    FROM airports
                                    WHERE city = 'Fiftyville')
                                ORDER BY hour
                                LIMIT 1))));

----------------IDENTIFICATION OF ACCOMPLIANCE--------------------------
SELECT *
FROM people
WHERE phone_number = (
    SELECT receiver
    FROM phone_calls
    WHERE day = 28
        AND month = 7
        AND duration < 60
        AND caller IN (
            SELECT phone_number
            FROM people
            WHERE id IN (
                SELECT person_id
                FROM bank_accounts
                WHERE account_number IN (
                    SELECT account_number
                    FROM atm_transactions
                    WHERE day = 28
                        AND month = 7
                        AND atm_location = 'Leggett Street'
                        AND transaction_type = 'withdraw'))
                AND passport_number IN (
                    SELECT passport_number
                    FROM passengers
                    WHERE passport_number IN (
                        SELECT passport_number
                        FROM people
                        WHERE license_plate IN (
                            SELECT license_plate
                            FROM bakery_security_logs
                            WHERE day = 28
                                AND month = 7
                                AND hour = 10
                                AND minute < 25
                                AND activity = 'exit'))
                        AND flight_id = (
                            SELECT id
                            FROM flights
                            WHERE day = 29
                                AND month = 7
                                AND origin_airport_id = (
                                    SELECT id
                                    FROM airports
                                    WHERE city = 'Fiftyville')
                                ORDER BY hour
                                LIMIT 1))));

-------------------------IDENTIFICATION OF CITY THEY ESCAPED TO---------------------

SELECT city
FROM airports
WHERE id = (
    SELECT destination_airport_id
    FROM flights
    WHERE id = (
        SELECT flight_id
        FROM passengers
        WHERE passport_number = (
            SELECT passport_number
            FROM people
            WHERE phone_number = (
                SELECT caller
                FROM phone_calls
                WHERE day = 28
                    AND month = 7
                    AND duration < 60
                    AND caller IN (
                        SELECT phone_number
                        FROM people
                        WHERE id IN (
                            SELECT person_id
                            FROM bank_accounts
                            WHERE account_number IN (
                                SELECT account_number
                                FROM atm_transactions
                                WHERE day = 28
                                    AND month = 7
                                    AND atm_location = 'Leggett Street'
                                    AND transaction_type = 'withdraw'))
                            AND passport_number IN (
                                SELECT passport_number
                                FROM passengers
                                WHERE passport_number IN (
                                    SELECT passport_number
                                    FROM people
                                    WHERE license_plate IN (
                                        SELECT license_plate
                                        FROM bakery_security_logs
                                        WHERE day = 28
                                            AND month = 7
                                            AND hour = 10
                                            AND minute < 25
                                            AND activity = 'exit'))
                                    AND flight_id = (
                                        SELECT id
                                        FROM flights
                                        WHERE day = 29
                                        AND month = 7
                                        AND origin_airport_id = (
                                            SELECT id
                                            FROM airports
                                            WHERE city = 'Fiftyville')
                                        ORDER BY hour
                                        LIMIT 1)))))));

```