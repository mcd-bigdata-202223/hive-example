# hive-example

Download the Lubuntu Virtual Machine updated with Hive Installed
https://myipleiria.sharepoint.com/:u:/s/BigData/ES6I4GK9Zz5Eq5RhuVndlsgB_lQn1icjTQXcTeM56Jhz9w?e=Rvc3Pg

 1. Open the **data** folder in Visual Studio Code.
 2. Run a new **Terminal**
 3. Start Apache HDFS and YARN
```
start-dfs.sh
start-yarn.sh
```
 4. Start the **Apache Hive** console:

```
hive
```
It will show a command prompt:

> hive>

 
5. Create a new managed table for *students*
```
CREATE TABLE IF NOT EXISTS students (
     id TINYINT,
     name STRING,
     number BIGINT,
     course STRING,
     final_grade TINYINT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

6. Load data from the file "students.txt" to the table

```
LOAD DATA LOCAL INPATH "students.txt" INTO TABLE students;
```

7. Now you can query the table. Example:
```
SELECT * FROM students;
```

> 1       Joe Citizen		2220001	Master Degree in Data Science	19
> 2       Jane Doe		2220002	Master Degree in Data Science	20

8. Repeat the steps from 5. to 6. to create a table for messages.

```
CREATE TABLE IF NOT EXISTS messages (
     student_id TINYINT,
     text STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```
```
LOAD DATA LOCAL INPATH "messages.txt" INTO TABLE messages;
```

9. Now you can query both tables. Examples:
9.1. Select students with positive grade
```
SELECT * FROM students WHERE final_grade > 9;
```
9.2. Order students by grade ascending
```
SELECT * FROM students ORDER BY grade ASC;
```
9.3. Count the number of students per course
```
SELECT course, count(1) FROM students GROUP BY course;
```
9.4. Select all messages with students' names:
```
SELECT m.text, s.name FROM students s JOIN messages m ON (s.id = m.student_id);
```
9.5. Select all messages with students' names and student names **without messages**:
```
SELECT m.text, s.name FROM students s LEFT OUTER JOIN messages m ON (s.id = m.student_id);
```
9.6. Select all messages with students' names and messages **without student**:
```
SELECT m.text, s.name FROM students s RIGHT OUTER JOIN messages m ON (s.id = m.student_id);
```
9.7. Select all messages with students' names, student names **without messages**, and messages **without student**:
```
SELECT m.text, s.name FROM students s FULL OUTER JOIN messages m ON (s.id = m.student_id);
```
9.8. Count the number of messages per student:
```
SELECT s.name, count(m.text) FROM students s LEFT JOIN messages m ON (s.id = m.student_id) GROUP BY s.name;
```

### Create a partitioned table, by course:
1. Create a new table
```
CREATE TABLE IF NOT EXISTS students_by_course (
	name STRING,
	number BIGINT,
	final_grade TINYINT
)
PARTITIONED BY(course string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
``` 

2. Load data from students table to the new table
```
INSERT INTO TABLE students_by_course PARTITION(course)
SELECT name, number, final_grade, course FROM students;
```