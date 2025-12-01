📘 BFHL Qualifier – SQL + Spring Boot Automation

This project is an automated solution for the BFHL Placement Qualifier, which requires candidates to:

Generate a webhook using the BFHL API

Submit a SQL query to the generated webhook

Verify that the webhook processes the query successfully

This repository contains a minimal Spring Boot application that performs the entire workflow automatically on startup.

🚀 Features
✔ Automatically calls BFHL Generate Webhook API

The app sends your registration number to:

https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/JAVA


and receives:

webhook URL

accessToken

✔ Submits final SQL query to the webhook

On startup, the app automatically formats and sends your SQL using:

Raw token header

Automatic retry with "Bearer " prefix (if required)

✔ SQL query fully implemented

The SQL solves the problem provided in the assignment:

Calculates average age of employees per department

Lists up to 10 employees sorted by ID

Filters employees whose average payment > 70,000

Orders departments in descending order of ID

🧠 Final SQL Query Used
SELECT
  d.department_name AS DEPARTMENT_NAME,
  ROUND(AVG(TIMESTAMPDIFF(YEAR, e.dob, CURDATE())), 2) AS AVERAGE_AGE,
  SUBSTRING_INDEX(
    GROUP_CONCAT(CONCAT(e.first_name, ' ', e.last_name) ORDER BY e.emp_id SEPARATOR ', '),
    ', ',
    10
  ) AS EMPLOYEE_LIST
FROM department d
JOIN employee e ON e.department = d.department_id
JOIN (
  SELECT emp_id
  FROM payments
  GROUP BY emp_id
  HAVING AVG(amount) > 70000
) high_pay ON high_pay.emp_id = e.emp_id
GROUP BY d.department_id, d.department_name
ORDER BY d.department_id DESC;

📁 Project Structure
src/
 └── main/
     └── java/com/example/bfhlqualifier/
         ├── BfhlQualifierApplication.java
         └── StartupRunner.java  <-- all automation code here
     └── resources/application.properties
pom.xml

🔧 How It Works
1️⃣ On startup, Spring Boot runs StartupRunner

This triggers the webhook flow automatically:

Step 1: Call generateWebhook

Step 2: Extract webhook + token

Step 3: Send SQL with appropriate Authorization header

2️⃣ Prints results to console

Example:

StartupRunner: starting webhook flow...
generateWebhook HTTP status: 200 OK
Submission response status: 200 OK
Submission response body: {"success":true,"message":"Webhook processed successfully"}

▶️ Running the Project
Build:
mvn -DskipTests clean package

Run:
mvn spring-boot:run

📌 Requirements

Java 17+

Maven

Internet connection to access BFHL API endpoints

🏁 Final Output

You will see a final message:

{"success":true,"message":"Webhook processed successfully"}


This confirms your SQL solution was accepted by the BFHL evaluation system.
