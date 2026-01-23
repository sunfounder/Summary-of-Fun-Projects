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
   
Kit purchase
------------------------
  
Looking for parts? Check out our all-in-one kits below — packed with components, beginner-friendly guides, and tons of fun.

.. image:: img/esp32_kit.png
   :width: 100%
   :align: center
   :target: https://www.sunfounder.com/products/sunfounder-esp32-ultimate-starter-kit-with-esp32-camera-extension-board-battery?_pos=1&_sid=7dcb30fb6&_ss=r&ref=jbzmncle

.. raw:: html

   <br><br>

.. list-table::
   :widths: 20 20 20
   :header-rows: 1

   * - Name
     - Includes ESP32 board
     - PURCHASE LINK
   * - ESP32 Ultimate Starter Kit
     - ESP32 WROOM 32E +
     - |link_esp32_kit_buy|
   * - Universal Maker Sensor Kit
     -
     - |link_umsk_buy|

Course Introduction
------------------------

In this lesson, you’ll learn how to build a simple water level indicator using an analog water level sensor and 8 LEDs with the Arduino Nano ESP32. 

As the water level rises, more LEDs light up to visually display the current level in real time.

.. raw:: html

  <iframe width="700" height="394" src="https://www.youtube.com/embed/fVJNkYtDFIE?si=twzFrQ3tNF31DFoj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

.. note::

  If this is your first time working with an ESP32 project, we recommend downloading and reviewing the basic materials first.
  
  * :ref:`install_arduino`
  * :ref:`introduce_arduino`
  * :ref:`install_esp32`

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
        - Arduino Nano ESP32
        - 1
        - 
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

  - **A:** Connect to **A0** on the ESP32.
  - **G:** Connect to breadboard’s negative power bus.
  - **V:** Connect to **3.3V** on the ESP32 Extension Board.

* **LEDS**

  - Connect the LED **cathode** to a **1KΩ resistor** then to the negative power bus on the breadboard, and the **anode** to the **D2~D9** on the ESP32.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * Don't forget to select the board(Arduino Nano ESP32) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      // LED pin array (Dx labels for Arduino Nano ESP32)
      const int ledPins[] = {D2, D3, D4, D5, D6, D7, D8, D9};
      const int numLEDs = 8;

      // Water level sensor analog input pin
      // Use an ADC-capable pin on ESP32
      const int sensorPin = A0;   // On Nano ESP32, A0 is mapped to a valid ADC pin

      void setup() {
        Serial.begin(115200);

        // Set all LED pins as output
        for (int i = 0; i < numLEDs; i++) {
          pinMode(ledPins[i], OUTPUT);
          digitalWrite(ledPins[i], LOW);
        }
      }

      void loop() {
        // Read the water level sensor value (ESP32 ADC range: 0~4095)
        int sensorValue = analogRead(sensorPin);

        Serial.print("Water level analog value: ");
        Serial.println(sensorValue);

        // Map the analog value to number of LEDs (0~8)
        // Adjust 0~1900 according to your actual sensor output range
        int level = map(sensorValue, 0, 1900, 0, numLEDs);
        level = constrain(level, 0, numLEDs);

        // Turn on LEDs according to the water level
        for (int i = 0; i < numLEDs; i++) {
          if (i < level) {
            digitalWrite(ledPins[i], HIGH);
          } else {
            digitalWrite(ledPins[i], LOW);
          }
        }

        delay(500);  // Delay before next update
      }
