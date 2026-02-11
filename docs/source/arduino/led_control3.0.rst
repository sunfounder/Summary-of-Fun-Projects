.. _led_control3.0:

LED control 3.0
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

.. image:: img/elite_explore_kit.png
   :width: 100%
   :align: center
   :target: https://www.sunfounder.com/collections/arduino-kits-bundles/products/sunfounder-elite-explorer-kit-with-official-arduino-uno-r4-wifi?ref=jbzmncle

.. raw:: html

   <br><br>

.. list-table::
   :widths: 20 20 20
   :header-rows: 1

   * - Name
     - Includes Arduino board
     - PURCHASE LINK
   * - Ultimate Sensor Kit
     - Arduino Uno R4 Minima
     - |link_ultimate_sensor_buy|
   * - Elite Explorer Kit
     - Arduino Uno R4 WiFi
     - |link_elite_buy|
   * - 3 in 1 Ultimate Starter Kit
     - Arduino Uno R4 Minima
     - |link_arduinor4_buy|
   * - Universal Maker Sensor Kit
     - ×
     - |link_umsk_buy|

Course Introduction
------------------------

In this lesson, you will use Arduino together with two Ultrasonic Sensor Modules and a row of LEDs to create an automatic directional light-flow effect.

When the left sensor detects an object, the LEDs light up in a forward flowing pattern. When the right sensor is triggered, the LEDs flow in the opposite direction.

Each sensor controls one direction of the animation, allowing the LEDs to respond dynamically based on which side an obstacle is detected.

.. raw:: html

  <iframe width="700" height="394" src="https://www.youtube.com/embed/NFj_RJYLQJw?si=XEEU-fi1eiNxXTmU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - Arduino UNO R4 Minima
        - 1
        - |link_unor4_wifi_buy|
    *   - 2
        - USB Type-C cable
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
        - 1kΩ resistor
        - Several
        - |link_resistor_buy|
    *   - 6
        - Ultrasonic Sensor Module
        - 2
        - |link_ultrasonic_buy|
    *   - 7
        - LED
        - Several
        - |link_led_buy|

**Wiring**

.. image:: img/LED_Control3.0_bb.png

**Common Connections:**

* **LED**

  - Connect the LEDs **cathode** to  the negative power bus on the breadboard, and the LEDs **anode** to **1kΩ resistor** then to **5** to **10** on the Arduino.

* **Ultrasonic Sensor Module Right**

  - **Trig:** Connect to **11** on the Arduino.
  - **Echo:** Connect to **12** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

* **Ultrasonic Sensor Module Left**

  - **Trig:** Connect to **3** on the Arduino.
  - **Echo:** Connect to **4** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * Don't forget to select the board(Arduino UNO R4 Minima) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      // ================= Ultrasonic pins =================
      // Left ultrasonic sensor
      const int trigLeft  = 3;
      const int echoLeft  = 4;

      // Right ultrasonic sensor
      const int trigRight = 11;
      const int echoRight = 12;

      // ================= LED pins =================
      int ledPins[] = {5, 6, 7, 8, 9, 10};
      int ledCount = 6;

      // ================= Distance levels (cm) =================
      int farLimit  = 15;   // slow
      int midLimit  = 10;   // medium
      int nearLimit = 5;    // fast

      // ================= LED speed =================
      int slowDelay = 100;
      int midDelay  = 60;
      int fastDelay = 30;

      void setup() {
        // Set ultrasonic pins
        pinMode(trigLeft, OUTPUT);
        pinMode(echoLeft, INPUT);
        pinMode(trigRight, OUTPUT);
        pinMode(echoRight, INPUT);

        // Set LED pins
        for (int i = 0; i < ledCount; i++) {
          pinMode(ledPins[i], OUTPUT);
        }

        Serial.begin(9600);
      }

      void loop() {
        // Read distance from both ultrasonic sensors
        float leftDistance  = readDistance(trigLeft, echoLeft);
        float rightDistance = readDistance(trigRight, echoRight);

        // Get LED speed based on distance
        int leftSpeed  = getSpeedByDistance(leftDistance);
        int rightSpeed = getSpeedByDistance(rightDistance);

        // Left sensor → LEDs flow left to right
        if (leftSpeed > 0) {
          flowForward(leftSpeed);
        }

        // Right sensor → LEDs flow right to left
        if (rightSpeed > 0) {
          flowBackward(rightSpeed);
        }

        delay(80); // short pause between measurements
      }

      // ================= Functions =================

      // Convert distance to LED speed
      int getSpeedByDistance(float d) {
        if (d <= 0) return -1;       // invalid reading
        if (d < nearLimit) return fastDelay;
        if (d < midLimit)  return midDelay;
        if (d < farLimit)  return slowDelay;
        return -1;                  // no trigger
      }

      // Measure distance using ultrasonic sensor
      float readDistance(int trigPin, int echoPin) {
        digitalWrite(trigPin, LOW);
        delayMicroseconds(2);
        digitalWrite(trigPin, HIGH);
        delayMicroseconds(10);
        digitalWrite(trigPin, LOW);

        long duration = pulseIn(echoPin, HIGH, 25000);
        if (duration == 0) return -1; // no echo

        return duration / 58.0;       // convert to cm
      }

      // LEDs flow from left to right
      void flowForward(int d) {
        for (int i = 0; i < ledCount; i++) {
          digitalWrite(ledPins[i], HIGH);
          delay(d);
          if (i > 0) {
            digitalWrite(ledPins[i - 1], LOW);
          }
        }
        digitalWrite(ledPins[ledCount - 1], LOW);
      }

      // LEDs flow from right to left
      void flowBackward(int d) {
        for (int i = ledCount - 1; i >= 0; i--) {
          digitalWrite(ledPins[i], HIGH);
          delay(d);
          if (i < ledCount - 1) {
            digitalWrite(ledPins[i + 1], LOW);
          }
        }
        digitalWrite(ledPins[0], LOW);
      }
