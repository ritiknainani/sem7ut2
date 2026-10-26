# BDA Practical & Viva — 1-Day Master Survival Guide 🎯

**Mumbai University | Sem 7 Computer Engineering | TSEC, Bandra**

> **How to use this guide:** Part 1 = code templates to memorise/run. Part 2 = questions asked AT your machine. Part 3 = 70+ rapid-fire viva questions. Part 4 = survival tactics. **Total study time: 6-8 hours.**

---

# PART 1: Practical Code Templates (Memorise These)

---

## Practical 1 — HDFS Commands

**What it does:** Basic file operations on the Hadoop Distributed File System.

### Must-Know Commands (2-3 per practical slip)

```bash
# List files in HDFS root
hdfs dfs -ls /

# Create a directory
hdfs dfs -mkdir /user/student/input

# Upload a local file to HDFS
hdfs dfs -put localfile.txt /user/student/input/

# View file contents
hdfs dfs -cat /user/student/input/localfile.txt

# Copy file within HDFS
hdfs dfs -cp /user/student/input/file.txt /user/student/backup/

# Move/rename
hdfs dfs -mv /user/student/input/old.txt /user/student/input/new.txt

# Download from HDFS to local
hdfs dfs -get /user/student/input/file.txt ./local_copy.txt

# Delete a file
hdfs dfs -rm /user/student/input/file.txt

# Delete a directory recursively
hdfs dfs -rm -r /user/student/input

# Check disk usage
hdfs dfs -du -h /user/student/

# Change replication factor
hdfs dfs -setrep -w 2 /user/student/input/file.txt

# Display last 1KB of a file
hdfs dfs -tail /user/student/input/file.txt
```

**What examiner checks:** You typing commands live, getting output without errors. Know at least `ls`, `mkdir`, `put`, `cat`, `get`, `rm`.

---

## Practical 2 — Sqoop (Import/Export between Hadoop & RDBMS)

**What it does:** Transfers data between MySQL (relational DB) and HDFS/Hive.

```bash
# Import a MySQL table into HDFS
sqoop import \
  --connect jdbc:mysql://localhost/testdb \
  --username root --password root \
  --table employees \
  --target-dir /user/student/employees \
  --m 1

# Import into Hive directly
sqoop import \
  --connect jdbc:mysql://localhost/testdb \
  --username root --password root \
  --table employees \
  --hive-import \
  --m 1

# Export from HDFS back to MySQL
sqoop export \
  --connect jdbc:mysql://localhost/testdb \
  --username root --password root \
  --table employees_export \
  --export-dir /user/student/employees \
  --m 1

# List databases
sqoop list-databases --connect jdbc:mysql://localhost/ --username root --password root

# List tables
sqoop list-tables --connect jdbc:mysql://localhost/testdb --username root --password root
```

**What examiner checks:** Successful import showing row count, then `hdfs dfs -cat` to show imported data.

---

## Practical 3 — HBase

**What it does:** NoSQL column-family database on top of HDFS. Interactive shell commands.

```bash
# Start HBase shell
hbase shell

# Create table with column families
create 'students', 'personal', 'academic'

# Insert data (put 'table', 'rowkey', 'cf:qualifier', 'value')
put 'students', 'row1', 'personal:name', 'Ritik'
put 'students', 'row1', 'personal:age', '22'
put 'students', 'row1', 'academic:marks', '85'
put 'students', 'row2', 'personal:name', 'Amit'
put 'students', 'row2', 'academic:marks', '90'

# Read all data
scan 'students'

# Read specific row
get 'students', 'row1'

# Read specific column
get 'students', 'row1', {COLUMN => 'personal:name'}

# Count rows
count 'students'

# Delete a cell
delete 'students', 'row1', 'personal:age'

# Delete entire row
deleteall 'students', 'row2'

# Disable and drop table
disable 'students'
drop 'students'

# List all tables
list

# Describe table structure
describe 'students'
```

**What examiner checks:** `create`, `put`, `scan`, `get` working. Know the syntax: `'table', 'rowkey', 'cf:qualifier', 'value'`.

---

## Practical 4 — Pig Latin

**What it does:** Scripting language for data transformations on Hadoop (alternative to writing Java MapReduce).

```pig
-- Load a CSV file
data = LOAD '/user/student/input/employees.csv' USING PigStorage(',')
       AS (id:int, name:chararray, dept:chararray, salary:int);

-- Display
DUMP data;

-- Filter: employees with salary > 50000
high_sal = FILTER data BY salary > 50000;
DUMP high_sal;

-- Group by department
grouped = GROUP data BY dept;
DUMP grouped;

-- Count per department
dept_count = FOREACH grouped GENERATE group AS dept, COUNT(data) AS count;
DUMP dept_count;

-- Average salary per department
avg_sal = FOREACH grouped GENERATE group AS dept, AVG(data.salary) AS avg_salary;
DUMP avg_sal;

-- Order by salary descending
sorted = ORDER data BY salary DESC;
DUMP sorted;

-- Store output to HDFS
STORE dept_count INTO '/user/student/output/dept_count' USING PigStorage(',');
```

