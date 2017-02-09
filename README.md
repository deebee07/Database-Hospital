<b>Application Description</b>
<br>
Web Application monitors a patient who is under Intensive care unit of the hospital. It will store the readings of the specific equipments of the patient and would help the doctor to understand the behaviour. Also, in case of any emergency it will give an alert to the doctor in the form of email, if any reading is above certain threshold value. Equipments vary with the room type. Equipments are specific to the medical condition, patient is suffering with. As soon as a patient is admitted to an ICU (Intensive Care Unit), they would be admitted to a specific room with the equipments based on their illness. The room will have its own equipments, that record the readings at regular intervals. Patient has certain doctors assigned to them, doctors can remotely monitor their patients.

 <b>Databases</b>
 </br>
 In web application we have two types of databases i.e Operational database (MySQl) and Analytical Database (MySQL). We have used two health datasets in web application, one is XML based and other is JSON based stored in mongoDB. This represent the multiple sources of database. We used php, mysql and apache to make a web app that drives operational data interactions and mongoDb, xml, and json data for analytical interactions. Php pages include html forms for input, and  html tables for displaying tabular output data, such as search results of find a doctor module. All php pages use php’s PDO module for database interaction, make use of prepared sql statements and bound parameters to avoid sql injection. We also used database transactions to make sure that all operations function atomically. It helps in the way that if an error occurs in the middle of a series of sql statements, database wouldn’t be left in an inconsistent state.
 
<b>Data Sources</b>
<br>
We didn't get an actual dataset that fully satisfies our web application data requirements. So, we have used different datasets and obtained the relevant data related to our application. We extracted data related to treatment and description of treatment from Centers for Medicare & Medicaid Services https://www.cms.gov. We generated patient and employee details using data generation tool at mockaroo.com. These details include FirstName, LastName, Email, StreetAddress, Zipcode, City and State. We used the mysql procedure to cleanse the data and populate the tables. We also wrote  scripts to further cleanse the data. While, we obtained our XML and JSON data from Catalog.data.gov and Data.cms.gov.

<b>Data Synchronization - Operational Model to Analytical Model</b>
<br>
We have used events and procedures for converting our operational model to analytical model.
Events are used to run procedures which will populate analytical schema after a certain period of time. A sample procedure and Event are stated below 

<b>Event</b>
<br>
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

<b>Procedure</b>
<br>
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

<b>Tools Used</b>
<br>
```XAMPP
     MYSQL Server for creating the database and tables
     ERD Plus for creating the ER diagram, relational schema and star schema.
     Mockaroo.
     HighCharts.
     Cisco Information Server(CIS).
```
<b>Technologies Used</b>
<br>
```
HTML/CSS
PHP
SQL, MongoDB
Data Warehousing
XML, CSV
```

