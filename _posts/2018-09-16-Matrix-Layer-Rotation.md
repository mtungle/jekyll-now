# Problem Statement

You are given a 2D matrix of dimension *m x n* and a positive integer *r*. You have to rotate the matrix  times and print the resultant 
matrix. Rotation should be in anti-clockwise direction.

Rotation of a *4x5* matrix is represented by the following figure. Note that in one rotation, you have to shift elements by one step only.

![Matrix Ratation](https://hr-challenge-images.s3.amazonaws.com/2517/matrix-rotation.png)

It is guaranteed that the minimum of *m* and *n* will be even.

As an example rotate the Start matrix by 2:

```
Start         First           Second
 1 2 3 4        2  3  4  5      3  4  5  6
12 1 2 5  ->   1  2  3  6 ->   2  3  4  7
11 4 3 6      12  1  4  7       1  2  1  8
10 9 8 7      11 10  9  8     12 11 10  9
```

### Function Description

Complete the matrixRotation function in the editor below. It should print the resultant 2D integer array and return nothing.

matrixRotation has the following parameter(s):
* matrix: a 2D array of integers
* r: an integer that represents the rotation factor

### Input Format

The first line contains three space separated integers, *m*, *n*, and *r*, the number of rows and columns in the matrix, and the required rotation. 
The next *m* lines contain *n* space-separated integers representing the elements of a row of the matrix.

### Constraints

![Constraints](https://latex.codecogs.com/gif.latex?%5C%5C%202%5Cle%20m%2Cn%20%5Cle%20300%20%5C%5C%201%5Cle%20r%20%5Cle%2010%5E9%20%5C%5C%20min%28m%2Cn%29%5C%252%3D0%5C%5C%201%5Cle%20a_%7Bij%7D%20%5Cle10%5E8%5C%2C%20where%20%5C%2C%20i%20%5Cin%20%5B1%5Cldots%20m%5D%5C%2C%20and%20%5C%2C%20j%5Cin%5B1%5Cldots%20n%5D)
 
### Output Format

Print each row of the rotated matrix as space-separated integers on separate lines.

### Sample Input

### Sample Input #01
```
4 4 2
1 2 3 4
5 6 7 8
9 10 11 12
13 14 15 16
```
### Sample Output #01
```
3 4 8 12
2 11 10 16
1 7 6 15
5 9 13 14
```
### Explanation #01

The matrix is rotated through two rotations.
```
 1  2  3  4      2  3  4  8      3  4  8 12
 5  6  7  8      1  7 11 12      2 11 10 16
 9 10 11 12  ->  5  6 10 16  ->  1  7  6 15
13 14 15 16      9 13 14 15      5  9 13 14
```
### Sample Input #02
```
5 4 7
1 2 3 4
7 8 9 10
13 14 15 16
19 20 21 22
25 26 27 28
```
### Sample Output #02
```
28 27 26 25
22 9 15 19
16 8 21 13
10 14 20 7
4 3 2 1
```
### Explanation 02

The various states through 7 rotations:
```
1  2  3  4      2  3  4 10    3  4 10 16    4 10 16 22
7  8  9 10      1  9 15 16    2 15 21 22    3 21 20 28
13 14 15 16 ->  7  8 21 22 -> 1  9 20 28 -> 2 15 14 27 ->
19 20 21 22    13 14 20 28    7  8 14 27    1  9  8 26
25 26 27 28    19 25 26 27    13 19 25 26   7 13 19 25

10 16 22 28    16 22 28 27    22 28 27 26    28 27 26 25
 4 20 14 27    10 14  8 26    16  8  9 25    22  9 15 19
 3 21  8 26 ->  4 20  9 25 -> 10 14 15 19 -> 16  8 21 13
 2 15  9 25     3 21 15 19     4 20 21 13    10 14 20  7
 1  7 13 19     2  1  7 13     3  2  1  7     4  3  2  1
```
### Sample Input #03
```
2 2 3
1 1
1 1
```
### Sample Output #03
```
1 1
1 1
```
### Explanation #03

All of the elements are the same, so any rotation will repeat the same matrix.

# Solution
```Python
#!/bin/python3

import math
import os
import random
import re
import sys

# Complete the matrixRotation function below.
def matrixRotation(matrix):
    return

#def pill(matrix): return 1D array of the matrix in spiral form
#Args:
#    matrix: 2D matrix
#Returns:
#    1D array as result of pilling the matrux in spiral form
def pill(matrix):
    layers=[]
    m=len(matrix)
    n=len(matrix[0])
    
    numLayers=math.ceil(min(m,n)/2)
    for i in range(numLayers):
        layers.append([])
    
    for i in range(numLayers):
        startRow=i
        startCol=i
        sizeRow=m-i*2
        sizeCol=n-i*2        
    
        for col in range(startCol,startCol+sizeCol-1):
            layers[i].append(matrix[startRow][col])
        for row in range(startRow,startRow+sizeRow-1):
            layers[i].append(matrix[row][startCol+sizeCol-1])
        for col in range(startCol+sizeCol-1,startCol,-1):
            layers[i].append(matrix[startRow+sizeRow-1][col])
        for row in range(startRow+sizeRow-1,startRow,-1):
            layers[i].append(matrix[row][startCol])

    return layers

#def reconstruct(layers,numRows,numCols): restore the original matrix
#Args:
#    layers: 1D input array
#    numRows: number of rows of the original matrix
#    numCols: number of cols of the original matrix
#Returns:
#    2D original matrix
def reconstruct(layers,numRows,numCols):
    #numRows=len(layers)
    #numCol=len(layer)
    matrix=[[0 for col in range(numCols)] for row in range(numRows)]
    
    for i in range(len(layers)):
        
        startRow=i
        startCol=i
        sizeRow=numRows-i*2
        sizeCol=numCols-i*2
        index=0
        
    
        for col in range(startCol,startCol+sizeCol-1):
     
            matrix[startRow][col]=layers[i][index]
            index+=1
            
        for row in range(startRow,startRow+sizeRow-1):            
            matrix[row][startCol+sizeCol-1]=layers[i][index]
            index+=1
            
        for col in range(startCol+sizeCol-1,startCol,-1):

            matrix[startRow+sizeRow-1][col]=layers[i][index]
            index+=1
        for row in range(startRow+sizeRow-1,startRow,-1):

            matrix[row][startCol]=layers[i][index]
            index+=1
    for row in range(numRows):
        for col in range(numCols):
            print(matrix[row][col],end=' ')
        print('')

#def rotate(layers,r): rotate 1D array instead of rotating 2D matrix
#Args:
#    layers: 1D input array
#    r: rotate factor
#Returns:
#    The 1D array after rotation        
def rotate(layers,r):
    numLayers=len(layers)
    newLayers=[[0 for col in range(len(layers[row]))] for row in range(numLayers)]
    
    for row in range(numLayers):
        for col in range(len(layers[row])):
            newLayers[row][col-(r%len(layers[row]))]=layers[row][col]
    return newLayers

if __name__ == '__main__':
    mnr = input().split()

    m = int(mnr[0])

    n = int(mnr[1])

    r = int(mnr[2])

    matrix = []

    for _ in range(m):
        matrix.append(list(map(int, input().rstrip().split())))
    layers=pill(matrix)
    newLayers=rotate(layers,r)
    reconstruct(newLayers,m,n)
    #matrixRotation(matrix)
```