**Run with:** `pig -x local script.pig` (local mode) or `pig script.pig` (MapReduce mode).

**What examiner checks:** `LOAD`, `FILTER`, `GROUP BY`, `FOREACH GENERATE`, `DUMP` working.

---

## Practical 5 — Hive (SQL on Hadoop)

**What it does:** SQL-like queries on data stored in HDFS. Creates tables and runs analytics.

```sql
-- Start Hive
hive

-- Create database
CREATE DATABASE IF NOT EXISTS bda_lab;
USE bda_lab;

-- Create table
CREATE TABLE employees (
  id INT,
  name STRING,
  dept STRING,
  salary DOUBLE
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

-- Load data from HDFS
LOAD DATA INPATH '/user/student/input/employees.csv' INTO TABLE employees;

-- Basic queries
SELECT * FROM employees;
SELECT * FROM employees WHERE salary > 50000;
SELECT dept, COUNT(*) AS count FROM employees GROUP BY dept;
SELECT dept, AVG(salary) AS avg_sal FROM employees GROUP BY dept;
SELECT dept, MIN(salary), MAX(salary), SUM(salary) FROM employees GROUP BY dept;

-- Descriptive statistics
DESCRIBE employees;
DESCRIBE FORMATTED employees;

-- Create external table
CREATE EXTERNAL TABLE ext_emp (
  id INT, name STRING, salary DOUBLE
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/student/external_data/';
```

**What examiner checks:** Table creation, data loading, `SELECT` with `GROUP BY` and aggregate functions.

---

## Practical 6 — Bloom Filter in R

**What it does:** Implements a probabilistic data structure to check set membership. (Refer to your PT2 notes for the algorithm.)

```r
# === Bloom Filter Implementation in R ===

bloom_filter <- function(m, k) {
  bit_array <- rep(0, m)    # bit array of size m, all zeros

  # Hash functions (simple mod-based)
  hash_funcs <- list(
    function(x) (x %% m) + 1,
    function(x) ((2 * x + 3) %% m) + 1
  )
  # Use first k hash functions
  hash_funcs <- hash_funcs[1:k]

  # INSERT function
  insert <- function(element) {
    for (h in hash_funcs) {
      idx <- h(element)
      bit_array[idx] <<- 1     # <<- modifies outer scope
    }
    cat("Inserted:", element, "| Bit array:", bit_array, "\n")
  }

  # LOOKUP function
  lookup <- function(element) {
    for (h in hash_funcs) {
      idx <- h(element)
      if (bit_array[idx] == 0) {
        cat(element, "-> DEFINITELY NOT in set\n")
        return(FALSE)
      }
    }
    cat(element, "-> POSSIBLY in set (may be false positive)\n")
    return(TRUE)
  }

  list(insert = insert, lookup = lookup)
}

# --- Demo ---
bf <- bloom_filter(m = 10, k = 2)
bf$insert(15)
bf$insert(22)
bf$insert(38)
bf$lookup(35)   # False positive
bf$lookup(22)   # True positive
bf$lookup(99)   # Definitely not

# False positive probability
n <- 3; m <- 10; k <- 2
fp_prob <- (1 - exp(-k * n / m))^k
cat("False positive probability:", round(fp_prob, 4), "\n")
```

**Run with:** `Rscript bloom_filter.R` or paste into RStudio console.

**What examiner checks:** Insert elements, query elements, show a false positive case, and state the FP probability formula.

---

## Practical 7 — Flajolet-Martin Algorithm in R

**What it does:** Estimates the number of distinct elements in a data stream using trailing zeros of hash values.

```r
# === Flajolet-Martin Algorithm in R ===

fm_algorithm <- function(stream, hash_func) {
  max_trailing_zeros <- 0

  for (elem in stream) {
    h <- hash_func(elem)
    binary <- as.integer(intToBits(h))  # 32-bit binary
    # Count trailing zeros
    trailing <- 0
    for (bit in binary) {
      if (bit == 0) trailing <- trailing + 1
      else break
    }
    if (trailing > max_trailing_zeros) {
      max_trailing_zeros <- trailing
    }
    cat("Element:", elem, "| Hash:", h,
        "| Binary:", paste(rev(binary[1:8]), collapse=""),
        "| Trailing 0s:", trailing, "\n")
  }

  estimate <- 2^max_trailing_zeros
  cat("\nMax trailing zeros R =", max_trailing_zeros, "\n")
  cat("Estimated distinct elements = 2^R =", estimate, "\n")
  return(estimate)
}

# --- Demo ---
stream <- c(1, 3, 2, 1, 2, 3, 4, 3, 1, 2, 4)
hash_func <- function(x) (3 * x + 1)    # simple hash

cat("Actual distinct elements:", length(unique(stream)), "\n\n")
fm_algorithm(stream, hash_func)
```

**Run with:** `Rscript fm_algorithm.R`

**What examiner checks:** Hash computation, trailing zero counting, final estimate = $2^R$.

---

## Practical 8 — MongoDB Commands

**What it does:** NoSQL document-store CRUD operations and aggregation.

