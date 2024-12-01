# P3 - Vending Machine Controller

**Date:** 01/12/2024

**Author:** Andrés Galea Torrecilla

**Circuit schematic:**

**Execution video:**

## Implementation details:
My implementation is based on a **state machine**, whose possible states depend on the two modes of operation, **CLIENT** mode and **ADMIN** mode. Each state is described below:
  - d
  -
  -

### Key states:

## Interruptions
I have used hardware interruptions for both buttons because they are **asynchronous** and so, they are the best option to get the required functionality for both buttons. Each interruption has different tasks:
  - **Push button:** This interruption is configured in CHANGE so that it runs every time the button changes from HIGH to LOW or from LOW to HIGH. It's the one in charge of updating the execution mode from client to admin or from admin to client (if the button has been pushed for 5 s or more) or reset the client functionality to the WAITING state (if the button has been pushed between 2 and 3 s and the current execution mode is the client one).

  - **Joystick push button:** This interruption is configured in FALLING so that it runs every time the button is pushed and state changes from HIGH to LOW (since it's INPUT_PULLUP). It's the one in charge of 'accepting' some actions:
    - Choose a coffee to order.
    - Choose the admin menu functionality.
    - Confirm the new price when changing a coffee price.
    
    This interruption is only working when if the current state is one of the following: SHOWING_COFFEES, CHANGE_PRICES, SHOWING_ADMIN_MENU and CHANGING_PRICE.

Both interruptions are not working in the first LOADING state (blinking led), they are configured after.

## Threads
I have used two threads in my implentation, and so, one thread controller.
  - **blink_thread:** This thread is in charge of turning on and off LED1 during the initial LOADING (blinking) state. It's enabled in the *void setup* and disabled once the LOADING state is completed. It executes every second and changes the state of LED1. Once the LED has change the state of the LED *nblinks * 2* times it disables and the program moves on to the next state.  

  -**temp_hum_thread:** This thread is in charge of 
## Whatchdog

## Difficulties:

While developing the algorithm I found some difficulties:
  - The robot getting stuck in certain places of the map. I try to solve it by including conditions that check if the robot has been a lot of time close to the wall.
  
  - The robot being unable to get out of rooms with hard access, I try to solve it by adding angular speed to the movement while following the wall so that it makes easier for the robot to rotate himself the correct way to make his way out of the room.
  
  - The robot not convering enough sapce of the house. To solve this I try to find a solution that combines some random elements with some established elements, such as having established turn directions depending on the bumper zone hit but turning during a random period of time (in the normal case).

  - Not being able to make the robot turn a concrete angle as long as the robot turns must be calculated by substraction of times. To solve this I decided to get the laser data only of three concrete angles and to make the turns based more on the bumper that on the laser sensor. Using the laser sensor only to estimate the freedom of the area around the robot.
