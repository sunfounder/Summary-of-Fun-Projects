.. _water_level2.0:

Water Level 2.0
==============================================================

.. note::
  
  🌟 Welcome to the SunFounder Facebook Community! Whether you're into Raspberry Pi, Arduino, or ESP32, you'll find inspiration, help ideas here.
   
  - ✅ Be the first to get free learning resources. 
   
  - ✅ Stay updated on new products & exclusive giveaways. 
   
  - ✅ Share your creations and get real feedback.
   
  * 👉 Need faster updates or support? Click [|link_sf_facebook|] join our Facebook community 

  * 👉 Or join our WhatsApp group: Click [|link_sf_whatsapp|]
   
  * 🎁 Looking for parts?Check out our all-in-one kits below — packed with components, beginner-friendly guides, and tons of fun.
  
  .. list-table::
    :widths: 20 20 20
    :header-rows: 1

    *   - Name	
        - Includes Arduino board
        - PURCHASE LINK
    *   - Elite Explorer Kit
        - Arduino Uno R4 WiFi
        - |link_elite_buy|
    *   - Universal Maker Sensor Kit
        - ×
        - |link_umsk_buy|
    *   - 3 in 1 Ultimate Starter Kit	
        - Arduino Uno R4 Minima
        - |link_arduinor4_buy|

Course Introduction
------------------------

In this lesson, you’ll learn how to build a simple water level indicator using an analog water level sensor and 8 LEDs with the Arduino UNO R4. 

As the water level rises, more LEDs light up to visually display the current level in real time.

.. .. raw:: html

..  <iframe width="700" height="394" src="https://www.youtube.com/embed/yXNe92a6Giw?si=44xzXrcJH05ZQBDP" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - Arduino UNO R4 Minima/Arduino UNO R4 wifi
        - 1
        - |link_arduinor4_buy|
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
        - Water Level Detection Module
        - 1
        - 
    *   - 6
        - Resistor
        - 1KΩ
        - |link_resistor_buy|
    *   - 7
        - LED
        - Several
        - |link_led_buy|

**Wiring**

.. image:: img/water_level2.0_bb.png

**Common Connections:**

* **Water Level Detection Module**

  - **A:** Connect to **A0** on the Arduino.
  - **G:** Connect to breadboard’s negative power bus.
  - **V:** Connect to breadboard’s red power bus.

* **LEDS**

  - Connect the LED **cathode** to a **1KΩ resistor** then to the negative power bus on the breadboard, and the **anode** to the **2~9** on the Arduino.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * Don't forget to select the board(Arduino UNO R4 Minima/WIFI) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      // RGB LED pins
      const int redPin = 9;
      const int greenPin = 10;
      const int bluePin = 11;

      // Potentiometer pin
      const int potPin = A0;

      void setup() {
        pinMode(redPin, OUTPUT);
        pinMode(greenPin, OUTPUT);
        pinMode(bluePin, OUTPUT);
        Serial.begin(9600);
      }

      void loop() {
        // Read potentiometer value (0–1023)
        int potValue = analogRead(potPin);
        Serial.println(potValue);

        // Map the potentiometer value to 0–765 range for color blending
        int range = map(potValue, 0, 1023, 0, 765);

        int r = 0, g = 0, b = 0;

        // Blend RGB colors based on range
        if (range <= 255) {
          r = 255;
          g = range;
          b = 0;
        } else if (range <= 510) {
          r = 510 - range;
          g = 255;
          b = range - 255;
        } else {
          r = 0;
          g = 765 - range;
          b = 255;
        }

        // Set RGB LED color
        analogWrite(redPin, 255 - r);   // Inverted for common cathode
        analogWrite(greenPin, 255 - g);
        analogWrite(bluePin, 255 - b);

        delay(20);
      }