```javascript
// Start MongoDB shell
// mongosh    (or    mongo)

// Switch to / create database
use bda_lab

// INSERT
db.students.insertOne({name: "Ritik", age: 22, marks: 85, dept: "COMP"})
db.students.insertMany([
  {name: "Amit", age: 21, marks: 90, dept: "COMP"},
  {name: "Sara", age: 22, marks: 72, dept: "IT"},
  {name: "John", age: 23, marks: 65, dept: "COMP"},
  {name: "Priya", age: 21, marks: 88, dept: "IT"}
])

// READ (Find)
db.students.find()                           // all documents
db.students.find({dept: "COMP"})             // filter
db.students.find({marks: {$gt: 80}})         // marks > 80
db.students.find({}, {name: 1, marks: 1, _id: 0})  // projection
db.students.find().sort({marks: -1})         // sort descending
db.students.find().limit(3)                  // top 3

// UPDATE
db.students.updateOne({name: "Ritik"}, {$set: {marks: 95}})
db.students.updateMany({dept: "IT"}, {$inc: {marks: 5}})

// DELETE
db.students.deleteOne({name: "John"})
db.students.deleteMany({marks: {$lt: 70}})

// AGGREGATION PIPELINE
db.students.aggregate([
  {$match: {dept: "COMP"}},
  {$group: {_id: "$dept", avg_marks: {$avg: "$marks"}, count: {$sum: 1}}},
  {$sort: {avg_marks: -1}}
])

// Count
db.students.countDocuments({dept: "COMP"})

// Distinct values
db.students.distinct("dept")

// Drop collection
db.students.drop()
```

**What examiner checks:** `insertOne/Many`, `find` with filter, `updateOne`, `deleteOne`, aggregation pipeline with `$match` + `$group`.

---

## Practical 9 — R Programming (THE MAIN PRACTICAL)

This is the **most likely practical** to be asked. Every sample slip has (a) HDFS commands + (b) R programming.

### Template A: Create DataFrame, Summary, Extract, Filter

```r
# Create a data frame from vectors
emp_id <- c(1:5)
emp_name <- c("Rick", "Dan", "Michelle", "Ryan", "Gary")
start_date <- c("2012-01-01", "2013-09-23", "2014-11-15", "2014-05-11", "2015-03-27")
salary <- c(60000, 45000, 75000, 84000, 20000)

emp <- data.frame(emp_id, emp_name, start_date, salary)
print(emp)

# Structure and summary
str(emp)
summary(emp)

# Extract specific columns
emp[, c("emp_name", "salary")]
# OR
emp$emp_name
emp$salary

# Filter: salary <= 60000
emp[emp$salary <= 60000, ]
# OR using subset()
subset(emp, salary <= 60000)
```

### Template B: Sorting and Graphs

```r
# Sort a vector
v <- c(23, 45, 10, 34, 89, 20, 67, 99)

asc <- sort(v)             # ascending (default)
desc <- sort(v, decreasing = TRUE)   # descending

cat("Ascending:", asc, "\n")
cat("Descending:", desc, "\n")

# Bar plot of sorted values
barplot(asc, main = "Ascending Order", col = "steelblue",
        xlab = "Index", ylab = "Value")

barplot(desc, main = "Descending Order", col = "coral",
        xlab = "Index", ylab = "Value")
```

### Template C: Visualisation (Scatterplot, Bar, Histogram, Boxplot, Pie, Bubble, Dot)

```r
# Sample data
x <- c(1, 2, 3, 4, 5)
y <- c(10, 25, 15, 30, 20)
labels <- c("A", "B", "C", "D", "E")

# Scatterplot
plot(x, y, main = "Scatterplot", xlab = "X", ylab = "Y", pch = 19, col = "blue")

# Bar Chart
barplot(y, names.arg = labels, main = "Bar Chart", col = "steelblue")

# Histogram
hist(y, main = "Histogram", col = "lightgreen", xlab = "Values")

# Boxplot
boxplot(y, main = "Boxplot", col = "orange")

# Pie Chart
pie(y, labels = labels, main = "Pie Chart", col = rainbow(5))

# Dot Plot
dotchart(y, labels = labels, main = "Dot Chart", pch = 19)

# Bubble Chart (scatterplot with size)
symbols(x, y, circles = y/5, main = "Bubble Chart", bg = "lightblue",
        xlab = "X", ylab = "Y", inches = 0.5)
```

### Template D: Product Sales Data (Numeric Vectors + Visualisation)

```r
# Product sales data
bread     <- c(12, 3, 5, 11, 9)
milk      <- c(21, 27, 18, 20, 15)
cola      <- c(10, 1, 33, 6, 12)
choc_bars <- c(6, 7, 4, 13, 12)
detergent <- c(5, 8, 12, 20, 23)
days <- c("Mon", "Tue", "Wed", "Thu", "Fri")

# Create data frame
sales <- data.frame(days, bread, milk, cola, choc_bars, detergent)
print(sales)

# Bar chart for bread
barplot(bread, names.arg = days, main = "Bread Sales", col = "wheat")

# Grouped bar chart (all products)
sales_matrix <- as.matrix(sales[, -1])   # remove 'days' column
barplot(t(sales_matrix), beside = TRUE, names.arg = days,
        col = rainbow(5), main = "Product Sales by Day",
        legend = colnames(sales[, -1]))
```

