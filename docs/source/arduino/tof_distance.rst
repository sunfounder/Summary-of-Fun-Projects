.. _tof_distance:

Tof Distance
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

This Arduino project uses a VL53L0X Time of Flight distance sensor and an OLED display to measure and visualize distance. The sensor continuously measures the distance in millimeters, and the measured value is shown on the OLED screen and printed to the serial monitor. This setup demonstrates how to use a micro LiDAR sensor for precise short-range distance measurement.

.. .. raw:: html
 
..  <iframe width="700" height="394" src="https://www.youtube.com/embed/qlKY4ye4YUE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - Laser Ranging Module
        - 1
        - |link_laser_module_buy|
    *   - 6
        - OLED Display Module
        - 1
        - |link_oled_buy|

**Wiring**

.. image:: img/lidar_guard_bb.png

**Common Connections:**

* **Digital Servo Motor**

  - Connect to breadboard’s positive power bus.
  - Connect to breadboard’s negative power bus.
  - Connect to **7** on the Arduino.

* **Laser Ranging Module**

  - **SDA:** Connect to **A4** on the Arduino.
  - **SCL:** Connect to **A5** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

**Writing the Code**

.. note::

 * Build the circuit.

 * Install the library, use the Arduino Library Manager and search for **Adafruit_VL53L0X** install it.

 * Upload the code to the Arduino board using Arduino IDE.

.. code-block:: arduino

      /*
        This code initializes and runs an Arduino Uno setup that interfaces with a VL53L0X Time of Flight 
        Micro-LIDAR Distance Sensor and an OLED display. It measures the distance in millimeters and displays 
        the distance on the OLED screen. The measurement values are also output on the serial monitor.
        Note: The VL53L0X can handle about 50 - 1200 mm of range distance.

        Board: Arduino Uno R4 (or R3)
        Component: Time of Flight Micro-LIDAR Distance Sensor (VL53L0X) and OLED Display Module
        Library: https://github.com/adafruit/Adafruit_VL53L0X  (Adafruit_VL53L0X by Adafruit)
                https://github.com/adafruit/Adafruit_SSD1306 (Adafruit SSD1306 by Adafruit)  
                https://github.com/adafruit/Adafruit-GFX-Library (Adafruit GFX Library by Adafruit) 
                
      */

      #include <Wire.h>
      #include "Adafruit_VL53L0X.h"
      #include <SPI.h>
      #include <Adafruit_GFX.h>
      #include <Adafruit_SSD1306.h>

      // Initialize the OLED display module with a resolution of 128x64
      Adafruit_SSD1306 display = Adafruit_SSD1306(128, 64, &Wire, -1);

      // Initialize the VL53L0X distance sensor
      Adafruit_VL53L0X lox = Adafruit_VL53L0X();

      void setup() {
        Serial.begin(9600);

        // Start the OLED display with I2C address 0x3C
        display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
        display.display();
        delay(1000);

        // Begin I2C communication
        Wire.begin();

        // Start the VL53L0X distance sensor, halt if initialization fails
        if (!lox.begin()) {
          Serial.println(F("Failed to boot VL53L0X"));
          while (1)
            ;
        }

        // Set OLED display text size and color
        display.setTextSize(3);
        display.setTextColor(WHITE);
      }

      void loop() {
        VL53L0X_RangingMeasurementData_t measure;

        lox.rangingTest(&measure, false);  // pass in 'true' to get debug data printout

        // If there are no phase failures, display the measured distance
        if (measure.RangeStatus != 4) {
          display.clearDisplay();
          display.setCursor(12, 22);
          display.print(measure.RangeMilliMeter);
          display.print("mm");
          display.display();
          Serial.println();
          delay(50);
        } else {
          display.display();
          display.clearDisplay();
          return;
        }
      }