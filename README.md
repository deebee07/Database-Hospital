

1.        Application Description
Our Web Application monitor a patient who is under Intensive care unit of the hospital. It will store the readings of the specific equipments of the patient and would help the doctor to understand the behaviour. Also, in case of any emergency it will give an alert to the doctor in the form of email, if any reading is above certain threshold value. Equipments vary with the room type. Equipments are specific to the medical condition, patient is suffering with. As soon as a patient is admitted to an ICU (Intensive Care Unit), they would be admitted to a specific room with the equipments based on their illness. The room will have its own equipments, that record the readings at regular intervals. Patient has certain doctors assigned to them, doctors can remotely monitor their patients.

Our Patient care and monitoring Application manage data related to patients, hospital employees, treatment, bills, and appointments. It has the data model of a hospital website where a patient and employee make an account through register page and view their details. Here, patient can view his treatment, assigned doctor, bill payment, doctors available and his health detail. Similarly, employee can view and edit his detail, can also search the details of other doctors informed by patients, information of patients being treated by him.     
 
Each patient and employee registers to create an account, which has 1 email, and password. All the basic details of a user like First Name, Last Name, Address, City, and Zip is stored in the database. 
 
When the user submits the login form, we query the patient and employee table based on user type to retrieve their record, so we can verify that their password matches the hashed value stored in database and related page appear dynamically.

In Employee login, the employee can search the patients attended by him. He can look up their patient details. On show button click, an employee will also retrieve the patient admit date, discharge date, treatment type and number of days. Here, the join query also present the patients whose reading values are higher than threshold value. If the value is greater than threshold value than an email alert will be send to main doctor.

Moreover, After login, the employee and patient can search the doctors based on Department, First name, Last name, Zip Code, City, State. When an employee is signed in, he will enter the patient id whom he attended. On submitting the id, a query will run that will update the billing cost as “dues” in patient table by adding that particular doctor cost to pending dues. This module will also inform about the employee details who attended that patient. The billing module is available after patient login.

The total bill of the patient can be calculated from a join query that will run and give the billing cost of patient based on room charges, treatment charges, visit charges and doctor charges. 
 
2.        Functional requirements
All the functional requirements laid out for the database are met.
●      Each patient needs to register first to avail the hospital services. Details are stored in PATIENT table.
●      PATIENT table has certain attributes such as Name, Admit Date, Discharge Date, Address, Age and Sex.
●     	Address of person has Street_Address, Zip_code, Apartment_no, Street, State, City and Country.
●   Each employee of the hospital needs to be registered. This data is stored in EMPLOYEE table and each has its unique Employee_ID.
●      	EMPLOYEE table is categorized into STAFF and DOCTOR table.
●      	STAFF table has Type attribute which specifies the Staff Type and DOCTOR table has specialization attribute that specifies the specialization of the doctor.
●      Each equipment is registered and is stored in EQUIPMENT table and a trigger can be set to send notification to the doctor & staff if the value it records is above the threshold value.
●      EQUIPMENT is a weak entity as it depends on the ROOM entity because it is the room that determines the equipment it has.
●      	Each PATIENT can be treated by many doctors.
●      	DOCTORS can also treat many patients.
●      	RECORD should store machine readings sent by equipments.
●      Each equipment should be hierarchically categorized based on rooms and further on department type.

3.        Non Functional Requirements
Some of the non-functional requirements are implemented. For others the implementation plan or proposal is mentioned below:
●      Database It should have security mechanisms to avoid any malicious queries, e.g. SQL injection, blind SQL injection etc that are implemented by using prepared statements with bound parameters.
●      Database must have permissions at read,write,create and should be clearly set on individual. There must be different table for patient and employee.
●       Database must have facility to fix the inconsistency manually.
●   	A backup table of RECORD table has to be created to have an archive of the old data, so as to have good access time to the database.
●      There must be consistency across replicas and can be fixed manually.
4.        Application
In our web application we have two types of databases i.e Operational database (MySQl) and Analytical Database (MySQL). We have used two health datasets in our web application, one is XML based and other is JSON based stored in mongoDB. This represent the multiple sources of database. We used php, mysql and apache to make a web app that drives our operational data interactions and mongoDb, xml, and json data for analytical interactions. Our php pages include html forms for input, and  html tables for displaying tabular output data, such as search results of find a doctor module. Our all php pages use php’s PDO module for database interaction, make use of prepared sql statements and bound parameters to avoid sql injection. We also used database transactions to make sure that our all operations function atomically. It helps in the way that if an error occurs in the middle of a series of sql statements, our database wouldn’t be left in an inconsistent state.