### Template E: Subsetting with [] and subset()

```r
# Data frame
df <- data.frame(
  course  = c(1, 2, 3, 4, 5, 6),
  id      = c(11, 12, 13, 14, 15, 16),
  class   = c(1, 2, 1, 2, 1, 2),
  marks   = c(56, 75, 48, 69, 84, 53)
)

# Using [] brackets: course < 3
df[df$course < 3, ]

# Using subset(): course < 3 OR class == 2
subset(df, course < 3 | class == 2)
```

### Template F: Combine Datasets

```r
A <- c(6, 7, 8, 9)
B <- c(1, 2, 4, 5)

# c() combines vectors
C <- c(A, B)
cat("Combined C:", C, "\n")

# Combine data frames (rbind = rows, cbind = columns)
df1 <- data.frame(name = c("A", "B"), val = c(1, 2))
df2 <- data.frame(name = c("C", "D"), val = c(3, 4))
combined <- rbind(df1, df2)
print(combined)
```

### Template G: paste() for Concatenation

```r
# paste() concatenates text with separator (default space)
result <- paste("Ram has scored", 89, "marks", "in Mathematics")
cat(result, "\n")

# paste0() concatenates without separator
result2 <- paste0("Score:", 89)
cat(result2, "\n")
```

### Template H: Employee Salary (Enter data, Extend, Visualise)

```r
# Given data
sr <- 1:10
emp_name <- c("Vivek","Karan","James","Soham","Renu",
              "Farah","Hetal","Mary","Ganesh","Krish")
salary <- c(21000, 55000, 67000, 50000, 54000,
            40000, 30000, 70000, 20000, 15000)

emp <- data.frame(Sr_No = sr, Name = emp_name, Salary = salary)
print(emp)

# Add 5 more employees using rbind
new_emp <- data.frame(
  Sr_No = 11:15,
  Name = c("Ali", "Zara", "Dev", "Nina", "Raj"),
  Salary = c(25000, 60000, 45000, 35000, 55000)
)
emp <- rbind(emp, new_emp)
print(emp)

# Visualise
barplot(emp$Salary, names.arg = emp$Name, las = 2,
        main = "Employee Salaries", col = "steelblue",
        cex.names = 0.7)
```

### Template I: Analyse & Visualise with Built-in Datasets (IRIS / Loan / Churn / Supermarket)

```r
# IRIS Dataset (built-in)
data(iris)
str(iris)
summary(iris)

# Scatter plot
plot(iris$Sepal.Length, iris$Sepal.Width,
     col = iris$Species, pch = 19,
     main = "Iris: Sepal Length vs Width",
     xlab = "Sepal Length", ylab = "Sepal Width")
legend("topright", legend = levels(iris$Species),
       col = 1:3, pch = 19)

# Histogram
hist(iris$Sepal.Length, col = "lightblue",
     main = "Distribution of Sepal Length")

# Boxplot by species
boxplot(Sepal.Length ~ Species, data = iris,
        col = c("red", "green", "blue"),
        main = "Sepal Length by Species")

# For CSV datasets (Loan, Churn, Supermarket):
# df <- read.csv("loan_data.csv")
# str(df); summary(df)
# Then use the same plot/barplot/hist/boxplot functions above
```

---

# PART 2: Per-Experiment Viva Attack (Questions At Your Machine)

---

## HDFS Commands Viva

| Q | A |
|---|---|
| What is the default block size in HDFS? | 128 MB (was 64 MB in Hadoop 1.x). |
| What is the replication factor and why is it 3? | Each block is stored on 3 different DataNodes for fault tolerance. Even if 2 nodes fail, data survives. |
| Difference between `-put` and `-copyFromLocal`? | Functionally same — both copy local file to HDFS. `-put` also reads from stdin. |
| What happens if you `rm` a file in HDFS? | It goes to the HDFS Trash (`.Trash` directory) and is permanently deleted after a configurable interval. Use `-skipTrash` to delete immediately. |

## Sqoop Viva

| Q | A |
|---|---|
| What is `-m 1` in the Sqoop command? | It sets the number of mappers to 1. Without a primary key, Sqoop cannot split data across multiple mappers, so we use `-m 1`. |
| Difference between Sqoop import and export? | Import = RDBMS → HDFS. Export = HDFS → RDBMS. |
| What connector does Sqoop use? | JDBC. It connects to any RDBMS that provides a JDBC driver (MySQL, Oracle, PostgreSQL). |
| Can Sqoop import directly into Hive? | Yes, using `--hive-import` flag. |

## HBase Viva

| Q | A |
|---|---|
| What is a column family? | A group of columns stored together physically on disk. All columns in a family share the same storage settings. You define families at table creation time; columns are added dynamically. |
| Why do you need to `disable` before `drop`? | HBase requires the table to be taken offline (disabled) before deletion to prevent data corruption on active reads/writes. |
| Is HBase a row-store or column-store? | Column-family store. Data for each column family is stored together, enabling efficient reads of specific columns. |
| Where does HBase store data? | On HDFS. HBase is a NoSQL layer built on top of HDFS. |

