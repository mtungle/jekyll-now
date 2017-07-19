This project shows how to use linear regression to predict the data trend in Matlab.

The data and business context are extracted from a bigger assignment from the Mahcine Learning course from Coursera at https://www.coursera.org/learn/machine-learning. 

Suppose we are running a food truck business which operates over multiple cities. We have some data about the profit of each food truck versus the city population. Then we would like to use those data to predict the profit of a new food truck in a new city. Based on the available data, it is assumed that there is a linear relationship between the city population and profit. We will use linear regression to quantify this relationship.

## The data and data visualization 

The data source is a csv file with 2 columns where the first column is the city population in 10000s and the second column is the profit in $10000s.

```
6.1101,17.592
5.5277,9.1302
8.5186,13.662
7.0032,11.854
5.8598,6.8233
8.3829,11.886
7.4764,4.3483
8.5781,12
6.4862,6.5987
```
Data scatter plot

```matlab
fprintf('Plotting Data ...\n')
data = load('ex1data1.txt');
X = data(:, 1); y = data(:, 2);
plot(x, y, 'rx', 'MarkerSize', 10); % Plot the data
ylabel('Profit in $10,000s'); % Set the y?axis label
xlabel('Population of City in 10,000s'); % Set the x?axis label
```

![Data visualization][logo1]

[logo1]: https://mtungle.github.io/images/LinearRegression1Variable/data.pnj
