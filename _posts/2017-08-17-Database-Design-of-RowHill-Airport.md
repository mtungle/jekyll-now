This is my solution of an university assignment.

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

![Diagram](https://mtungle.github.io/images/LinearRegression1Variable/data.png)


Data scatter plot

```matlab
fprintf('Plotting Data ...\n')
data = load('ex1data1.txt');
X = data(:, 1); y = data(:, 2);
plot(x, y, 'rx', 'MarkerSize', 10); % Plot the data
ylabel('Profit in $10,000s'); % Set the y?axis label
xlabel('Population of City in 10,000s'); % Set the x?axis label
```

![Data visualization](https://mtungle.github.io/images/LinearRegression1Variable/data.png)

## The model and cost function