## Pig Latin Viva

| Q | A |
|---|---|
| What is the difference between `DUMP` and `STORE`? | `DUMP` displays results on the console (stdout). `STORE` saves results to HDFS. |
| What is Pig's execution model? | Pig compiles Pig Latin scripts into MapReduce jobs that run on Hadoop. |
| What does `FOREACH GENERATE` do? | It applies transformations to each tuple in a relation — similar to `SELECT` in SQL. |
| What is the difference between Pig and Hive? | Pig uses a procedural scripting language (Pig Latin). Hive uses a declarative SQL-like language (HiveQL). Pig is preferred for ETL pipelines; Hive for ad-hoc queries. |

## Hive Viva

| Q | A |
|---|---|
| Difference between internal and external table? | Internal (managed): Hive owns the data; dropping the table deletes the data. External: Hive only has metadata; data stays on HDFS even after drop. |
| Where does Hive store metadata? | In the **Metastore** (usually a relational DB like Derby or MySQL). |
| Does Hive support real-time queries? | No. Hive is designed for batch processing. For real-time, use Impala or HBase. |
| What does `ROW FORMAT DELIMITED FIELDS TERMINATED BY ','` mean? | It tells Hive how to parse rows: each field in a row is separated by a comma (CSV format). |

## Bloom Filter (R) Viva

| Q | A |
|---|---|
| Can a Bloom Filter give false negatives? | **No.** If the filter says "not present," it is definitely not present. False negatives are impossible. |
| What causes false positives? | Hash collisions — different elements map to the same bit positions, making an absent element look present. |
| What is the formula for false positive probability? | $P = (1 - e^{-kn/m})^k$ where $n$ = elements inserted, $m$ = bit array size, $k$ = hash functions. |
| How to reduce false positives? | Increase $m$ (bit array size), optimise $k$ (optimal $k = (m/n) \ln 2$), or use better hash functions. |
| Can you delete from a Bloom Filter? | No. Setting a bit to 0 might affect other elements. Use a **Counting Bloom Filter** (replace bits with counters) for deletions. |

## Flajolet-Martin (R) Viva

| Q | A |
|---|---|
| What does FM algorithm estimate? | The number of **distinct elements** in a data stream. |
| Why trailing zeros? | The probability of a hash ending in $r$ zeros is $1/2^r$. So if we see $r$ trailing zeros, we likely hashed about $2^r$ distinct values. |
| Is the estimate exact? | No, it is an approximation. FM can over- or under-estimate. Using **multiple hash functions** and taking the **average** (or median of means) improves accuracy. |
| What is the space complexity? | $O(\log n)$ — we only need to store the max trailing zero count, not the actual elements. |

## MongoDB Viva

| Q | A |
|---|---|
| What is BSON? | Binary JSON. MongoDB stores documents in BSON format, which supports additional data types (Date, Binary, ObjectId) beyond regular JSON. |
| Difference between `find()` and `aggregate()`? | `find()` is simple querying/filtering. `aggregate()` is a multi-stage pipeline for complex operations like grouping, joining, and reshaping. |
| What is `_id` in MongoDB? | A unique identifier automatically added to every document. It is indexed by default and acts as the primary key. |
| Difference between `updateOne` and `replaceOne`? | `updateOne` modifies specific fields using `$set`. `replaceOne` replaces the entire document (except `_id`). |
| What is sharding? | Horizontal partitioning of data across multiple servers (shards) to handle large datasets and high throughput. |

## R Programming Viva

| Q | A |
|---|---|
| Difference between `c()` and `list()`? | `c()` creates a flat vector (all elements same type). `list()` creates a collection that can hold mixed types (numbers, strings, data frames). |
| What does `str()` do? | Displays the internal **structure** of an R object: data types, dimensions, and first few values. |
| Difference between `<-` and `=`? | Both assign values, but `<-` is the standard R assignment operator. `=` also works at top level but `<-` is preferred and works inside function calls. |
| What is a data frame? | A 2D table-like structure where each column can be a different data type. It is R's equivalent of a spreadsheet or SQL table. |
| Difference between `rbind` and `cbind`? | `rbind` combines by rows (stacking vertically). `cbind` combines by columns (side by side). |

---

# PART 3: Rapid-Fire Master Viva Question Bank (70+ Questions)

---

## Module 1: Big Data & Hadoop Fundamentals

| # | Question | Answer |
|---|----------|--------|
| 1 | What is Big Data? | Data too large, fast, or complex for traditional tools. Characterised by the 5 Vs: Volume, Velocity, Variety, Veracity, Value. |
| 2 | What are the 5 Vs? | Volume (size), Velocity (speed), Variety (types), Veracity (quality/trust), Value (usefulness). |
| 3 | What is Hadoop? | An open-source framework for distributed storage (HDFS) and processing (MapReduce) of big data on commodity hardware. |
| 4 | Name Hadoop's core components. | HDFS (storage), MapReduce (processing), YARN (resource management). |
| 5 | Name 5 Hadoop ecosystem tools. | Hive, Pig, HBase, Sqoop, Flume, ZooKeeper, Spark, Oozie. |
| 6 | Hadoop 1.x vs 2.x difference? | Hadoop 2.x introduced YARN (separating resource management from MapReduce), allowing other frameworks like Spark to run on Hadoop. |
| 7 | Limitations of Hadoop? | Not for real-time processing; small files problem; NameNode single point of failure; complex setup; weak native security. |

