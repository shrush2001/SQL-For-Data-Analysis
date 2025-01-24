-- Advanced SQL Questions and Answers based on the dataset

-- 1. Find the average height of users by gender using a Common Table Expression (CTE).
WITH AvgHeightByGender AS (
    SELECT 
        Gender,
        AVG(Height) AS AvgHeight
    FROM dating
    GROUP BY Gender
)
SELECT * FROM AvgHeightByGender;


-- 2. Create a trigger that automatically logs updates to the "Swiping History" column.
DELIMITER //
CREATE PROCEDURE SampleProcedure()
BEGIN
    SELECT 'Hello, World!';
END //
DELIMITER ;
_ _ create new trigger
DELIMITER //
CREATE TRIGGER SampleTrigger
AFTER INSERT ON dating
FOR EACH ROW
BEGIN
    INSERT INTO log_table (log_message) VALUES ('New record inserted.');
    BEGIN
    SELECT 'Correct usage!';
END;

-- 3. Write a query to find the top 3 occupations with the highest average swiping history using a window function.
SELECT 
    Occupation,
    AVG(Swiping_History) AS AvgSwipes,
    RANK() OVER (ORDER BY AVG(Swiping_History) DESC) AS Rank
FROM dating
GROUP BY Occupation
HAVING COUNT(*) > 1
LIMIT 3;

-- 4. Create a stored procedure to fetch users based on their "Looking For" and "Frequency of Usage".
DELIMITER //
CREATE PROCEDURE GetUsersByCriteria (
    IN relationshipType VARCHAR(50),
    IN usageFrequency VARCHAR(20)
)
BEGIN
    SELECT *
    FROM users
    WHERE Looking_For = relationshipType AND Frequency_of_Usage = usageFrequency;
END //
DELIMITER ;

-- 5. Use a CASE statement to classify users by their swiping activity.
SELECT 
    user_id, 
    `Swiping History`, 
    CASE
        WHEN `Swiping History` < 50 THEN 'Low Activity'
        WHEN `Swiping History` BETWEEN 50 AND 100 THEN 'Moderate Activity'
        ELSE 'High Activity'
    END AS ActivityLevel
FROM dating
LIMIT 0, 1000;


select * from dating;

-- 6. Find users who have more than 3 common interests using JSON functions.
SELECT 
    user_id,
    JSON_LENGTH(Interests) AS NumberOfInterests
FROM dating
WHERE JSON_LENGTH(Interests) > 3;

ALTER TABLE dating RENAME COLUMN `User ID` TO `user_id`;

-- 7. Create an index on the "Looking For" column to optimize search performance.
CREATE INDEX idx_looking_for ON dating (`Looking For`(255));

-- 8. Write a query to calculate the percentage of male and female users.
SELECT 
    Gender,
    COUNT(*) * 100.0 / (SELECT COUNT(*) FROM dating) AS Percentage
FROM dating
GROUP BY Gender;

-- 9. Use a CTE to find the average age of users grouped by their "Looking For" category.
WITH AvgAgeByLookingFor AS (
    SELECT 
        `Looking For`,
        AVG(Age) AS AvgAge
    FROM dating
    GROUP BY `Looking For`
)
SELECT * FROM AvgAgeByLookingFor;

-- 10. Write a query to find users with the highest swiping history in each occupation using a window function.
WITH RankedOccupations AS (
    SELECT 
        User_ID, 
        Occupation, 
        `Swiping History`, 
        RANK() OVER (PARTITION BY Occupation ORDER BY `Swiping History` DESC) AS `Rank`
    FROM dating
)
SELECT * 
FROM RankedOccupations
WHERE `Rank` = 1;

/* Transaction Control Language (TCL)
-- 11. Explain the difference between COMMIT, ROLLBACK, and SAVEPOINT in SQL with an example. */
BEGIN TRANSACTION

UPDATE users SET is_active = 1 WHERE user_id = 101;
SAVEPOINT update_active;

UPDATE users SET is_active = 0 WHERE user_id = 102;

-- Rollback to the savepoint
ROLLBACK TO update_active;

-- Commit the transaction
COMMIT;

 Stored Procedures
--12. Write a stored procedure to fetch all users who signed up in the last 7 days.
DELIMITER $$

CREATE PROCEDURE GetRecentUsers()
BEGIN
    SELECT * 
    FROM users 
    WHERE signup_date >= NOW() - INTERVAL 7 DAY;
END $$

DELIMITER ;


/*13. Identify the top 3 most active users (based on swiping history) for each gender and calculate their rank. Include the user ID, gender, swiping history, and rank.*/

SELECT
    user_id,
    Gender,
    `Swiping History`,
    Rankh
FROM
    RankedUsers
WHERE
    Rankh <= 3;
    select * from dating;
    
/* 14.Rank users by the number of swipes within each gender group.*/
SELECT 
    `User_ID`, `Gender`, `Swiping History`, 
    RANK() OVER (PARTITION BY `Gender` ORDER BY `Swiping History` DESC) AS rankh
FROM dating;

/*15.Find users who are seeking "Marriage" and have a Ph.D. but don't have children.*/
SELECT 
    `User_ID`, `Age`, `Gender`, `Looking For`, `Education Level`, `Children` 
FROM dating
WHERE `Looking For` = 'Marriage' 
  AND `Education Level` = 'Ph.D.' 
  AND `Children` = 'No';
  
  /*16. Use a CTE to calculate the average swipes by education level, and then find education levels where the average swipes are greater than 80.*/
WITH AvgSwipes AS (
    SELECT 
        `Education Level`, 
        AVG(`Swiping History`) AS avg_swipes
    FROM dating
    GROUP BY `Education Level`
)
SELECT 
    `Education Level`, avg_swipes 
FROM AvgSwipes 
WHERE avg_swipes > 80;

/*17.Update a user’s "Looking For" to "Friendship," and roll back if the update affects users under 18 years old.*/
DELIMITER //

CREATE PROCEDURE UpdateLookingFor(IN user_id INT)
BEGIN
    DECLARE user_age INT;

    -- Fetch the user's age
    SELECT `Age` INTO user_age
    FROM dating
    WHERE `User_ID` = user_id;

    -- Start the transaction
    START TRANSACTION;

    -- Check if the user is under 18
    IF age < 18 THEN
        -- Rollback if the condition is met
        ROLLBACK;
    ELSE
        -- Otherwise, update the record and commit
        UPDATE dating
        SET `Looking For` = 'Friendship'
        WHERE `User_ID` = user_id;
        COMMIT;
    END IF;
END;
//

DELIMITER ;

        

    
   /*18. Find users with exactly three interests.*/
   SELECT 
    `User_ID`, `Interests` 
FROM dating 
WHERE LENGTH(`Interests`) - LENGTH(REPLACE(`Interests`, ',', '')) = 2;

/*19. List the top 3 occupations with the highest average "Swiping History."*/
SELECT 
    `Occupation`, 
    AVG(`Swiping History`) AS avg_swipes
FROM dating
GROUP BY `Occupation`
ORDER BY avg_swipes DESC
LIMIT 3;

/*20.Find users aged 25–35 who swipe more than 50 times weekly and are seeking "Long-term Relationship."*/
SELECT 
    `User_ID`, `Age`, `Swiping History`, `Looking For`, `Frequency of Usage`
FROM dating
WHERE `Age` BETWEEN 25 AND 35
  AND `Swiping History` > 50
  AND `Looking For` = 'Long-term Relationship'
  AND `Frequency of Usage` = 'Weekly';
  
  

















 









