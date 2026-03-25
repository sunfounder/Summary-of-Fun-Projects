.. _smart_stop5:

Smart Stop 5.0
==============================================================

.. note::
  
  🌟 Welcome to the SunFounder Facebook Community! Whether you're into Raspberry Pi, Arduino, or ESP32, you'll find inspiration, help ideas here.
   
  - ✅ Be the first to get free learning resources. 
   
  - ✅ Stay updated on new products & exclusive giveaways. 
   
  - ✅ Share your creations and get real feedback.
   
  * 👉 Need faster updates or support? Click [|link_sf_facebook|] join our Facebook community 

  * 👉 Or join our WhatsApp group: Click [|link_sf_whatsapp|]
   
Kit purchase
------------------------

Looking for parts? Check out our all-in-one kits below — packed with components, beginner-friendly guides, and tons of fun.

.. image:: img/ultimate_sensor_kit.png
   :width: 100%
   :align: center
   :target: https://www.sunfounder.com/collections/arduino-kits-bundles/products/sunfounder-ultimate-sensor-kit-with-original-arduino-uno-r4-minima?ref=jbzmncle

.. raw:: html

   <br><br>

.. list-table::
   :widths: 20 20 20
   :header-rows: 1

   * - Name
     - Includes Arduino board
     - PURCHASE LINK
   * - Elite Explorer Kit
     - Arduino Uno R4 WiFi
     - |link_elite_buy|
   * - 3 in 1 Ultimate Starter Kit
     - Arduino Uno R4 Minima
     - |link_arduinor4_buy|

Course Introduction
------------------------

In this lesson, you’ll learn how to use an L9110 Motor Driver Module, an Ultrasonic Sensor Module, an I2C LCD Module, an Buzzer module and a TT motor with the Arduino UNO R4 to create a Smart Stop 5.0 system.

As the obstacle gets closer to the Ultrasonic Sensor Module, the LCD screen displays the distance to obstacles and the servo speed.

.. .. raw:: html

.. <iframe width="700" height="394" src="https://www.youtube.com/embed/_WoZojtIqF8?si=eiE2Klw8YBnm7pzr" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

.. note::

  If this is your first time working with an Arduino project, we recommend downloading and reviewing the basic materials first.
  
  * :ref:`install_arduino`
  * :ref:`introduce_arduino`

**Required Components**

In this project, we need the following components:

.. list-table::
    :widths: 5 20 5 20
    :header-rows: 1

    *   - SN
        - COMPONENT INTRODUCTION	
        - QUANTITY
        - PURCHASE LINK

    *   - 1
        - Arduino UNO R4 Minima/Arduino UNO R4 WIFI
        - 1
        - |link_unor4_wifi_buy|
    *   - 2
        - USB Cable
        - 1
        - 
    *   - 3
        - Breadboard
        - 1
        - |link_breadboard_buy|
    *   - 4
        - Wires
        - Several
        - |link_wires_buy|
    *   - 5
        - L9110 Motor Driver Module
        - 1
        - 
    *   - 6
        - Ultrasonic Sensor Module
        - 1
        - |link_ultrasonic_buy|
    *   - 7
        - TT Motor
        - 1
        - 
    *   - 8
        - Buzzer Modudle
        - 1
        - |link_buzzer_module_buy|
    *   - 9
        - I2C LCD 1602
        - 1
        - |link_i2clcd1602_buy|

**Wiring**

.. image:: img/Smart_Stop5.0_bb.png

**Common Connections:**

* **Ultrasonic Sensor Module**

  - **Trig:** Connect to **10** on the Arduino.
  - **Echo:** Connect to **11** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

* **TT Motor**

  -  Connect to **MOTOR B** on the L9110 Motor Driver Module.

* **L9110 Motor Driver Module**

  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.
  - **B-1B:** Connect to **2** on the Arduino.
  - **B-1A:** Connect to **3** on the Arduino.

* **Buzzer Module**

  - **I/0:** Connect to **4** on the Arduino.
  - **＋:** Connect to breadboard’s red power bus. 
  - **－:** Connect to breadboard’s negative power bus.

