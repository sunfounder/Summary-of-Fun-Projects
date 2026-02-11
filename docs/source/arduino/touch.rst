.. _touch:

Touch Sensor Module
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

In this lesson, we will learn how to use the Touch Sensor Module with Arduino.

.. raw:: html

 <iframe width="700" height="394" src="https://www.youtube.com/embed/0PVVLL4yH_M?si=0IAqScQ_-kvZ9QXS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - Arduino UNO R4 WIFI
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
        - Touch Sensor Module
        - 1
        - |link_touch_buy|

**Wiring**

.. image:: img/12_Touch_Module_bb.png

**Common Connections:**

* **Touch Sensor Module**

  - **SIG:** Connect to **7** on the Arduino.
  - **GND:** Connect to **GND** on the Arduino.
  - **VCC:** Connect to **5V** on the Arduino.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * Don't forget to select the board(Arduino UNO R4 Minima/WIFI) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      /*
        A simple code to read input from a touch sensor
        connected to pin 7 of an Arduino Uno board.
        If the sensor is touched, the built-in LED is turned on
        and a message is printed to the serial monitor.
        If the sensor is not touched, the LED is turned off
        and a different message is printed.

        Board: Arduino Uno R3 (or R4)
        Component: Touch Sensor
      */

      // Define the pin used for the touch sensor
      const int sensorPin = 7;

      void setup() {
        pinMode(sensorPin, INPUT);     // Set the sensor pin as input
        Serial.begin(9600);            // Start the serial communicatio
      }
      void loop() {
        // Check sensor state and output result
        if (digitalRead(sensorPin) == HIGH) {  
          Serial.println("Touch detected!");
        } else {
          Serial.println("No touch detected...");
        }
        delay(100);  // Short pause between reads
      }

