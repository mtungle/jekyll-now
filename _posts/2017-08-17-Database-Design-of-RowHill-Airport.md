---
layout: post
title: Database Design of RowHill Airport
description: Database design
tags: SQL
---


## Business Description
Computer Sciences Department frequent fliers have been complaining to RowHill Airport officials about the poor
organization at the airport. As a result, the officials decided that all information related to the airport should be
organized using a DBMS, and you have been hired to design the database. Your first task is to organize the
information about all the airplanes stationed and maintained at the airport. The relevant information is as
follows:
* Every airplane has a registration number, and each airplane is of a specific model.
* The airport accommodates a number of airplane models, and each model is identified by a model
number (e.g., DC-10) and has a capacity and a weight.
* A number of technicians work at the airport. You need to store the name, SSN, address, phone number,
and salary of each technician.
* Each technician is an expert on one or more plane model(s).
* Traffic controllers must have an annual medical examination. For each traffic controller, you must store
the date of the most recent exam.
* All airport employees (including technicians and traffic controllers) belong to a union. You must store
the union membership number of each employee. You can assume that each employee is uniquely
identified by a social security number.
* The airport has a number of tests that are used periodically to ensure that airplanes are still airworthy.
Each test has a Federal Aviation Administration (FAA) test number, a name, and a maximum possible
score.
* The FAA requires the airport to keep track of each time that a given airplane is tested by a given
technician using a given test. For each testing event, the information needed is the date, the number of
hours the technician spent doing the test, and the score that the air plane received on the test.

## Entity Relational Diagram

![Diagram](https://mtungle.github.io/images/Database-Design-of-RowHill-Airport/p1.png)

## Relational Schemas
```
Airplane(Registration number, Model number)
PK: Registration number
FK: Model number refers to table Model

Model(Model number, capacity, weight)
PK: Model number

Employee(SSN, union membership)
PK: SSN

Traffic controller(SSN, exam date)
PK: SSN
FK: SSN refers to table Employee

Technician(SSN, name, address, phone, salary)
PK:SSN
FK:SSN refers to table Employee

Expertise(SSN, model number)
PK: SSN, model number
FK: SSN refers to table Employee
FK: model number refers to table Model

Test(FFA, name, maximum score)
PK:FFA

Test event(FFA, Registration number, SSN, date, hours, score)
PK: FFA, Registration number, SSN
FK: FFA refers to table Test
FK: Registration number refers to table Airplane
FK: SSN refers to table Employee
```

## Example Queries

```SQL
SELECT unique SSN
FROM Technician, Traffic controller
WHERE Technician.SSN=Traffic controller.SSN
List the social security number of the employees that is both technician and traffic controller.

SELECT registration number
FROM Air plane, Model
WHERE Airplane.modelnumber=Model.modelnumber
AND Model.capacity>500
List all the air plane registration numbers which can carry more than 500 passengers.

SELECT unique registration number
FROM Testevent, Test
Where Testevent.FAA=Test.FAA
AND Testevent.score=Test.Maximumscore
List all of air plane registration numbers that achieve a maximum score in at least one test.

SELECT name, salary
FROM Technician
WHERE salary>90000
List all name and salary of technicians who have salary greater than 90000.

SELECT name
FROM Technician, Testevent
WHERE Testevent.SSN=Technician.SSN
GROUP BY Testevent.SSN
HAVING count(*)>1
List all the name of technicians who have performed more than 1 test event.
```