5.        Configuration
In the project we used MongoDB and XML as part of analytics that was performed on the data sets. We had various JSON datasets that were stored in the database. We had a data-set of XML format that was also analyzed. The configuration for that was basically using the PDO to form a connection and perform queries on it. Data-sets have been taken from various sources such as the catalog.data.gov and data.cms.gov.

In our project, we have a number of modules that do database interaction using PHP scripts. Our application make use of reusable code to establish a PDO database connection. the script will throw an error if the connection was unsuccessful. 

6.        Data Sources
We didn't get an actual dataset that fully satisfies our web application data requirements. So, we have used different datasets and obtained the relevant data related to our application. We extracted data related to treatment and description of treatment from Centers for Medicare & Medicaid Services https://www.cms.gov. We generated patient and employee details using data generation tool at mockaroo.com. These details include FirstName, LastName, Email, StreetAddress, Zipcode, City and State. We used the mysql procedure to cleanse the data and populate the tables. We also wrote  scripts to further cleanse the data. While, we obtained our XML and JSON data from Catalog.data.gov and Data.cms.gov.
6.1          Patient Data
Patient’s FirstName, LastName, Email, StreetAddress, Zipcode, City , State, patient_id, admit_date, discharge_date  were generated using the tool at mockaroo.com.
6.2          Employee Data
Employee’s FirstName, LastName, Email, StreetAddress, Zipcode, City , State, patient_id were generated using the tool at mockaroo.com.

6.3          Billing & Alerts
Billing related information were calculated at time of need. Billing information was calculated using room patient admit date and discharge, room charges ,treatment charges, doctor visits charges.
Data in notification table was inserted only when reading of a patient exceeded a certain threshold value. This insertion is caused by a Trigger on Record table. It also sends email notification to main doctor of the concerned patient.
 
7.        Data Synchronization - Operational Model to Analytical Model
We have used events and procedures for converting our operational model to analytical model.
Events are used to run procedures which will populate analytical schema after a certain period of time.  A sample procedure and Event are stated below 

Event

DELIMITER |
CREATE EVENT myevent
    ON SCHEDULE EVERY 1 WEEK STARTS '2016-11-30 03:00:00'
    DO
      BEGIN
        CALL populate_analyticalTables();
        CALL populate_recordFactTable();
        CALL populate_treatmentFactTable();
        CALL populate_stayFactTable();
             END |

 Procedure

DELIMITER ;
BEGIN
DECLARE done INT DEFAULT FALSE;
DECLARE pid, eid, charge,tid INT(11);
DECLARE nresult CURSOR FOR 
SELECT v.Patient_ID , v.Employee_ID ,t.Time_ID, v.Visit_charge FROM  finalproject1.Time t, finalproject1.Visit1 v WHERE t.year = Year(v.time_stamp) and t.month = Month(v.time_stamp) and t.Day_of_year = DayofMonth(v.time_stamp) ;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
OPEN nresult;
read_loop: LOOP
FETCH nresult INTO pid, eid , tid, charge ;
IF done THEN
LEAVE read_loop;
END IF;
insert into finalproject1.treatment_fact  Values ( eid, pid , tid, charge);
 END LOOP;
CLOSE nresult;
END
 
8.        Tools Used
●      XAMPP
●      MYSQL Server for creating the database and tables
●      ERD Plus for creating the ER diagram, relational schema and star schema.
●      Mockaroo.
●      HighCharts.
●      Cisco Information Server(CIS).
9.         Technologies Used
●      HTML
●      CSS
●      PHP
●      SQL
●      Data Warehousing
●      XML
●      CSV

 
11.    Data Models Overview
11.1      Operational Data Model
Below are the tables in 3NF in the Operational data model:
 
