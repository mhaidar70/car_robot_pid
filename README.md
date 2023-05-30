# PID Robot Car
This project is a robot car that will always stay 15cm away from a setpoint by using 2 TT Motors, DRV8833, and Ultrasonic sensor.
## Table of Contents
* [Planning](#Planning)
* [Design](#Design)
* [Wiring](#Wiring)
* [Code](#Code)
* [Final Product](#final-product)
* [Reflection](#Reflection)
---

## Planning
Below is a link to our planning document where we brainstormed ideas, equipments, and time management.

[Planning Document](https://docs.google.com/document/d/1pS8x4_KN1o8x4viN81sqH_3ViWb4FMq0SSYEMJr-LKw/edit#)

## Design
Here is our design pictures that we protyped and made in Onshape. The reason for this design was because it was very simple and worked well in moving forwards and backwards smoothly. 

[Also our Onshape document](https://cvilleschools.onshape.com/documents/005e585cd5af9495849462fc/w/d7b6680123938b66cb7ad969/e/e5230d64fb9466e0788375d0)

![Screenshot 2023-04-17 112206](https://user-images.githubusercontent.com/112962044/232820076-10aa31a3-67df-49f4-90ae-312871f2cb4b.png)
![Screenshot 2023-04-18 110646](https://user-images.githubusercontent.com/112962044/232820331-7b2efcb0-1866-4748-aa62-67451eb60ea8.png)
![Screenshot 2023-04-18 111012](https://user-images.githubusercontent.com/112962044/232821432-d45af163-7280-4c60-adc9-660c5de55af6.png)



## Wiring
Below is the complete wiring of what we used for this car. 
![Screenshot 2023-05-16 123845](https://github.com/mhaidar70/car_robot_pid/assets/112962044/7225c21c-cf04-4d3e-a901-3bd05c76d1f3)
Credit goes to [IDeATe Courses](https://courses.ideate.cmu.edu/16-223/f2016/text/ex/Arduino/DRV8833-motor-driver/DRV8833-motor-driver.html)


Note: Our Digital Pins are different from the picture. It's the following:    
[AIN1-- D5]
[AIN2-- D6]
[BIN1-- D7]
[BIN2-- D8]
[SLP--5V]

## Code
Below is our code that oves the car back and forth using PID tuning on a setpoint. 

```python



import time
import board
import pwmio
import adafruit_hcsr04
from simple_pid import PID



setpoint=15
pid = PID(11000, 3600, 2000, setpoint)

pid.sample_time = 0.01  # Update every 0.01 seconds
pid.output_limits = (-65535, 65535)    # Output duty_cycle will be between 0 and 10

distance=0.0

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
AIN1 = pwmio.PWMOut(AIN1_PIN)
AIN2 = pwmio.PWMOut(AIN2_PIN)
BIN1 = pwmio.PWMOut(BIN1_PIN)
BIN2 = pwmio.PWMOut(BIN2_PIN)




# Set PWM pins to output low
AIN1.duty_cycle = 0
BIN1.duty_cycle = 0

print("Lets go!")


while True: 
  
    try:
        # Read distance from ultrasonic sensor
        distance = sonar.distance
        print("Distance: {0:0.2f} cm".format(distance))

        # Compute new output from the PID according to the systems current duty_cycle
        control = pid(distance)
        print(control)

        control = int(abs(control))
        

        
        # If distance is greater than setpoint, move forward
        if distance > setpoint:
            AIN1.duty_cycle = control
            AIN2.duty_cycle = False
            BIN1.duty_cycle = control
            BIN2.duty_cycle = False

        # If distance is less than setpoint, move backward
        elif distance < setpoint:
            AIN1.duty_cycle = False
            AIN2.duty_cycle = control
            BIN1.duty_cycle = False
            BIN2.duty_cycle = control

        # If distance is exactly setpoint, stop moving
        else:
            print("stopped")
            AIN1.duty_cycle = False
            AIN2.duty_cycle = False
            BIN1.duty_cycle = False
            BIN2.duty_cycle = False

        # Wait for 0.2 seconds before reading distance again
        time.sleep(.2)

    # If there's an error reading distance, print the error message and continue
    except RuntimeError as e:
        print("Error: ", e)
        continue

# Get P close to 15 but not completely so it's still oscillating
# Get I the rectangle between setpoint and car to get close
# Get D to maintain slope to 0


```

## Final Product
![image1](https://github.com/mhaidar70/car_robot_pid/assets/112962044/995dc747-e31d-40be-a262-a61725cce564)
![image2](https://github.com/mhaidar70/car_robot_pid/assets/112962044/3985b8b0-9301-4e6e-84da-cb57b85613d5)
![image0 (1)](https://github.com/mhaidar70/car_robot_pid/assets/112962044/dfc08db3-2cad-4b32-84b9-7e489133814d)



## Reflection