## Module 2: HDFS Architecture

| # | Question | Answer |
|---|----------|--------|
| 8 | What is NameNode? | Master server that stores metadata (filename, block locations, permissions) in RAM. Does NOT store actual data. |
| 9 | What is DataNode? | Worker/slave node that stores actual data blocks and serves read/write requests. Sends heartbeats to NameNode. |
| 10 | What is Secondary NameNode? | NOT a backup. It periodically merges EditLog with FSImage to prevent EditLog from growing too large (checkpointing). |
| 11 | Default block size? Why so large? | 128 MB. Large blocks reduce NameNode metadata overhead and minimise seek time for sequential reads. |
| 12 | Default replication factor? | 3. Blocks are stored on 3 different DataNodes for fault tolerance. |
| 13 | What is rack awareness? | HDFS places replicas on different racks so that data survives even if an entire rack fails. |
| 14 | What are heartbeats? | Periodic signals (every 3 seconds) from DataNode to NameNode. If missing for ~10 minutes, the node is declared dead. |
| 15 | What is SafeMode? | Startup state where NameNode loads FSImage and waits for DataNodes to report block availability. No writes allowed. |
| 16 | What is block report? | A DataNode periodically sends a list of all blocks it holds to the NameNode for verification. |
| 17 | What happens when a DataNode fails? | NameNode detects via missing heartbeats, finds under-replicated blocks, and instructs other DataNodes to re-replicate. |

## Module 2: MapReduce Internals

| # | Question | Answer |
|---|----------|--------|
| 18 | What are the 3 phases of MapReduce? | Map (transform input to key-value pairs), Shuffle & Sort (group by key), Reduce (aggregate values per key). |
| 19 | What is an InputSplit? | A logical chunk of data assigned to one mapper. Usually corresponds to one HDFS block but can differ. |
| 20 | InputSplit vs Block? | Block is a physical chunk on disk (128 MB). InputSplit is a logical division for processing; it may span multiple blocks. |
| 21 | What does RecordReader do? | Converts an InputSplit into individual key-value pairs that the Mapper can process (e.g., line number → line text). |
| 22 | Mapper signature in Java? | `map(LongWritable key, Text value, Context context)` — key is byte offset, value is the line of text. |
| 23 | Why `IntWritable` not `int`? | Hadoop needs serialisable types for network transfer. `IntWritable` implements Hadoop's `Writable` interface for efficient serialisation. |
| 24 | What is a Combiner? | A mini-reducer that runs on the mapper's output locally before shuffle. Reduces network traffic. Must be commutative and associative. |
| 25 | Combiner vs Reducer? | Combiner runs locally on each mapper node (optional optimisation). Reducer runs on the final grouped data across all mappers. |
| 26 | What is a Partitioner? | Determines which reducer receives a given key. Default: `hash(key) % numReducers`. |
| 27 | Where does Shuffle & Sort happen? | Between Map and Reduce. Map output is partitioned, sorted by key, and sent over the network to the correct Reducer. |
| 28 | What if Reducer count = 0? | Map output goes directly to HDFS with no aggregation. Useful when you only need the map transformation. |
| 29 | What is data locality in MapReduce? | The framework tries to run the mapper on the node where the input block is stored, avoiding network transfer. |
| 30 | What is speculative execution? | If a task is running slowly, Hadoop launches a duplicate on another node. Whichever finishes first wins. |

## YARN Architecture

| # | Question | Answer |
|---|----------|--------|
| 31 | What is YARN? | Yet Another Resource Negotiator — Hadoop's resource management layer (introduced in Hadoop 2.x). |
| 32 | Components of YARN? | ResourceManager (master), NodeManager (per-node agent), ApplicationMaster (per-job coordinator), Containers (resource units). |
| 33 | What does ResourceManager do? | Global resource allocation. Has a Scheduler (allocates containers) and ApplicationManager (manages job lifecycle). |
| 34 | What is a Container? | An allocated bundle of CPU + RAM on a NodeManager where a task runs. |
| 35 | What is ApplicationMaster? | A per-job process that negotiates containers from RM and coordinates task execution. Each job gets its own AM. |

## Module 3: NoSQL & MongoDB

