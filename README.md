# Small-gotcha-when-using-negative-indexing

Negative indexing is a commonly used method in R to drop elements from a vector or rows/columns from a matrix that the user does not want. For example, the code below drops the third column from the matrix M:

M <- matrix(1:9, nrow = 3)
M
#      [,1] [,2] [,3]
# [1,]    1    4    7
# [2,]    2    5    8
# [3,]    3    6    9

M[, -3]
#      [,1] [,2]
# [1,]    1    4
# [2,]    2    5
# [3,]    3    6

Now, let’s say we want to write a function with signature dropColumns(x, exclude) that takes in a matrix x and a vector of indices exclude, and returns a matrix without the columns listed in exclude. (The result in the code snippet above could be achieved with the call dropColumns(M, 3)). A natural attempt (without checking the indices are within bounds) would be the following:

dropColumns <- function(x, exclude) {
  x[, -exclude, drop = FALSE]
}

Let’s try it out:

dropColumns(M, c(1, 2))
#      [,1]
# [1,]    7
# [2,]    8
# [3,]    9

What happens if exclude is the empty vector (i.e. we don’t want to drop any columns)? The code below shows two different ways of doing this, and BOTH of them don’t give the expected result!

dropColumns(M, c())
# Error in -exclude : invalid argument to unary operator

dropColumns(M, integer(0))
# [1,]
# [2,]
# [3,]

Moral of the story: When doing negative indexing with variables, you need to check for whether the variable is a vector of length zero. The revised function below is one way to get around this issue: let me know if there are more elegant ways to achieve this!

dropColumns <- function(x, exclude) {
  if (length(exclude) > 0) 
    x[, -exclude, drop = FALSE]
  else x
}

dropColumns(M, c())
#      [,1] [,2] [,3]
# [1,]    1    4    7
# [2,]    2    5    8
# [3,]    3    6    9

dropColumns(M, integer(0))
#      [,1] [,2] [,3]
# [1,]    1    4    7
# [2,]    2    5    8
# [3,]    3    6    9

dropColumns(M, c(1, 2))
#      [,1]
# [1,]    7
# [2,]    8
# [3,]    9
