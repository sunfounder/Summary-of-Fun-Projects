.. _centrifugal_pump:

Centrifugal Pump
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

In this lesson, we will learn how to use the Centrifugal Pump with Arduino.

.. raw:: html

  <iframe width="700" height="394" src="https://www.youtube.com/embed/3l_ziypN7RU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - |link_unor4_buy|
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
        - Centrifugal_Pump
        - 1
        - 

**Wiring**

.. image:: img/21_Centrifugal_Pump_bb.png

**Common Connections:**

* **L9110 Motor Driver Module**

  - **GND:** Connect to **GND** on the Arduino.
  - **VCC:** Connect to **5V** on the Arduino.
  - **B-1B:** Connect to **10** on the Arduino.
  - **B-1A:** Connect to **9** on the Arduino.

* **Centrifugal Pump**

  -  Connect to L9110 Motor Driver Module **Motor B**.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * Don't forget to select the board(Arduino UNO R4 Minima) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      /*
        This code defines and initializes two pins for a centrifugal pump and sets 
        the speed of one of the pump pins to HIGH and the other to LOW, causing the 
        pump to spin in one direction. After 5 seconds, the pump turns off.

        Board: Arduino Uno R3 (or R4)
        Component: Motor and L9110 motor control board
      */

      // Define the pump pins
      const int motorB_1A = 9;
      const int motorB_2A = 10;


      void setup() {
        pinMode(motorB_1A, OUTPUT);  // set pump pin 1 as output
        pinMode(motorB_2A, OUTPUT);  // set pump pin 2 as output

        digitalWrite(motorB_1A, HIGH); 
        digitalWrite(motorB_2A, LOW);

        delay(5000);// wait for 5 seconds
        
        digitalWrite(motorB_1A, LOW);  // turn off the pump
        digitalWrite(motorB_2A, LOW);
      }

      void loop() {

      }