| # | Question | Answer |
|---|----------|--------|
| 36 | What is NoSQL? | "Not Only SQL" — databases designed for large-scale, schema-flexible, distributed data. No fixed schema, horizontal scaling. |
| 37 | 4 types of NoSQL databases? | Key-Value (Redis), Document (MongoDB), Column-Family (HBase, Cassandra), Graph (Neo4j). |
| 38 | What is CAP Theorem? | A distributed system can guarantee at most 2 of 3: **Consistency** (all nodes same data), **Availability** (every request gets a response), **Partition tolerance** (works despite network splits). |
| 39 | ACID vs BASE? | ACID = Atomicity, Consistency, Isolation, Durability (RDBMS). BASE = Basically Available, Soft state, Eventual consistency (NoSQL). |
| 40 | MongoDB is CP or AP? | MongoDB is **CP** by default (strong consistency via primary reads; may sacrifice availability during elections). |
| 41 | What is sharding? | Splitting data across multiple servers by a shard key. Each shard holds a subset of data. Enables horizontal scaling. |
| 42 | Sharding vs Replication? | Sharding = splitting data across nodes (scalability). Replication = copying same data to multiple nodes (availability/fault tolerance). |
| 43 | What is BSON? | Binary JSON. MongoDB's storage format that adds types (Date, ObjectId, Binary) and is more efficient to parse than text JSON. |
| 44 | What is an aggregation pipeline? | A sequence of stages (`$match`, `$group`, `$project`, `$sort`, `$limit`) that process documents step by step. |
| 45 | `$match` vs `$group`? | `$match` filters documents (like `WHERE`). `$group` groups and aggregates (like `GROUP BY`). |
| 46 | What is a Replica Set? | A group of MongoDB instances maintaining the same data. One primary (writes) and multiple secondaries (reads/failover). |
| 47 | Master-slave vs peer-to-peer? | Master-slave: one node handles writes, slaves replicate (HBase, MongoDB). Peer-to-peer: all nodes equal, any can write (Cassandra). |

## Apache Spark

| # | Question | Answer |
|---|----------|--------|
| 48 | What is Spark? | A fast, in-memory distributed computing engine. 10-100x faster than MapReduce because it avoids disk I/O between stages. |
| 49 | RDD vs DataFrame vs Dataset? | RDD = low-level, unstructured, no optimisation. DataFrame = structured (like a table), optimised by Catalyst. Dataset = typed DataFrame (Java/Scala only). |
| 50 | What is lazy evaluation? | Transformations are not executed immediately. Spark builds a DAG (plan) and only computes when an **action** (like `collect()`, `count()`) is triggered. |
| 51 | Why is lazy evaluation useful? | Spark can optimise the entire pipeline before execution, eliminating unnecessary steps and minimising data shuffles. |
| 52 | Transformations vs Actions? | Transformations (map, filter, join) create a new RDD lazily. Actions (count, collect, save) trigger actual computation. |
| 53 | Narrow vs Wide dependency? | Narrow: each parent partition maps to at most one child (e.g., `map`, `filter`). Wide: requires shuffle across partitions (e.g., `groupByKey`, `join`). |
| 54 | What is a DAG? | Directed Acyclic Graph — Spark's execution plan showing the sequence of transformations and shuffle boundaries (stages). |
| 55 | Driver vs Executor? | Driver = master process that creates SparkContext and coordinates. Executors = worker processes on cluster nodes that run tasks. |
| 56 | What is SparkContext? | The entry point to Spark functionality. Creates RDDs, accumulators, broadcasts. In Spark 2+, use SparkSession. |
| 57 | Spark vs MapReduce? | Spark: in-memory, iterative, supports streaming/ML/SQL. MapReduce: disk-based, batch-only, writes intermediate results to HDFS. |

## Module 4: Data Stream Mining

| # | Question | Answer |
|---|----------|--------|
| 58 | What is a data stream? | An infinite, continuous, ordered sequence of data arriving in real-time. Cannot store all of it; must process on the fly. |
| 59 | What is a Bloom Filter? | A space-efficient probabilistic structure to test set membership. Can have false positives but never false negatives. |
| 60 | FP formula for Bloom Filter? | $P = (1 - e^{-kn/m})^k$ where $n$ = inserted elements, $m$ = bit array size, $k$ = hash functions. |
| 61 | What does FM algorithm do? | Estimates the number of **distinct elements** in a stream using trailing zeros of hash values. Estimate = $2^R$ where $R$ = max trailing zeros. |
| 62 | Why does FM use trailing zeros? | Probability of $r$ trailing zeros = $1/2^r$, so if we observe $r$ zeros, we likely saw ~$2^r$ distinct items. |
| 63 | How to improve FM accuracy? | Use multiple independent hash functions, compute $R$ for each, then take the **median of group averages**. |
| 64 | What is DGIM algorithm? | Counts the number of 1s in a sliding window of the last $N$ bits of a binary stream using logarithmic space. |
| 65 | DGIM bucket rules? | Buckets come in pairs. At most 2 buckets of each size. Sizes are powers of 2. When a third bucket of the same size appears, merge the two oldest into one bucket of double the size. |
| 66 | DGIM error bound? | At most 50% error. The count is between (exact / 2) and exact. Average error ~25%. |
| 67 | What is reservoir sampling? | Maintaining a random sample of $k$ items from a stream of unknown size. Item $i$ is kept with probability $k/i$. |

## Module 5: Recommendation Systems & Graph Analytics

