# Green Machine
My project is the Green Machine. It is a agriculture robot that plants and waters plants/trees.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Aman | Mission San Jose High School | Astrophysics | Incoming Junior

![Headstone Image](https://lh3.googleusercontent.com/pw/AM-JKLUqwVLOwE-zGW1dLj1Zd_YprCihRz79k14BO8IZSeNU0Xv3QyP-AYmYYFAGavOWbZFLfWW_Ch1vuwSSC7tnXMcrkfqObiM-XP-6RiC-9Tz93Tfqh81yK4Ih5DoT3l1ufrtY455UdwnAC6lQ62roXW9z=w699-h931-no?authuser=0)

![Circuit Diagram](https://lh3.googleusercontent.com/o7-vKY884_VI-tZoQNEDv48JevvL1TO6ICmo9yOtsCkvNkPzaXpJvVwSRp0uGrn5P91kp7KfDvaWPGKC3TCZ30i8W7psL-huq9hp1_VmOD6mnBufRPmxNrhmWKjiYnozod-MKSZ1l5w6yZJmsFX52LLUrtcoyBnzBzx6IqMIKZTOB_Q7Pd7EvDhIA-WqKg4CHKs2L4QT5x8-AWEJuzeGMa8epsVub7voZRFPGK5-2DF-VgkIL0YjjMieoTWpxxFIqH-teGoY1oqqz5Y0t004zQr3rLF1fg4Kfu9xNnxVnyWo-V3vPU_5trTHYx6XMhTZbpSHiSRGbwzXyZOwrAgL71MAIyNuurdUMNIkkK8N_vCE4oDnMPd65cHNfPRtafNlkgNuWP5VDryd-EQTRYSmMqUUclbPZFF-1NaqfuK0e8uYe_aP3iZoRTBG_QqQO9bxSF4-FF4yMznM7-oKbMVgTa_VaWMV0SmyvmAGSAyykBm2umJ_4AUCf6JjzRRXP1B_kJYI6QOcEEQ1J61w-l0DBjtKwpOjWV53Wc37-A76gFa0L49358L-NiZbbQtS4zvoo3MkDfg3XbA4xIlFvTmKDGvcF2ACh1-9N4WnA8bl7wy8anfW1f6vRlCv6PWbMBK0UCRL-Sws4lxL0jgtTO3UpIH9hkOOUGHKBC5gejkl7r8u5Z5Kjo5hiDafC9X4SGD8_7FhpjFMPH6L2oJOXMr5trkfw3Sy6-Ktn-V9XNiwpJ3b-FiiMaDP08FWTejx1A=w1474-h894-no?authuser=0)

```
#include <SoftwareSerial.h> // includes Software Serial Library
#include <Servo.h> // Includes Servo Library
SoftwareSerial BT(2, 3); // RX, TX // declares  Bluetooth software communication pins as 2 for receiving and 3 for transmitting

#define enA 10 // defines pins which motor controller is connected to
#define in1 4
#define in2 5

#define in3 6
#define in4 7
#define enB 9

Servo servo; // makes servo a Servo variable
int xAxis = 140, yAxis = 140; // creates 2 variables for position of joystick and sets the value to 140
int readByte; // creates a integer variable for reading the bytes
int motorSpeedA = 0; // creates the motor speed variables and sets the speed to 0
int motorSpeedB = 0;
const int RELAY_PIN = A5; // declares pin A5 as the variable RELAY_PIN
void setup() {
  pinMode(enA, OUTPUT); // Declares motor pins as an output
  pinMode(enB, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(RELAY_PIN, OUTPUT); // Declares relay pin as an output
  // Declares servo attached to pin 11
  servo.write(80); // Turns the servo to the 90 degree position

  Serial.begin(9600); // begins serial at the Baud rate of 9600
  BT.begin(9600); // Default communication rate of the Bluetooth module
  delay(500);
}

void loop() {
  // Default value - no movement when the Joystick stays in the center
  //xAxis = 140;
  //yAxis = 140;
  // Read the incoming data from the Smartphone Android App
  while (BT.available() >= 2) {
    xAxis = BT.read();
    delay(10);
    yAxis = BT.read();
    Serial.print(xAxis);
    Serial.print(",");
    Serial.println(yAxis);

  }
  delay(10);

  if (xAxis == 254 && yAxis == 255) {
    digitalWrite(RELAY_PIN, HIGH);
    Serial.println("WaterOn");
  }

  if (xAxis == 255 && yAxis == 254) {
    digitalWrite(RELAY_PIN, LOW);

  }

  if (xAxis == 252 && yAxis == 253) {
    servo.attach(11);
    delay(48);
    servo.write(0);
    servo.read();
    Serial.println("PlantSeed");
  }

  if (xAxis == 253 && yAxis == 252) {
    servo.write(80);
    delay(48);
    servo.detach();
  }

if (xAxis > 130 && xAxis < 150 && yAxis > 130 && yAxis < 150) {
    Stop();
  }

  if (yAxis > 130 && yAxis < 150) {

    if (xAxis < 135) {
      turnLeft();
      motorSpeedA = map(xAxis, 135, 60, 0, 255);
      motorSpeedB = map(xAxis, 135, 60, 0, 255);
    }

    if (xAxis<251 && xAxis > 145) {
      turnRight();
      motorSpeedA = map(xAxis, 145, 220, 100, 255);
      motorSpeedB = map(xAxis, 145, 220, 0, 255);
    }

  } else {

    if (xAxis > 130 && xAxis < 150) {

      if (yAxis < 130) {
        forward();
      }
      if (yAxis<251 && yAxis > 150) {
        backward();
      }

      if (yAxis < 130) {
        motorSpeedA = map(yAxis, 130, 60, 0, 255);
        motorSpeedB = map(yAxis, 130, 60, 0, 255);
      }

      if (yAxis > 150) {
        motorSpeedA = map(yAxis, 150, 220, 0, 255);
        motorSpeedB = map(yAxis, 150, 220, 0, 255);
      }

    } else {

      if (yAxis < 130) {
        forward();
      }
      if (yAxis<251 && yAxis > 150) {
        backward();
      }

      if (xAxis < 130) {
        motorSpeedA = map(xAxis, 130, 60, 255, 50);
        motorSpeedB = 255;
      }

      if (xAxis<251 && xAxis > 150) {
        motorSpeedA = 255;
        motorSpeedB = map(xAxis, 150, 220, 255, 50);
      }

    }
  }




  // Makes sure we receive corrent values

  

  //Serial.print(motorSpeedA);
  //Serial.print(",");
  //Serial.println(motorSpeedA);

  
}


void forward() {
  Serial.println("forward");
  analogWrite(enA, motorSpeedA); // Send PWM signal to motor A
  analogWrite(enB, motorSpeedB); // Send PWM signal to motor B
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void backward() {
  Serial.println("backward");
  analogWrite(enA, motorSpeedA); // Send PWM signal to motor A
  analogWrite(enB, motorSpeedB); // Send PWM signal to motor B
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void turnLeft() {
  Serial.println("turnLeft");
  analogWrite(enA, motorSpeedA); // Send PWM signal to motor A
  analogWrite(enB, motorSpeedB); // Send PWM signal to motor B
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void turnRight() {
  Serial.println("turnRight");
  analogWrite(enA, motorSpeedA); // Send PWM signal to motor A
  analogWrite(enB, motorSpeedB); // Send PWM signal to motor B
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void Stop() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  // Serial.println("stop");
}
```

# Final Milestone
My final milestone is the seed mechanism. I CAD'ed and 3D printed a container and a sliding piece for the bottom. When the servo which is controlled via the app spins and  the 2 holes on the the container and sliding piece line up, the seed can fall out. I atteched the mechanism using mounting tape and wood which I drilled and sawed. I really ejoyed learning how to saw and drill for this mechanism. I struggled with getting the seeds falling out but I fixed that by making the hole larger. 

[![Final Milestone](https://img.youtube.com/vi/T5iJ-yrfPIw/maxresdefault.jpg)](https://www.youtube.com/watch?v=T5iJ-yrfPIw "Final Milestone")

# Second Milestone
My second milestone is the watering mechanism for the plants. To reach this milestone, I CAD'ed and laser cut an acrylic box which I glued together with caulk. This served as the water container which has a submersible water pump. My app sends a signal to the bluetooth module which sends the signal to the arduino. The arduino sends a signal to the relay which controlls the submerisible water pump. I really enjoyed learning how to CAD and use Fusion360. I struggled with water siphoning out but I fixed that buy pointing the pipe upwards. For my next milestone, I will be adding the seed planting mechanism. 


[![Second Milestone](https://img.youtube.com/vi/VPIVSs_u_t4/maxresdefault.jpg)](https://www.youtube.com/watch?v=VPIVSs_u_t4 "Second Milestone")
# First Milestone
  

 For my first milestone, I managed to get the RC movement mechanism of the tank working. I used an Arduino to control the robot. The 2 DC motors are controlled by the L298N motor controller. The whole robot is controlled via an app I made. The app conveys the instructions to the Bluetooth module which transmits the info to the Arduino. The Arduino sends the signal to the motor controller which controls the DC motor. I enjoyed learning how to code and build the app. I also enjoyed soldering all the connections. I struggled with getting the Bluetooth module to work. It often stopped working. For my next milestone, I will be adding the watering mechanism.


[![First Milestone](https://img.youtube.com/vi/d7-dR8qXSpk/maxresdefault.jpg)](https://www.youtube.com/watch?v=d7-dR8qXSpk "First Milestone")


# Starter Project
  

My starter project was a motor powered by a motor controller. It was controlled via an Arduino. A motion sensor gave the input for when to move. For example when I moved my hand in front of the sensor, the motor spun for 5 seconds and stopped. I wasted a lot of time troublehshooting because the motor controller turned out to be dead. I enjoyed testing and coding it. I look forward to my intensive project.

[![Starter Project](https://i3.ytimg.com/vi/lcGB89pQ1Dg/hqdefault.jpg)](https://www.youtube.com/watch?v=lcGB89pQ1Dg "Starter Project")
