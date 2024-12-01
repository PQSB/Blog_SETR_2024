# P3 - Vending Machine Controller

**Date:** 01/12/2024

**Author:** Andrés Galea Torrecilla

**Circuit schematic:**

**Execution video:**

## Implementation details:
My implementation is based on a **state machine**, whose possible states depend on the two modes of operation, **CLIENT** mode and **ADMIN** mode.

### Key states:
  - **SPIRAL_MOTION:** The robot follows a spiral pattern, so that, at the begining it covers the greatest area possible.

  - **MOVING_BACKWARD:** When the bumper detects a collision (on the left, right or front), the robot moves backward for a set amount of time. This gives it some space to avoid the obstacle detected.
  
  - **TURNING:** After moving backward, the robot turns in the direction set by the bumper zone that detected the obstacle. Moreover, depending on the laser measures at 0º, 90º and 180º it decides whether it's too close to a wall or obstacle. Depending on those meausres there are two possible turn types:
       - Normal turn: If it has enough space in front and around the robot, it performs a normal turn with random parameters.
       - Wall turn: If it is too close to a wall or obstacle it turns at a smaller angular speed to try to follow the wall or to go around the obstacle.
         
      To avoid getting stuck if the robot detects that the wall turn type has been used more times than the maximum established, it makes a big turn.
  
  - **MOVING_FORWARD:** After the turn, the robot moves forward with a constant linear speed until the bumper is hit. Furthermore, if the robot detects that the turn direction has been the same more times than the maximum established, it adds a constant angular speed until another turn direction is choosen.

## Interruptions
I have used interruptions for both buttons:
  - **Push button:** This interruption is configured in CHANGE so that it runs every the button changes from HIGH to LOW or LOW to HIGH. This interruption is the one in charge of updating the execution mode (if the button has been pushed for 5 s or more) 

  - **Joystick push button:** 

## Threads

## Whatchdog

## Difficulties:

While developing the algorithm I found some difficulties:
  - The robot getting stuck in certain places of the map. I try to solve it by including conditions that check if the robot has been a lot of time close to the wall.
  
  - The robot being unable to get out of rooms with hard access, I try to solve it by adding angular speed to the movement while following the wall so that it makes easier for the robot to rotate himself the correct way to make his way out of the room.
  
  - The robot not convering enough sapce of the house. To solve this I try to find a solution that combines some random elements with some established elements, such as having established turn directions depending on the bumper zone hit but turning during a random period of time (in the normal case).

  - Not being able to make the robot turn a concrete angle as long as the robot turns must be calculated by substraction of times. To solve this I decided to get the laser data only of three concrete angles and to make the turns based more on the bumper that on the laser sensor. Using the laser sensor only to estimate the freedom of the area around the robot.