●      Patient- Maintains details of patient
●     Employee- Maintains details of employees viz. Staff, doct
●      Login- Maintains login details
●     Record-  Contains health related parameters and values. This is linked to patient.
●      Room- Maintains details of room and room charge
●      Visit- Maintains visits of visiting doctor to patients along with time stamp
●      Treatment - Maintains of treatments and their cost
 
11.2      Analytical Data Model 

11.2.1  Dimension Tables:
Time_Dimension- Maintains the time IDs to be used for analysis
 Patient_Dimension - Contains information of all past and present patients of the hospital
 Employee_Dimension- Contains information of all past and present employees of the hospital
 Record_Dimension- Contains health records of all past and present patients, used for analyzing based on  number of alerts generated 
 Room_Dimension- Contains information of all the rooms in the hospital and stay charges of each room
Treatment_Dimension- Contains information regarding various treatments offered, their description and charge 


11.2.2  Fact Tables:
Cost_Fact:  The fact table tracks the treatment of  patients by their main doctors.
Stay_Fact: This fact table tracks the patients and details about their stay in the hospital.
Visit_Fact: This fact table tracks visiting doctors and patient treated by them. 
Record_Fact: The record fact table generates the health facts of patients based on number of alerts received, patient details.
 
12.	ER Diagram
 
Fig. 1 ER Diagram of Hospital Management And Alert System 
13.	Relational Schema
 
Fig. 2 Relational Schema of Database 

14.	Star Schemas
 
 
Fig 3. Star Schema of Database
15.    Operational Queries:
We have performed various OLTP operations such as SELECT, INSERT, and UPDATE with our highly normalized tables. Most of these operations are driven by our html forms in our php powered web pages, which we’ll detail next.
 
15.1      Web Page Forms

Our web application provides login and registration form for patient and employee to access. When a user signs up he submits the registration form and data will be store in patient and employee table based on user type. However, our application has the login form. When a user submits the login form, the query will work on patient or employee table based on username after verifying the password. The user name is stored in a session variable which is utilized to maintain the sessions in the application. The passwords are stored as a hash in the database in order to provide greater security.  
 
 
15.1.1  	User Registration: 
New user Sign Up by entering all the user details, which gets inserted into different related tables Login, Patient, and Employee based on user type. So, on submission, a new row will be inserted into tables but a validation error will appear if the matching record already exists within the tables, for example, maybe the detail of patient already exists, in which case we just reuse the existing id that corresponds to the existing patient.  
Fig. 4 Registration And Login Form 
15.1.2  	Find a Doctor:
The search form is used to determine the details of particular doctor or all the doctors within that area. Here, the query will provide the details of doctor based on Firstname, or lastname or location or department or can be using all the fields for a particular doctor.
Fig. 5 Form to find the details of Doctors

15.2.1  	Search and display all patients of a doctor:
In Employee login, the employee can search the patients attended by him. He can look up their patient details

 Fig. 6 Patients Record

15.2.2 	Search Employee based on department, employee geo- location:
   After login, the employee and patient can search the doctors based on Department, First name, Last name, Zip Code, City, State

Fig. 7 Search Doctor Details
15.2.3 	Calculate total bill of a patient based:
Here a join query will run that will give the billing cost of patient based on room charges, treatment charges, visit charges and doctor charges


Fig. 8 Billing

15.2.4 Send email notification and update notification table
Send email alert to main doctor and update the notification table if certain reading is greater than a threshold value.

Fig. 9 Email Alert of a Patient 

15.2.5 Update billing information when a doctor visits a patient -
  When an employee is signed in, he will enter the patient id whom he attended. On submitting the id, a query will run that will update the billing cost as “dues” in patient table by adding that particular doctor cost to pending dues

Fig. 10 Bill updation
16.    Analytical and OLAP Queries
Analytical Queries are performed on the MongoDB and XML datasets and various OLAP queries are performed on it. They are listed as follows:

16.1  	Drill Up/Down:
Query 1: This query analyzes the revenue generated from the types of rooms present in the hospital.

