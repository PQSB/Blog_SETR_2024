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

  -**temp_hum_thread:** This thread is in charge of updating the values of the temperature and humidity measured by the DHT11 sensor. It's disabled until LOADING state ends, since temperature and humidity values are not relevant during this period. Once it's enabled it executes every second and it updates the temperature and humidity values. Will remain enabled from the first time it is enabled.

I decided to use **blink_thread** to have more freedom during the LOADING state and be able to add an extra functionality in this state that displays adds a dot to the 'CARGANDO' display every second.

On the other hand, I decided to use **temp_hum_thread** to encapsulate this task and control it's execution period since these values do not need to be updated continuously.

## Whatchdog

## Difficulties:

While developing the algorithm I found some difficulties:
