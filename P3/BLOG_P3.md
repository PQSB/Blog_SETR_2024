# P3 - Vending Machine Controller

**Date:** 01/12/2024

**Author:** Andrés Galea Torrecilla

**Circuit schematic:**

**Execution video:**

## Implementation details:
My implementation is based on a **state machine**, whose possible states depend on the two modes of operation, **CLIENT** mode and **ADMIN** mode.

### Key states:

## Interruptions
I have used interruptions for both buttons:
  - **Push button:** This interruption is configured in CHANGE so that it runs every the button changes from HIGH to LOW or LOW to HIGH. It's the one in charge of updating the execution mode from client to admin or from admin to client (if the button has been pushed for 5 s or more) or reset the client functionality to the WAITING state (if the button has been pushed between 2 and 3 s and the current execution mode is the client one).

  - **Joystick push button:** 

## Threads

## Whatchdog

## Difficulties:

While developing the algorithm I found some difficulties:
  - The robot getting stuck in certain places of the map. I try to solve it by including conditions that check if the robot has been a lot of time close to the wall.
  
  - The robot being unable to get out of rooms with hard access, I try to solve it by adding angular speed to the movement while following the wall so that it makes easier for the robot to rotate himself the correct way to make his way out of the room.
  
  - The robot not convering enough sapce of the house. To solve this I try to find a solution that combines some random elements with some established elements, such as having established turn directions depending on the bumper zone hit but turning during a random period of time (in the normal case).

  - Not being able to make the robot turn a concrete angle as long as the robot turns must be calculated by substraction of times. To solve this I decided to get the laser data only of three concrete angles and to make the turns based more on the bumper that on the laser sensor. Using the laser sensor only to estimate the freedom of the area around the robot.