SELECT td.month,r.Room_Type, s.Room_charge*s.Ndays FROM patient_dimension p , employee_dimension e , time_dimension td, room_dimension r, stay_fact s WHERE s.Patient_ID = p.Patient_ID and s.Employee_ID = e.Employee_ID and s.Time_ID = td.Time_ID and s.Room_ID = r.Room_ID and r.Room_Type = "General" and td.year = 2015 GROUP by td.month

Query 2: This query analyzes the revenue generated from the types of rooms present in the hospital.

SELECT td.day,count(r.Room_ID), s.Room_charge*s.Ndays FROM patient_dimension p , employee_dimension e , time_dimension td, room_dimension r, stay_fact s WHERE s.Patient_ID = p.Patient_ID and s.Employee_ID = e.Employee_ID and s.Time_ID = td.Time_ID and s.Room_ID = r.Room_ID and and td.year = 2015 GROUP by td.day

 

Fig 11. Rooms Trend in Hospital
 
 
16.2  	Slice/Dice

Query 1: This query slices the database based on the gender which is either Male or Female and then dices them based on the diseases they have, this helps in analyzing the trend of that disease.

SELECT p.sex,rd.Reading_Type, sum(rf.No_of_alerts) as "Number of Alerts" FROM patient_dimension p, time_dimension td, record_dimension rd, record_fact rf WHERE p.Patient_ID = rf.Patient_ID AND rd.Record_ID = rf.Record_ID AND p.Sex = 'f'  GROUP BY rd.Reading_Type

Query 2: This query slices the database based on the amount of alerts sent to doctors based on the diseases that the patients had and were analyzes as part of OLAP queries.

SELECT rd.Reading_Type, td.month, COUNT(rf.No_of_alerts) as Alert_Count FROM patient_dimension p, time_dimension td, record_dimension rd, record_fact rf WHERE p.Patient_ID = rf.Patient_ID AND rd.Record_ID = rf.Record_ID and rd.Reading_Type = 'Heart rate' and td.year = 2015 GROUP BY td.month


Fig. 12 Trend of Patient Alerts



16.3 Roll Up
Query 1: This query rolls up the doctor's salary, the amount they individually have earned by their service in the hospital.
SELECT  e.FName As Doctor, sum(s.Visit_Charge) as Visit_Fees FROM patient_dimension p , employee_dimension e , time_dimension td, visit_fact1 s WHERE s.Patient_ID = p.Patient_ID and s.Employee_ID = e.Employee_ID and s.Time_ID = td.Time_ID GROUP BY  e.FName

Query 2: This query analysis the trend of the expense done by patients in this hospital based on their stay _charge and visit charge.
SELECT p.FName, p.LName, s.Room_charge*s.Ndays, s.Ndays*e.Visit_Charge FROM patient_dimension p , employee_dimension e , time_dimension td, room_dimension r, stay_fact s WHERE s.Patient_ID = p.Patient_ID and s.Employee_ID = e.Employee_ID and s.Time_ID = td.Time_ID ands.Room_ID = r.Room_ID GROUP BY  p.FName, p.LName

SELECT p.FName, p.LName, sum(s.Visit_Charge) as Visit_Fees FROM patient_dimension p , employee_dimension e , time_dimension td, visit_fact1 s WHERE s.Patient_ID = p.Patient_ID and s.Employee_ID = e.Employee_ID and s.Time_ID = td.Time_ID GROUP BY  p.FName, p.LName




 
Fig. 13 Trend of Patients Visit Cost	
17.    Data Virtualization using CIS(Cisco Information Server)
Following are the screenshots of our database schema on remote machine. CIS helps in data modeling and provides a feature rich development environment. We have also upload xml files and using cis we are able to visualize the xml files.
	

Fig. 14 MySQL Database at Cisco Information Server
18.    Enhancements Planned
 
Create automatic backup feature which will replicate the database after certain period of time to cloud. 
Data archiving of old records and create analytical surveys to derive insights and  patterns from the data archive.
 Create caching mechanism to increase the performance of the database.
 Create feedback, notes and chat section for doctors. This will help them in sharing their insights about the analytics to other doctors.