* **I2C LCD 1602**

  - **SDA:** Connect to **A4** on the Arduino.
  - **SCL:** Connect to **A5** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * To install the library, use the Arduino Library Manager and search for **LiquidCrystal I2C** and install it.
    * Don't forget to select the board(Arduino UNO R4 WiFi) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      #include <Wire.h>
      #include <LiquidCrystal_I2C.h>

      // I2C LCD (16x2)
      LiquidCrystal_I2C lcd(0x27, 16, 2);

      // Motor driver pins (L9110)
      const int motorPinA = 3;
      const int motorPinB = 2;

      // Ultrasonic sensor pins
      const int trigPin = 10;
      const int echoPin = 11;

      // Passive buzzer pin
      const int buzzerPin = 4;

      // Distance thresholds (cm)
      const int STOP_DISTANCE = 5;
      const int SLOW_DISTANCE = 20;

      // Motor speed range (PWM)
      const int MIN_MOTOR_PWM = 75;
      const int MAX_MOTOR_PWM = 255;

      // Variables for non-blocking buzzer
      unsigned long previousBeepTime = 0;
      bool beepState = false;

      void setup() {

        // Set motor pins
        pinMode(motorPinA, OUTPUT);
        pinMode(motorPinB, OUTPUT);

        // Set ultrasonic pins
        pinMode(trigPin, OUTPUT);
        pinMode(echoPin, INPUT);

        // Set buzzer pin
        pinMode(buzzerPin, OUTPUT);

        // Fix motor direction
        digitalWrite(motorPinB, LOW);

        // Initialize LCD
        lcd.init();
        lcd.backlight();

        // Startup message
        lcd.setCursor(0, 0);
        lcd.print("Smart Stop");
        lcd.setCursor(0, 1);
        lcd.print("Ready...");
        delay(1000);
        lcd.clear();
      }

      void loop() {

        // Read distance (cm)
        int distance = readDistance();

        // Calculate motor speed
        int speed = calculateSpeed(distance);

        // Control motor
        controlMotor(speed);

        // Update buzzer and display
        updateBuzzer(distance);
        updateLCD(distance);

        delay(50);
      }

      // Measure distance using ultrasonic sensor
      int readDistance() {
        long duration;

        // Send trigger pulse
        digitalWrite(trigPin, LOW);
        delayMicroseconds(2);
        digitalWrite(trigPin, HIGH);
        delayMicroseconds(10);
        digitalWrite(trigPin, LOW);

        // Read echo time (with timeout)
        duration = pulseIn(echoPin, HIGH, 25000);

        // If no signal, return far distance
        if (duration == 0) return 100;

        // Convert to cm
        return duration / 58;
      }

      // Convert distance to motor speed
      int calculateSpeed(int distance) {

        // Stop if too close
        if (distance <= STOP_DISTANCE) {
          return 0;
        }

        // Slow down in warning range
        if (distance <= SLOW_DISTANCE) {
          return map(distance,
                    STOP_DISTANCE,
                    SLOW_DISTANCE,
                    MIN_MOTOR_PWM,
                    MAX_MOTOR_PWM);
        }

        // Full speed when safe
        return MAX_MOTOR_PWM;
      }

      // Control motor speed (PWM)
      void controlMotor(int speed) {
        analogWrite(motorPinA, speed);
        digitalWrite(motorPinB, LOW);
      }

      // Control buzzer without delay()
      void updateBuzzer(int distance) {
        unsigned long currentTime = millis();

        // Fast beep when very close
        if (distance <= STOP_DISTANCE) {
          if (currentTime - previousBeepTime >= 100) {
            previousBeepTime = currentTime;
            beepState = !beepState;
            beepState ? tone(buzzerPin, 2000) : noTone(buzzerPin);
          }
        }
        // Slow beep in warning range
        else if (distance <= SLOW_DISTANCE) {
          if (currentTime - previousBeepTime >= 500) {
            previousBeepTime = currentTime;
            beepState = !beepState;
            beepState ? tone(buzzerPin, 800) : noTone(buzzerPin);
          }
        }
        // No sound when safe
        else {
          noTone(buzzerPin);
          beepState = false;
        }
      }

      // Update LCD display
      void updateLCD(int distance) {

        // Line 1: system state
        lcd.setCursor(0, 0);

        if (distance <= STOP_DISTANCE) {
          lcd.print("STOP            ");
        }
        else if (distance <= SLOW_DISTANCE) {
          lcd.print("SLOW            ");
        }
        else {
          lcd.print("SAFE            ");
        }

        // Line 2: distance value
        lcd.setCursor(0, 1);
        lcd.print("Dist:");
        lcd.print(distance);
        lcd.print("cm   ");
      }