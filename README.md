# hive-example

Download the Lubuntu Virtual Machine updated with Hive Installed
https://myipleiria.sharepoint.com/:u:/s/BigData/ES6I4GK9Zz5Eq5RhuVndlsgB_lQn1icjTQXcTeM56Jhz9w?e=Rvc3Pg

## Open Visual Studio Code and Start Hadoop

 1. Download and open the **hive-example** folder in Visual Studio Code.
 2. Run a new **Terminal**.
 3. Start Apache HDFS and YARN:
```
start-dfs.sh
start-yarn.sh
```

## Start Apache Hive
**On the first run only**, initialise the MetaStore database!
```
schematool -initSchema -dbType derby
```

Then start the **Apache Hive** console:

```
hive
```
It will show a command prompt:

> hive>

 

## Create a table for *students*

1. Create a new managed table for *students*:
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

2. Load data from the file "students.txt" to the table:

```
LOAD DATA LOCAL INPATH "students.txt" INTO TABLE students;
```

3. Now you can query the table. Example:
```
SELECT * FROM students;
```

> 1       Joe Citizen		2220001	Master Degree in Data Science	19
> 2       Jane Doe		2220002	Master Degree in Data Science	20


## Create a table for *messages*
1. Create a new managed table for *messages*:
```
CREATE TABLE IF NOT EXISTS messages (
     student_id TINYINT,
     text STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```
2. Load data from the file "messages.txt" to the table:
```
LOAD DATA LOCAL INPATH "messages.txt" INTO TABLE messages;
```
## Perform queries
Now you can query both tables. Examples:

1. Select students with positive grade:
```
SELECT * FROM students WHERE final_grade > 9;
```
2. Order students by grade ascending:
```
SELECT * FROM students ORDER BY grade ASC;
```
3. Count the number of students per course:
```
SELECT course, count(1) FROM students GROUP BY course;
```
4. Select all messages with students' names:
```
SELECT m.text, s.name FROM students s JOIN messages m ON (s.id = m.student_id);
```
5. Select all messages with students' names and student names **without messages**:
```
SELECT m.text, s.name FROM students s LEFT OUTER JOIN messages m ON (s.id = m.student_id);
```
6. Select all messages with students' names and messages **without student**:
```
SELECT m.text, s.name FROM students s RIGHT OUTER JOIN messages m ON (s.id = m.student_id);
```
7. Select all messages with students' names, student names **without messages**, and messages **without student**:
```
SELECT m.text, s.name FROM students s FULL OUTER JOIN messages m ON (s.id = m.student_id);
```
8. Count the number of messages per student:
```
SELECT s.name, count(m.text) FROM students s LEFT JOIN messages m ON (s.id = m.student_id) GROUP BY s.name;
```

### Create a partitioned table, by course:
1. Create a new table:
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

2. Load data from students table to the new table:
```
INSERT INTO TABLE students_by_course PARTITION(course)
SELECT name, number, final_grade, course FROM students;
```