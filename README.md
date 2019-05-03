# Julia ACO TSP
This project is to implement ACO(Ant Colony Optimization) and assess its performance for solving TSP(Traveling Salesman Problem) in Julia. **Note** that the uploaded files is intended to run in Jupyter Notebook.

## Installation
We first need to install Julia and Jupyter notebook with this link (https://datatofish.com/add-julia-to-jupyter/)

## Intro to Julia
"The Julia language is a high-level programming language aimed at doing data and numerical analysis without worrying about the specific computation implementation details. " It's similart to Python and C. You can download the **Julia_Code.ipynb** to learn more about Julia or visit the website (https://docs.julialang.org/en/v1/).

## TSP
**Traveling Salesman Problem** is a combinatorial optimization problem. Given a set of cities and the distance between each pair, our goal is to find the shortest route that visits all the cities without repetition and returns to the original city. The distance between cities does not necessarily mean how far the two cities are physically apart. It represents the cost to travel from one city to another, so the cost from city A to city B sometimes doesn’t equal to the cost from city B to city A. For example, the flight cost between two cities usually are not the same and the time needed is not equivalent. Therefore, not all the graphic representation of TSP is consistent with the Triangle Inequality Theorem, which states the sum of the length of any two sides is greater than the third one in a triangle. In Figure 1, the left graph is based on Euclidean Distance, so the cost between the two cities are fixed. The right graph is based on the traveling cost, so the cost between two cities vary. In this paper, we use the Euclidean Distance one for simplicity. 
[image]

There are three ways to **get a TSP**. You can randomly generate a new TSP without optimal solution, which cannot use the reporting feature in the code. Another choice is to download the existing TSP with optimal solution and use it as input file. THe last choice is to use the hard-coded TSP problem. We have 500-city, 800-city, and 1000-city TSPs without solution as well as 48-city and 131-city TSPs with the optimal solution. If you want to see how close the generated solution can get to the optimal solution, you need to choose from the last two options.

### Background
The Traveling Salesman Problem was first defined in the 1800s by W.R. Hamilton and Thomas Kirkman and haven’t been solved now. The problem doesn’t seem to be difficult if we just use a set of four cities, but it will be exponentially expensive to compute all the solutions of a larger amount of cities. Given n cities and their location, the salesman starts with one of them. Then, he has (n - 1) cities left to choose. After he moves to the second city, (n - 2) cities are left. This process goes on until he finishes the tour, so we have (n-1)! possible tours. Because the tours are not distinguished by the direction, we also need to divide the number by two. Therefore, given n cities, there will be (n - 1) !2possible solutions. 
n! =12....(n-1)n
Wheat and Chessboard Problem is a famous mathematical problem. In the story, the king promised to give the man any reward he wants if he wins the chess game. The man simply asked the put one grain on the first square of the chessboard, two on the second, and four on the third. The amount is doubled every time and he wants to have the sum of the grains of wheat. The sum is 264-1grains of wheat. The weight is about 1,199,000,000,000 metric tons. which is about 1,645 times the global production of wheat in 2014. This demonstrates the rapid growth of the exponential function 2n. The factorial function n!, on the other hand, even grows faster than exponential function 2n, so it’s nearly impossible to compute all the solutions and compare them to find the best one as the number of the cities increases. 
There is a way to reduce the time complexity to n22n, which is much less than factorial function n!, but the time needed is still exponential and expensive. Due to the cost to compute all the solutions, it’s effective to find a relatively good solution with some algorithms.

## Ant Colony Algorithms
### Ant Colony Optimization (ACO)
**Ant Colony Optimization** obviously mimics ants’ behavior of finding the shorter route from the nest and food source with pheromone-based communication. The leftmost image of Figure 3 is the simplest situation where there is no obstacle in the way from nest to food. The ants just move towards one direction. The second image is when the pheromone starts to work. If a stone is put on the way, the ants don’t know which way is shorter. They will each randomly chose a path and deposit some pheromone along the way. The pheromone is a chemical substance that evaporates as time passes, so the ants will get some information about the tour based the remnant pheromone. Interestingly, ants are more likely to choose the path with more pheromone, which indicates a shorter length, but it’s not a guaranteed choice. This randomness ensures the high probability of choosing the current best tour as well as the opportunity of exploring the new tours.

### Max-Min Ant System (MMAS)
**Max-Min Ant System** and ACO are both in Ant Colony Algorithm Family and have very similar behaviors. One unique feature of the MMAS is the extra control over the pheromone. The pheromone on the edge will always be in a range, which avoids the situation where the best solution dominates. 

## Implementation
### Choices
I didn't choose struct to implement the basic structures, such as city, ant, pheromone, for two reasons. (1) Struct has more constraints and strict rules to follow. (2) Julia is a Python-like language. Ut should run faster with the basic data structure, array. If Julia is an object-oriented language, like Java, it makes more sense to use object/struct.
We tried to implement the pheromone map as dictionary so that we don't need to keep track of all the edges, especially for MMAS. When the pheromone level hits the lower bound, we delete it from the dictionary. This implementation should save the space, but it also hurts the speed. Using dictionary is three times slower than the array, which requires much longer to run a big TSP. Therefore, we changed the dictionary back to array. 


### Planning
There are several ways we can improve our program
(1) One observation along the implementation is the similarity between distance matrix and pheromone map. They are both symmetric matrix because the distance from i to j equals to the distance from j to i. Same with the pheromone map. That is to say, they both wasting half of the space storing repeated data. By merging the two arrays, we are able to save space without hurting the speed.
(2) The program first run the greedy algorithm to quickly get several solutions (tours). Then pheromone is deposited along these 10 tours based on the length. In this case, instead of starting without knowing anythiinng, the ants will have some information about the TSP. 
[image]
