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

![Data visualization](https://mtungle.github.io/images/LinearRegression1Variable/data.png)

## The model and cost function

Suppose we model the relationship between the population and the profit by a linear model as follows

![Hypothesis](https://latex.codecogs.com/gif.latex?h_%7B%5Ctheta%7D%28x%29%3D%5Ctheta%5ETx%3D%5Ctheta_0%20&plus;%20%5Ctheta_1x_1)

where `x_1` is the city population and `\theta` is our parameter. We would like to optimize the parameter `\theta_0` and `\theta_1` best fit the model line. It means minimizing the square distance between the actual profit and the hypothesis profit as stated in the following cost function.

![Cost function](https://latex.codecogs.com/gif.latex?J%28%5Ctheta%29%3D%5Cfrac%7B1%7D%7B2m%7D%5Csum_%7B1%7D%5E%7Bm%7D%28h_%5Ctheta%28x%5E%7B%28i%29%7D%29-y%5E%7B%28i%29%7D%29%5E2)

To solve the optimization problem, we may use either gradient descent algorithm or apply the closed form solution. First, we need to calculate the derivative of the cost function.

![Derivative cost function](https://latex.codecogs.com/gif.latex?%5Cfrac%7B%5Cpartial%20J%7D%7B%5Cpartial%20%5Ctheta_j%7D%3D%5Cfrac%7B1%7D%7Bm%7D%5Csum_%7Bi%3D1%7D%5Em%28%5Ctheta_0x_0%5E%7B%28i%29%7D%20&plus;%20%5Ctheta_1x_1%5E%7B%28i%29%7D%20-%20y%5E%7B%28i%29%7D%29x_j%5E%7B%28i%29%7D)

