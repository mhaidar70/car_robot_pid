# PID Robot Car
This project is a robot car that will always stay 15cm away from a setpoint by using 2 TT Motors, DRV8833, and Ultrasonic sensor.
## Table of Contents
* [Planning](#Planning)
* [Design](#Design)
* [Wiring](#Wiring)
* [Code](#Code)
* [Final Product](#Final_Product)
* [Reflection](#Reflection)
---

## Planning
Below is a link to our planning document where we brainstormed ideas, equipments, and time management.

[Planning Document](https://docs.google.com/document/d/1pS8x4_KN1o8x4viN81sqH_3ViWb4FMq0SSYEMJr-LKw/edit#)

## Design
Here is our design pictures that we protyped and made in Onshape. The reason for this design was because it was very simple and worked well in moving forwards and backwards smoothly. 

![Screenshot 2023-04-17 112206](https://user-images.githubusercontent.com/112962044/232820076-10aa31a3-67df-49f4-90ae-312871f2cb4b.png)
![Screenshot 2023-04-18 110646](https://user-images.githubusercontent.com/112962044/232820331-7b2efcb0-1866-4748-aa62-67451eb60ea8.png)
![Screenshot 2023-04-18 111012](https://user-images.githubusercontent.com/112962044/232821432-d45af163-7280-4c60-adc9-660c5de55af6.png)



## Wiring
Below is the complete wiring of what we used for this car. 


## Code
Below is our code that oves the car back and forth using PID tuning on a setpoint. 

```python

import time
import board
import adafruit_hcsr04
import digitalio

# Ultrasonic Sensor Pins
TRIG_PIN = board.D2
ECHO_PIN = board.D3

# Motor Driver Pins
AIN1_PIN = board.D5
AIN2_PIN = board.D6
BIN1_PIN = board.D7
BIN2_PIN = board.D8

# Set up GPIO pins
sonar = adafruit_hcsr04.HCSR04(trigger_pin=TRIG_PIN, echo_pin=ECHO_PIN)
AIN1 = digitalio.DigitalInOut(AIN1_PIN)
AIN2 = digitalio.DigitalInOut(AIN2_PIN)
BIN1 = digitalio.DigitalInOut(BIN1_PIN)
BIN2 = digitalio.DigitalInOut(BIN2_PIN)


AIN1.direction = digitalio.Direction.OUTPUT
AIN2.direction = digitalio.Direction.OUTPUT
BIN1.direction = digitalio.Direction.OUTPUT
BIN2.direction = digitalio.Direction.OUTPUT


# Set PWM pins to output low
AIN1.value = False
BIN1.value = False

while True:
    try:
        # Read distance from ultrasonic sensor
        distance = sonar.distance
        print("Distance: {0:0.2f} cm".format(distance))

        # If distance is greater than 15 cm, move forward
        if distance > 15:
            AIN1.value = True
            AIN2.value = False
            BIN1.value = True
            BIN2.value = False

        # If distance is less than 15 cm, move backward
        elif distance < 15:
            AIN1.value = False
            AIN2.value = True
            BIN1.value = False
            BIN2.value = True

        # If distance is exactly 15 cm, stop moving
        else:
            AIN1.value = False
            AIN2.value = False
            BIN1.value = False
            BIN2.value = False

        # Turn on motors
        AIN1.value = True
        AIN2.value = True
        BIN1.value = True
        BIN2.value = True
        # Wait for 0.1 seconds before reading distance again
        time.sleep(0.1)

    # If there's an error reading distance, print the error message and continue
    except RuntimeError as e:
        print("Error: ", e)
        continue
```

## Final Product

## Reflection

