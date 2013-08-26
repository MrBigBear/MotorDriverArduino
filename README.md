MotorDriverArduino
==================

An Arduino Library that provides bi-drectional control of a 2WD robot.


-------------------
Installation:
-------------------

GIT:/MotorDriverArduino/
        |
        |--Motor/
            |
            |-Motor.h
            |-Motor.cpp
        
1. Copy folder "Motor" to <Arduino IDE Folder>/libraries/
    Final directory structure: <Arduino IDE Folder>/libraries/Motor/
                                                                |
                                                                |-Motor.h
                                                                |-Motor.cpp
2. Open Arduino IDE->Sketch->Import Library
3. Navigate to the folder Motor and choose it.

-------------------
Usage:
-------------------

Motor <name>(int pinA1, int pinA2, int pinAE, int pinB1, int pinB2, int pinBE);
    Initialises a Motor Device with name <name>.
    A, B: Motor A and Motor B.
    1, 2: Digital logic output, controls direction of motors.
    E: PWM output, enables/disables motors.
    Debug:
        Default: No debug printed to serial.
    
Motor <name>(int pinA1, int pinA2, int pinAE, int pinB1, int pinB2, int pinBE, int Debug);
    Same as constructor above.
    Debug:
        1 - Serial will print out debug information
        0 - No debug printed to serial
        
To view Serial messages, please set terminal to:
    Baudrate: 115200
    Carriage Return and Newline

-------------------
Example sketch:
-------------------

/* Motor Driver Demo */
/* Allows you to control the motors from serial. Also has a "serial debounce" implemented */

#define LENGTH 6

#include <string.h>
#include <stddef.h>
#include <Motor.h>

/* Set LEN, LA, LB, REN, RA, RB as digital output */
void setup() {
  Serial.begin(115200); // Faster serial Baud rate. Optimisation purpose.
}

int request = 0;
int command = 0;
int param = 0;

Motor motors(2, 4, 5, 3, 7, 8, 1);

void loop() {
  char buffer[LENGTH]; 
  const char delimiters[] = " ,-;:.";

  if (request == 0) {
    Serial.println();  
    Serial.println("Ready. Enter command: ");
    Serial.println("1:Forwards 2:Backwards 3:Left 4:Right");

    while (Serial.read() != -1); // Debouncing feature. Flushes the buffer.
    request = 1;
  }

  if (request == 1) {
    if(Serial.available() > 0){
      Serial.readBytesUntil('\n', buffer, LENGTH);

      command = atoi(strtok(buffer, delimiters));
      param = atoi(strtok(NULL, delimiters));

      request = 0;
    }

    if (request == 0) { // Will only perform a check if a command was read. Optimisation purposes.
      switch (command) {
      case 1:
        Serial.println("Forward");
        Serial.println(param);
        motors.forward(param);
        break;
      case 2:
        Serial.println("Backward");
        Serial.println(param);
        motors.backward(param);
        break;
      case 3:
        Serial.println("Left");
        Serial.println(param);
        motors.left(param);
        break;
      case 4:
        Serial.println("Right");
        Serial.println(param);
        motors.right(param);
        break;
      default:
        Serial.println("Unknown Command!");
        break;
      }
    }
  }
}


