# CarND Path Planning Project

## Goals
The goal of this project was to explore implementing a cost function to allow your vehicle to safely, and efficiently, navigate a highway simulation with other vehicles on the road using a combination of localization data of the your position and the position of cars around you, and a set of waypoints defining the center of the highway all around the map. Using both real world and Frenet coordinates, the goal is to program the car to safely drive around the map while maintaining lane center, unless changing lanes, and without colliding with any surrounding vehicles. Restrictions are defined that limit the maximium acceleration (10m/s^2) and jerk (10m/s^3) as to provide the riders with a comfortable trip. The [rubric](https://review.udacity.com/#!/rubrics/1020/view) explains the requirements for the project.

## Getting Started
View the project outline and how to download and bringup the source code from the repository [here](https://github.com/djiglesias/CarND-Path-Planning-Project/blob/master/overview.md). Once downloaded, the following commands can be run to compile the application.

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

## Lane Keep Assist
The first step of the project was to get the car to drive forward and follow the lane on the road without breaking the rules of the project such as acceleration, jerk, and max speed limit. Thankfully the course provides a detailed [tutorial](https://www.youtube.com/watch?time_continue=1403&v=7sI3VHFPP0w) for this section that demonstrates how to get the car driving around the track by passing a vector of waypoints to the simulator for the car to follow.

The helper functions provided by Udacity included functions for querying the next closest waypoint and conversion between real world and Frenet coordinates which came handy for calculating the ideal path for the car. From the current car's position generate three points ahead of the car using the getXY() function for distances of 30m, 60m, and 90m. Convert these X and Y points to align with the car in a zero degree steering angle reference and pass into the spline function as input parameters. With the spline for the trajectory defined, populate a new trajectory vector with the remaining points calculated from the previous iteration and then populate the remaining points to reach 50 by calculating y from passing the spline the x coordinate and then rotating back to the original car's angle reference.

## Adjust Speed & Avoid Jerk
Initially, the speed for the vehicle was set to 49.5 mph to generate forward motion for learning purposes, however this leads to very high (almost instantaneous) accelerations and large jerks. To account for this the intial speed for set to zero and a loop added to populate points into a vector if zero were present before to ensure a forward motion trajectory was achieved. Using the sensor_fusion information provided, which returns a list of all cars on our side of the road, the list was scanned for any cars in the same lane and within 30m of our location. In the event that a car was present ahead of us in the lane we reduce our target velocity by a minimal amount (0.224 mph) to avoid sudden abrupt accelerations and high jerks. 

<p align="center">
 <img src="./res/collision_avoidance.gif" width=550>
</p>

## Cost Function
Finally, we address the logic for changing lanes since we want to complete a lap in the fastest time possible without getting stuck behind slow pokes. Each time the callback function for the message handler is performed, the `sensor_fusion` data is parsed into three lists contains the cars within each of the three lanes that are within 30m. Using this data, along with our current lane, the program first checks to see if it can pass on the left (as is normal for right-hand side highways), then the right (those cheeky kids in their Honda Civics), or if no lane change is available just reduce speed to avoid a collision.

<p align="center">
 <img src="./res/lane_change.gif" width=550>
</p>

The project rubric calls for a minimum of 4.32 miles to be driven without incident. Our car drove for 7.36 miles before the program was terminated. No collision was detected.

<p align="center">
 <img src="./res/full_lap.png" width=550>
</p>


