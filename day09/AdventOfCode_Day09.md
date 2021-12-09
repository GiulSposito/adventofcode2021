---
title: "Day 9: Smoke Basin"
output:
  html_document:
    keep_md: yes
editor_options: 
  chunk_output_type: console
---

## Part 01

These caves seem to be lava tubes. Parts are even still volcanically active; small hydrothermal vents release smoke into the caves that slowly settles like rain.

If you can model how the smoke flows through the caves, you might be able to avoid it and be that much safer. The submarine generates a heightmap of the floor of the nearby caves for you (your puzzle input).

Smoke flows to the lowest point of the area it's in. For example, consider the following heightmap:

```
2199943210
3987894921
9856789892
8767896789
9899965678
```

Each number corresponds to the height of a particular location, where 9 is the highest and 0 is the lowest a location can be.

Your first goal is to find the low points - the locations that are lower than any of its adjacent locations. Most locations have four adjacent locations (up, down, left, and right); locations on the edge or corner of the map have three or two adjacent locations, respectively. (Diagonal locations do not count as adjacent.)

In the above example, there are four low points, all highlighted: two are in the first row (a 1 and a 0), one is in the third row (a 5), and one is in the bottom row (also a 5). All other locations on the heightmap have some lower adjacent location, and so are not low points.

The risk level of a low point is 1 plus its height. In the above example, the risk levels of the low points are 2, 1, 6, and 6. The sum of the risk levels of all low points in the heightmap is therefore 15.

### Test Case


```r
# test data as input
input_raw <- c("2199943210","3987894921","9856789892","8767896789","9899965678")

# vector of integers
input <- paste0(input_raw, collapse = "") |>
  strsplit("") |>
  unlist() |>
  as.integer()

m <- matrix(input, nrow = length(input_raw), byrow = T)
# create shifted versions of the matrix (adjacency)
m_up <- apply(m,2,dplyr::lag)
m_dw <- apply(m,2,dplyr::lead)
m_lf <- t(apply(t(m),2,dplyr::lag))
m_rt <- t(apply(t(m),2,dplyr::lead))

# create a 3 dimensional (x,y,z)
# where z is a shifted element from the original matrix
mm <- array(c(m,m_up,m_dw,m_lf,m_rt), dim = c(dim(m)[1],dim(m)[2],5))

# locate the minimal
minmap <- matrix(rep(F,length(input)), nrow = dim(m)[1], byrow = T)
# for each (x,y) get the vector in the dimension z -> position + adjacent
for(i in 1:dim(mm)[1]){
  for(j in 1:dim(mm)[2]){
    adj <- mm[i,j,] 
    # where is the minimal (position + adjacent)
    min_adj <-  which(mm[i,j,]==min(mm[i,j,], na.rm = T))
    # minimal is the position itself?
    local_min <- (length(min_adj)==1 && min_adj==1)
    minmap[i,j] <- local_min
  }
}

# get the minimal positions and sum 1 to get risk level
sum(m[minmap]+1)
```

```
## [1] 15
```

### Puzzle Answer

Find all of the low points on your heightmap. What is the sum of the risk levels of all low points on your heightmap?


```r
# input as array of lines
input_raw <- readLines("./input.txt")

# vector of integers
input <- paste0(input_raw, collapse = "") |>
  strsplit("") |>
  unlist() |>
  as.integer()

m <- matrix(input, nrow = length(input_raw), byrow = T)
# create shifted versions of the matrix (adjacency)
m_up <- apply(m,2,dplyr::lag)
m_dw <- apply(m,2,dplyr::lead)
m_lf <- t(apply(t(m),2,dplyr::lag))
m_rt <- t(apply(t(m),2,dplyr::lead))

# create a 3 dimensional (x,y,z)
# where z is a shifted element from the original matrix
mm <- array(c(m,m_up,m_dw,m_lf,m_rt), dim = c(dim(m)[1],dim(m)[2],5))

# locate the minimal
minmap <- matrix(rep(F,length(input)), nrow = dim(m)[1], byrow = T)
# for each (x,y) get the vector in the dimension z -> position + adjacent
for(i in 1:dim(mm)[1]){
  for(j in 1:dim(mm)[2]){
    adj <- mm[i,j,] 
    # where is the minimal (position + adjacent)
    min_adj <-  which(mm[i,j,]==min(mm[i,j,], na.rm = T))
    # minimal is the position itself?
    local_min <- (length(min_adj)==1 && min_adj==1)
    minmap[i,j] <- local_min
  }
}

# get the minimal positions and sum 1 to get risk level
sum(m[minmap]+1)
```

```
## [1] 504
```
