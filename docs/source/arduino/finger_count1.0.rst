.. _finger_count1.0:

Finger Count 1.0
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

This project controls the LED matrix on an Arduino Uno R4 WiFi board based on finger count data received from a Python script. 
The script detects the number of fingers shown to a camera and sends this data to the Arduino via serial communication(USB). 
The Arduino then displays the corresponding number on the LED matrix.

.. raw:: html

 <iframe width="700" height="394" src="https://www.youtube.com/embed/DweLO80QhhU?si=t3BNiADJxvUzZU9K" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

.. note::

  If this is your first time working with an Arduino project, we recommend downloading and reviewing the basic materials first.
  
  * :ref:`install_arduino`
  * :ref:`introduce_arduino`
  * |link_python_down|

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


**Operating Steps**

.. note::

    1. Copy the following code into **Arduino IDE**. 
    2. Use the Arduino Library Manager and search for **ArduinoGraphic** and **Arduino_LED_Matrix** install libraries.
    3. Select the board(Arduino UNO R4 WIFI) and then clicking the **Upload** button.
    4. Then use the Python code ``FingerCountSender`` . You can click here :download:`FingerCountSender.zip </_static/FingerCountSender.zip>` to download it. 
    5. Update the Python script to use the correct serial port(COMx), ensuring it matches the one identified during Arduino setup(COMx).
        .. image:: img/port1.png
    6. Then run the python code to open the camera window.

.. code-block:: arduino

      #include "ArduinoGraphics.h"
      #include "Arduino_LED_Matrix.h"

      ArduinoLEDMatrix matrix;

      char lastChar = '\0';  // Variable to store the last read character

      void setup() {
        // Start serial communication
        Serial.begin(115200);
        Serial.setTimeout(1);

        matrix.begin();

        delay(500);
      }

      void loop() {
        // Check if data is available on the serial port
        if (Serial.available() > 0) {
          // Read one character from serial input
          char c = Serial.read();

          // Check if the new character is different from the last character
          if (c != lastChar) {
            // Update the last character
            lastChar = c;

            // Clear the matrix before displaying the new character
            matrix.clear();

            matrix.beginDraw();

            matrix.stroke(0xFFFFFFFF);  // Set stroke color
            matrix.textFont(Font_5x7);  // Set font
            matrix.beginText(4, 1, 0xFFFFFF);  // Position and color for text
            matrix.print(c);  // Display the character
            matrix.endText();

            matrix.endDraw();
          }

          // Delay to prevent too frequent updates (optional)
          delay(10);
        }
      }



  