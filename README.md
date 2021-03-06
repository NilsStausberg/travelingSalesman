# Traveling salesman problem
## How to run the code
[tsp.R](tsp.R) and
[tsp.C](tsp.c) are files solving the traveling salesman problem. Choose **tsp.R** for understanding purpose, since comments are available. Further in the R-code, for illustration reasons, pictures are drawn automatically.

The C-code is an equivalent implementation, to speed up the calculation. Here, more advanced computations like the examination of the solution on different parameters can be performed. Examples are implemented but no explanation is given.

In order to run **tsp.R** the package ***combinat*** and ***scatterplot3d*** have to be installed. Then, in R do **source("tsp.R")** for running the script. The data in file [beer127.tsp](data/beer127.tsp) is automatically loaded and saved as variables **x** and **y**. With this done, following functions are available:

- **random(x,y)** - Chooses a random connection between all places and calculates the total distance to travel.
- **brute(x,y)** - Finds the best connection using brute force. WARNING! This is not recommended on such a large data set. Only try this with a dataset, which consists of a few places.
- **sa(x,y)** - Suggest a "best" connection using simulated annealing.

[beer127.mp4](beer127.mp4) shows a video of the algorithm, while finding the optimized path with respect to the distance. The final result can be seen in [beer.pdf](beer.pdf). In the following a rough explanation of the implementation is given.

---
## Project
The traveling salesman problem (TSP) is a long-standing and often discussed issue in computational physics and operations research: a traveling salesperson has to find the shortest closed tour between a certain set of cities, where each city is only visited once during the tour.

Naturally, this problem has applications in many every-day problems, e.g. in finding directions using a route guiding system. Optimizations can be achieved with respect to different functions, e.g. the shortest path, the quickest path, and others.

The following development of the algorithm is mostly inspired by a paper "Optimization of the time-dependent traveling salesman problem with Monte Carlo method" by Johannes Bentner et al. (
[paper.pdf](paper.pdf)).

The data which is used, can be found in the folder **data/** and consists out of 127 beer garden, distributed around Augsburg, Germany.

## Method
### Simulated annealing
Simulated Annealing is a Monte-Carlo-method inspired by actual physics. It is used to find an approximate optimum of a function in solution space. The general idea is to start off by setting the system, which one wishes to observe, to a well-chosen starting temperature ![](http://latex.codecogs.com/gif.latex?%24T_%5Ctext%7Bstart%7D%24.). The system should be in equilibrium state at the beginning of the observation. This can be assumed to be the case, if the starting temperature is large compared to the maximal possible energy-fluctuations of the system. The measure one uses to define the optimum, is the energy of the system. Therefore one wants to optimize (minimize or maximize) the function in accordance to the energy of the system, where the system mean energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%24) at temperature ![](http://latex.codecogs.com/gif.latex?%24T%24) can be calculated as:

![](http://latex.codecogs.com/gif.latex?%5Cbar%7B%5Cmathcal%7BH%7D%7D%20%3D%20%5Cint%20%5Ctext%7Bd%7Dx%20%5C%3B%20%5Cmathcal%7BH%7D%28x%29%20%5C%3Be%5E%7B-%5Cfrac%7B%5Cmathcal%7BH%7D%20%28x%29%7D%7BT%7D%7D)

where ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x%29%24) is the function that is being  optimized and ![](http://latex.codecogs.com/gif.latex?%24x%24) the parameter, that ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x%29%24) is being optimized for.
The clue is to decrease the temperature ![](http://latex.codecogs.com/gif.latex?%24T%24) towards a value close to 0. With decreasing temperature the system cools down, meaning that the system energy decreases until it hits a local or, if one is lucky, the global minimum, which would also be the ground state of the system.

### Algorithm
The computational algorithm is as follows.
One starts of by choosing a random system setting ![](http://latex.codecogs.com/gif.latex?%24x_i%24), in case of a TSP one would start of by choosing a random permutation of the sequence in which the cities will be visited. The energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_i%29%24) (which symbolizes the total distance in the case of a general TSP) is then calculated for this setting. A new setting ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) is then proposed and the corresponding energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi&plus;1%7D%29%24) calculated. Depending on the energy of ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) the setting is accepted or not. If the energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi&plus;1%7D%29%24) is smaller than the energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi%7D%29%24), the new setting ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) always gets accepted. Else if the new energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi&plus;1%7D%29%24) is larger than the energy of the previous setting ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi%7D%29%24) the new setting ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) gets accepted with the probability

![](http://latex.codecogs.com/gif.latex?P_%7B%5Ctext%7Bacc%7D%7D%20%3D%20%5Ctext%7Bmin%7D%5Cleft%28exp%5Cleft%28-%5Cfrac%7B%5CDelta%20%5Cmathcal%7BH%7D%7D%7BT%7D%5Cright%29%2C1%5Cright%29%2C)

where ![](http://latex.codecogs.com/gif.latex?%24%5CDelta%20%5Cmathcal%7BH%7D%20%3D%20%5Cmathcal%7BH%7D%28x_%7Bi&plus;1%7D%29-%20%5Cmathcal%7BH%7D%28x_%7Bi%7D%29%24). This allows the system to leave its current potentially local minimum to find a new one. If ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) is not accepted, ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) is discarded, while the old setting ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi%7D%24) is kept. A new setting is then drawn and the process is repeated for a fix number of times ![](http://latex.codecogs.com/gif.latex?%24I%24). After this ![](http://latex.codecogs.com/gif.latex?%24I%24) times the temperature ![](http://latex.codecogs.com/gif.latex?%24T%24) is decreased and the process repeated until convergence is reached. During the decrease of the temperature ![](http://latex.codecogs.com/gif.latex?%24T%24), less new settings ![](http://latex.codecogs.com/gif.latex?%24x_%7Bi&plus;1%7D%24) are accepted if their energy ![](http://latex.codecogs.com/gif.latex?%24%5Cmathcal%7BH%7D%28x_%7Bi&plus;1%7D%29%24) is higher than the previous. Therefore it gets less likely to walk out of a local minimum for low temperatures. The system wanders only locally over the solution space, while for larger ![](http://latex.codecogs.com/gif.latex?%24T%24) it still travels over greater areas.

There are several ways of decreasing the temperature. This is crucial, since it is influencing how fast the algorithm is confined in a shrinking area in solution space.
In the following the temperature is lowerd using:

![](http://latex.codecogs.com/gif.latex?%24T_%7Bj%7D%3D%5Calpha%5Ej%20T_%7B%5Ctext%7Bstart%7D%7D%24)

## Implementation
The implementation of the algorithm can be divided into several steps:

1. Calculation of a square matrix, containing the distances between all points. This matrix is used to calculate the total distance of each suggested state (connection), without to calculate the distance between the places all over again. Since the matrix is symmetric only an upper- or lower triangular matrix is needed to be calculated in first place.
2. Start off with a random connection (a random permutation of all places) and with this a random total distance to travel.
3. Before applying the simulated annealing algorithm, a start temperature has to be found. For this a random walk in solution space is performed (in other words every step of a newly randomly suggested connection is accepted). A value proportional to the maximal energy difference between these steps is chosen as a good approximation for the starting temperature.
4. The simulated annealing algorithm, as explained above, is applied.
5. To stop the algorithm a minimal temperature is needed as a threshold. The temperature can be set by hand, or better by testing on convergence. This can be done by looking at the number of accepted connections each temperature step.
5. The result is visualized.
