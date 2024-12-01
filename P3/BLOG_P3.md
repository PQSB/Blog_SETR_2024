# P3 - Vending Machine Controller

**Date:** 01/12/2024

**Author:** Andrés Galea Torrecilla

**Circuit schematic:** https://urjc-my.sharepoint.com/:b:/g/personal/a_galea_2022_alumnos_urjc_es/EeKfaghQZJNEh9Fact-LfDUB6V8HPvq2MPstDVrqdJnr0A?e=qFgLAv

**Execution video:**

## Implementation details:
My implementation is based on a **state machine**, whose possible states depend on the two modes of operation, **CLIENT** mode and **ADMIN** mode. Each state is described below:
  - Initial state:
    - **LOADING:** This state displays *CARGANDO* and adds a dot to the display every second until 3 blinks of 1s interval are completed.
  
  - Client states:
    - **WAITING:** This state displays *ESPERANDO CLIENTE* until the ultrasonic sensor detects an user closer than 1m.

    - **SHOWING_SENSORS:** This state displays during 5s the values for temperature and humidity.

    - **SHOWING_COFFEES:** This state allows the client to navigate along all the available coffees displaying the current coffee until the client pushes the josytick button to select one.

    - **PREPARING_COFFEE:** This state displays *Preparando Cafe...* and turns on a LED2, whose intensity depends (turned on using pwm) on the time remaining for the state to end, which will be randomly chosen between 4s and 8s.

    - **COFFEE_READY:** This state displays *RETIRE BEBIDA* for 3s.
    
  - Admin states
    - **SHOWING_ADMIN_MENU:** This state allows the client to navigate along all the available admin functionalities displaying the current functionality until the client pushes the josytick button to select one.
  
    In the following states, the user can go back to the previous state moving the joystick to the left direction.
    
    - **WATCH_TEMP_HUM:** This state displays the temperature and humidity values.

    - **WATCH_DIST:** This state displays the distance measured by the ultrasonic sensor.
    
    - **WATCH_COUNTER:** This state displays the time passed since the board was turned on in seconds.
    
    - **CHANGE_PRICES:** This state allows the client to navigate along all the available coffees displaying the current coffee until the client pushes the josytick button to select one.
    
    - **CHANGING_PRICE:** This state allows the client to modify the price of the coffee chosen in the preivous state (CHANGE_PRICES) by moving the joystick up and down. Each time the price increases (up) or decreases in **0.05€**. Furthermore, the minimum allowable cost is 0€, negative values are not allowed. To confirm the new price, the client has to push the joystick button.

By the way, to facilitate the navigation through all the available coffees and admin functionalities, I created the Cafe and the Admin_menu_function structs, so that it's easier to access information relating to each of them. Furthermore, I used the utils files to try to encapsulate as much code as possible outside the main.ino file.

## Interruptions
I have used hardware interruptions for both buttons because they are **asynchronous** and so, they are the best option to get the required functionality for both buttons. Each interruption has different tasks:
  - **Push button:** This interruption is configured in CHANGE so that it runs every time the button changes from HIGH to LOW or from LOW to HIGH. It's the one in charge of updating the execution mode from client to admin or from admin to client (if the button has been pushed for 5 s or more) or reset the client functionality to the WAITING state (if the button has been pushed between 2 and 3 s and the current execution mode is the client one).

  - **Joystick push button:** This interruption is configured in FALLING so that it runs every time the button is pushed and state changes from HIGH to LOW (since it's INPUT_PULLUP). It's the one in charge of 'accepting' some actions:
    - Choose a coffee to order (move from SHOWING_COFFEES to PREPARING_COFFEE).
      
    - Choose the admin menu functionality (move from SHOWING_ADMIN_MENU to any of the possible states excepting CHANGING_PRICE).
      
    - Confirm the new price when changing a coffee price (move from CHANGING_PRICE to CHANGE_PRICES).
  
    This interruption is only working if the current state is one of the following: SHOWING_COFFEES, CHANGE_PRICES, SHOWING_ADMIN_MENU and CHANGING_PRICE.

Both interruptions are not working in the first LOADING state (blinking led), they are configured after.

## Threads
I have used two threads in my implentation, and so, one thread controller.
  - **blink_thread:** This thread is in charge of turning on and off LED1 during the initial LOADING (blinking) state. It's enabled in the *void setup* and disabled once the LOADING state is completed. It executes every second and changes the state of LED1. Once the LED has change the state of the LED *nblinks * 2* times it disables and the program moves on to the next state.  

  - **temp_hum_thread:** This thread is in charge of updating the values of the temperature and humidity measured by the DHT11 sensor. It's disabled until LOADING state ends since temperature and humidity values are not relevant during this period. Once it's enabled it executes every second and it updates the temperature and humidity values. Will remain enabled from the first time it is enabled.

I decided to use **blink_thread** to have more freedom during the LOADING state and be able to add an extra functionality in this state that adds a dot to the 'CARGANDO' display every second.

On the other hand, I decided to use **temp_hum_thread** to encapsulate this task and control it's execution period since these values do not need to be updated continuously.

## Whatchdog
I decided to use the whatchdog to avoid blocking during execution. It's initialised in the setup and reset in the last line of the void loop. The established time for watchdog is **2s**.

## Difficulties:

While developing the exercise I found some difficulties:

  - Controlling when it's neccessary to clear the screen and to print new data, since at the begining y was clearing the screen in every iteration, which was inefficient, inconvenient and forced me to have a small delay at the end of the void loop. To solve this I did the following upgrades:
    - Modify some functions re-distributing the location of the lcd.print.

    - Adding specific execution time intervals to some functions.

    - Add the **reset_for_display()** function. This function sets the value of the **display** variable, which is used to know when to clear the screen and print new data in the complex display functions **display_coffee, display_admin_menu and change_coffee_price**, to true. So by calling this function in the correct places of the code, the lcd clears the screen only when there is new data to show. So that, this function will appear in concrete places of the code in the file main.ino. Furthermure, in the utils.cpp file, the **display** variable is directly modified when neccessary.

  - Remove button bounces.

  - Regulating the joystick sensibility so that the navigation is comfortable.