| # | Question | Answer |
|---|----------|--------|
| 68 | Content-based vs Collaborative filtering? | Content-based: recommends items similar to what user liked before (item features). Collaborative: recommends based on what similar users liked (user behaviour). |
| 69 | User-based vs Item-based CF? | User-based: find users similar to you, recommend what they liked. Item-based: find items similar to ones you liked. |
| 70 | What is the cold-start problem? | New users have no history (user cold start) or new items have no ratings (item cold start), making recommendations difficult. |
| 71 | What is PageRank? | An algorithm that ranks web pages by importance. A page is important if many important pages link to it. Formula: $PR(A) = (1-d)/N + d \sum PR(T_i)/C(T_i)$. |
| 72 | What is the damping factor? | $d = 0.85$ typically. Probability that a random surfer follows a link vs jumping to a random page ($1-d$). |
| 73 | What is a spider trap? | A set of pages that link only to each other, causing PageRank to accumulate there. Fixed by the teleportation (damping) factor. |
| 74 | What is a dead end? | A page with no outgoing links. PageRank leaks out. Fixed by teleportation: redirect to any random page. |
| 75 | What is HITS? | Hyperlink-Induced Topic Search: every page gets a Hub score (links to good authorities) and Authority score (linked by good hubs). |
| 76 | Girvan-Newman algorithm? | Community detection. Repeatedly remove the edge with highest betweenness centrality until communities emerge. |

## Module 6: R Programming

| # | Question | Answer |
|---|----------|--------|
| 77 | What is R? | An open-source statistical programming language for data analysis, visualisation, and machine learning. |
| 78 | Difference between vector and list? | Vector: all elements same type. List: elements can be different types (number, string, data frame). |
| 79 | How to create a data frame? | `data.frame(col1 = c(...), col2 = c(...))`. Each column is a vector of equal length. |
| 80 | What does `summary()` do? | Gives descriptive statistics: min, max, median, mean, 1st and 3rd quartile for numeric columns; frequency for factors. |
| 81 | How to read a CSV file? | `df <- read.csv("file.csv")`. Optional: `header = TRUE`, `sep = ","`. |
| 82 | `subset()` vs `[]` indexing? | Both filter data. `subset(df, condition)` is cleaner syntax. `df[df$col > 5, ]` is base R bracket indexing. |
| 83 | What is `paste()` vs `paste0()`? | `paste()` concatenates with a separator (default space). `paste0()` concatenates with no separator. |
| 84 | How to install a package? | `install.packages("ggplot2")` then `library(ggplot2)`. |

---

# PART 4: Examiner Survival & Trap Prevention

---

## Top 5 Red Flags (Things That Get You Grilled Harder)

1. **"I don't know what this line does."** Never say this about your own code. Even if you copied it, know what every line does.
2. **Saying "Hadoop stores data in NameNode."** NameNode stores ONLY metadata. Data goes to DataNodes. This mistake instantly signals you don't understand HDFS.
3. **Confusing Hive with a database.** Hive is NOT a database. It is a data warehouse tool that provides SQL-like access to data stored on HDFS.
4. **Saying "MongoDB is a relational database."** MongoDB is a document-oriented NoSQL database. If you say relational, the viva is over.
5. **Not knowing what `$group` or `$match` does in your aggregation pipeline.** If you ran a pipeline on screen, be ready to explain every stage.

## "I Don't Know" Pivot Strategy

**Never say "I don't know" directly.** Instead:

1. **Bridge to what you know:** *"I'm not fully sure about [X], but it's related to [Y] which works like..."*
2. **Give a partial answer:** *"From what I understand, [concept] is used for [purpose], similar to how [thing you know] works..."*
3. **Redirect honestly:** *"I focused more on the [HDFS/Spark/R] part of the syllabus. In HDFS, for example..."*
4. **Use the code on your screen:** *"I can show you how it works in my code here..."* — pull the examiner's attention to your running practical.

**Safe pivot topics (you know these well from PT1 and PT2):**
- HDFS architecture (NameNode/DataNode/replication)
- MapReduce pipeline (Map → Shuffle → Reduce)
- Bloom Filter / FM algorithm (you have full numericals)
- R data frames and visualisation

## Last-Minute Checklist (Morning of the Exam)

- [ ] Can I type `hdfs dfs -ls /`, `-put`, `-cat`, `-mkdir`, `-rm` from memory?
- [ ] Can I write a 10-line R data frame script with `str()`, `summary()`, `subset()`?
- [ ] Can I write a barplot/histogram/boxplot in R?
- [ ] Can I write 5 MongoDB commands: `insertOne`, `find`, `updateOne`, `deleteOne`, `aggregate`?
- [ ] Can I explain Bloom Filter in 3 sentences?
- [ ] Can I explain FM algorithm in 3 sentences?
- [ ] Do I know what NameNode vs DataNode stores?
- [ ] Do I know MapReduce phases: Map → Shuffle & Sort → Reduce?
- [ ] Can I explain why Spark is faster than MapReduce? (In-memory processing)
- [ ] Do I know CAP theorem? (Pick 2 of 3: C, A, P)

**You're ready. Go get those marks.** 🎯
