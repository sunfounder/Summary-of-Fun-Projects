.. _rfid_access7.0_:

RFID Access7.0
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
        - Arduino board
        - PURCHASE LINK
    *   - Elite Explorer Kit
        - Arduino Uno R4 WiFi
        - |link_elite_buy|
    *   - Ultimate Starter Kit for Arduino Mega 2560
        - Arduino Mega 2560
        - |link_mega_2560_kit_buy|

Course Introduction
------------------------

In this lesson, we’ll build a 7.0 access-control system using the MFRC522 module, I2C LCD, a digital servo motor, buzzer, LEDs and flame monitor. 

.. .. raw:: html

..  <iframe width="700" height="394" src="https://www.youtube.com/embed/NTM_WniSmV4?si=Nauil7ME5hYNHTgY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
        - Flame Sensor Module
        - 1
        - |link_flame_buy|
    *   - 6
        - Active Buzzer
        - 1
        - 
    *   - 7
        - MFRC522 Module
        - 1
        - |link_mfrc522_module_buy|
    *   - 8
        - Power Supply Module
        - 1
        - |link_power_buy|
    *   - 9
        - Digital Servo Motor
        - 1
        - |link_motor_buy|
    *   - 10
        - I2C LCD 1602
        - 1
        - |link_i2clcd1602_buy|
    *   - 11
        - LED
        - 2
        - |link_led_buy|
    *   - 12
        - 1kΩ resistor
        - 2
        - |link_resistor_buy|

**Wiring**

.. image:: img/RFID_Access7.0_bb.png

**Common Connections:**

* **MFRC522 Module**

  - **IRQ:** Connect to **7** on the ESP32.
  - **SDA:** Connect to **6** on the ESP32.
  - **SCK:** Connect to **5** on the ESP32.
  - **MOSI:** Connect to **4** on the ESP32.
  - **MISO:** Connect to **3** on the ESP32.
  - **GND:** Connect to breadboard’s negative power bus.
  - **RST:** Connect to **2** on the ESP32.
  - **3.3V:** Connect to breadboard’s passive power bus.

* **Flame Sensor Module**

  - **D0:** Connect to **12** on the ESP32.
  - **GND:** Connect to **GND** on the ESP32.
  - **VCC:** Connect to **5V** on the ESP32.

* **Active Buzzer**

  - **＋:** Connect to breadboard’s red power bus. 
  - **－:** Connect to breadboard’s negative power bus.

* **Digital Servo Motor**

  - Connect to breadboard’s positive power bus.
  - Connect to breadboard’s negative power bus.
  - Connect to  **9** on the Arduino.

* **I2C LCD 1602**

  - **SDA:** Connect to **A4** on the Arduino.
  - **SCL:** Connect to **A5** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * The ``RFID1`` library is used here. You can click here :download:`RFID1.zip </_static/RFID1.zip>` to download it.
    * Don't forget to select the board(Arduino UNO R4 WIFI) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      #include <rfid1.h>
      #include <Servo.h>
      #include <LiquidCrystal_I2C.h>

      #define ID_LEN 4   // UID length of the RFID card

      RFID1 rfid;
      Servo myServo;
      LiquidCrystal_I2C lcd(0x27, 16, 2);  // I2C LCD (16x2)

      // ==== Pin Definitions ====
      const int servoPin  = 9;    // Servo signal pin
      const int ledPin    = 10;   // LED indicator pin
      const int buzzerPin = 11;   // Active buzzer pin
      const int flamePin  = 12;   // Flame sensor pin (LOW means fire detected)

      // ==== Authorized RFID UID ====
      uchar userId[ID_LEN] = {0x33, 0xF8, 0xB8, 0x1A};
      uchar userIdRead[ID_LEN];  // Buffer to store scanned UID

      // ==== System State Flags ====
      bool cardAction = false;      // Door open/close in progress
      unsigned long cardTimer = 0;  // Hold-open timer

      bool fireAlert = false;       // Fire detected state
      bool fireRecovering = false;  // Fire ended, waiting to reset
      unsigned long fireRecoverStart = 0;

      bool fireLCDLocked = false;   // Prevent LCD from refreshing repeatedly during fire alert

      // ==== Servo Control Variables ====
      int targetPos = 0;    // Target servo angle (0° or 90°)
      int currentPos = 0;   // Current gradual servo position

      // ==== LCD State ====
      String lcdState = "";  // Tracks which page is currently displayed

      // ==== Beep Control ====
      unsigned long beepTimer = 0;
      int beepStage = -1;
      bool waitingDeniedFinish = false;

      // Additional delay for denied message (0.5 seconds)
      unsigned long deniedHoldStart = 0;
      const unsigned long deniedHoldDuration = 500;


      // ======================= Servo Control =======================
      // Smooth servo movement without blocking code
      void setServoAngle(int angle) {
        targetPos = constrain(angle, 0, 90);
      }

      void servoSmoothRun() {
        static unsigned long last = 0;
        if (millis() - last >= 15) {          // Move every 15ms
          last = millis();
          if (currentPos < targetPos) currentPos++;
          else if (currentPos > targetPos) currentPos--;
          myServo.write(currentPos);
        }
      }


      // ======================= Buzzer & LED =======================
      // One short beep + LED on for success
      void startSuccessBeep() {
        beepStage = 0;
        beepTimer = millis();
        digitalWrite(ledPin, HIGH);  // LED stays on until door closes
      }

      // 4 fast beeps for access denied
      void startDeniedBeep() {
        beepStage = 10;
        beepTimer = millis();
        waitingDeniedFinish = true;
        deniedHoldStart = 0;
      }

      // Handles both success beep and denied beep patterns
      void handleBeep() {
        unsigned long now = millis();

        // One short success beep
        if (beepStage == 0) {
          digitalWrite(buzzerPin, HIGH);
          if (now - beepTimer >= 120) {
            digitalWrite(buzzerPin, LOW);
            beepStage = -1;
          }
        }

        // 4 quick denied beeps (LED + buzzer blink)
        else if (beepStage >= 10 && beepStage < 18) {
          bool on = (beepStage % 2 == 0);
          digitalWrite(buzzerPin, on ? HIGH : LOW);
          digitalWrite(ledPin,    on ? HIGH : LOW);

          if (now - beepTimer >= 120) {
            beepStage++;
            beepTimer = now;
          }

          if (beepStage == 18) {
            digitalWrite(buzzerPin, LOW);
            digitalWrite(ledPin, LOW);
            beepStage = -1;
          }
        }
      }


      // ======================= Fire Alarm Beeping =======================
      // Non-blocking fire alert beep + LED flashing
      void beepAlarmNonBlock() {
        static unsigned long t = 0;
        static bool on = false;

        if (millis() - t >= 120) {   // Toggle every 120ms
          t = millis();
          on = !on;
          digitalWrite(buzzerPin, on ? HIGH : LOW);
          digitalWrite(ledPin,    on ? HIGH : LOW);
        }
      }


      // ======================= RFID Functions =======================
      void getId() {
        uchar status, str[MAX_LEN];
        status = rfid.anticoll(str);
        if (status == MI_OK) {
          for (int i = 0; i < ID_LEN; i++) userIdRead[i] = str[i];
          rfid.halt();  // Stop reading the same card repeatedly
        }
      }

      bool idVerify() {
        for (int i = 0; i < ID_LEN; i++)
          if (userIdRead[i] != userId[i]) return false;
        return true;
      }

      void clearBuffer() {
        for (int i = 0; i < ID_LEN; i++) userIdRead[i] = 0;
      }


      // ======================= LCD Pages =======================
      // Prevent LCD flickering by checking lcdState before updating

      void showNormal() {
        if (lcdState == "normal") return;
        lcdState = "normal";
        fireLCDLocked = false;  // Allow normal updates again after fire
        lcd.clear();
        lcd.setCursor(0, 0); lcd.print("Ready to Scan");
        lcd.setCursor(0, 1); lcd.print("Present Your Card");
      }

      void showAccessGranted() {
        lcdState = "grant";
        lcd.clear();
        lcd.setCursor(0, 0); lcd.print("Entry Approved");
        lcd.setCursor(0, 1); lcd.print("Please Proceed");
      }

      void showAccessDenied() {
        lcdState = "denied";
        lcd.clear();
        lcd.setCursor(0, 0); lcd.print("Invalid Card");
        lcd.setCursor(0, 1); lcd.print("Access Denied");
      }

      void showFireAlert() {
        // Lock LCD during fire to prevent repeating refresh
        if (fireLCDLocked) return;
        fireLCDLocked = true;

        lcdState = "fire";
        lcd.clear();
        lcd.setCursor(0, 0); lcd.print("Fire Alert!");
        lcd.setCursor(0, 1); lcd.print("Evacuate Now!");
      }


      // ======================= Setup =======================
      void setup() {

        rfid.begin(7, 5, 4, 3, 6, 2);
        rfid.init();

        pinMode(buzzerPin, OUTPUT);
        pinMode(ledPin, OUTPUT);
        pinMode(flamePin, INPUT);

        myServo.attach(servoPin);
        myServo.write(0);

        lcd.init();
        lcd.backlight();
        showNormal();
      }


      // ======================= Main Loop =======================
      void loop() {

        bool fireDetected = (digitalRead(flamePin) == LOW);
        bool allowRFID = true;

        // ================= FIRE DETECTED =================
        if (fireDetected) {

          fireAlert = true;
          fireRecovering = false;

          setServoAngle(90);       // Open door for emergency exit
          beepAlarmNonBlock();     // Flash LED + buzzer
          showFireAlert();         // Stable LCD text during fire

          allowRFID = false;
        }

        // ================= FIRE RECOVERY =================
        else if (fireAlert) {

          // Fire just stopped
          if (!fireRecovering) {
            fireRecovering = true;
            fireRecoverStart = millis();

            digitalWrite(ledPin, HIGH);  // LED stays on until door closes
            digitalWrite(buzzerPin, LOW);
          }

          // Keep waiting 1.5 seconds before returning to normal
          if (millis() - fireRecoverStart < 1500) {
            allowRFID = false;
          }
          else {
            fireAlert = false;
            fireRecovering = false;
            setServoAngle(0);      // Close door after danger ends
            allowRFID = true;
          }
        }


        // ================= RFID SCANNING =================
        if (allowRFID && !cardAction && !waitingDeniedFinish) {

          uchar status, str[MAX_LEN];
          status = rfid.request(PICC_REQIDL, str);

          if (status == MI_OK) {

            getId();

            // ------ Valid Card ------
            if (idVerify()) {
              startSuccessBeep();
              showAccessGranted();
              setServoAngle(90);     // Open the door
              cardAction = true;
              cardTimer = 0;
            }

            // ------ Invalid Card ------
            else {
              startDeniedBeep();
              showAccessDenied();
            }

            clearBuffer();
          }
        }


        // ================= EXTRA DELAY FOR DENIED PAGE =================
        if (waitingDeniedFinish && beepStage == -1) {

          if (deniedHoldStart == 0)
            deniedHoldStart = millis();

          if (millis() - deniedHoldStart >= deniedHoldDuration) {
            waitingDeniedFinish = false;
            showNormal();
          }
        }


        // ================= SUCCESS DOOR FLOW =================
        if (cardAction) {

          // Opening the door smoothly
          if (currentPos < 90) {
            servoSmoothRun();
          }
          else {
            // Hold door open for 1.5 sec
            if (cardTimer == 0) cardTimer = millis();
            if (millis() - cardTimer >= 1500)
              setServoAngle(0);     // Start closing

            servoSmoothRun();
          }
        }


        // ================= DOOR FULLY CLOSED =================
        if (currentPos <= 3 && targetPos == 0) {

          // Close after successful card read
          if (lcdState == "grant") {
            cardAction = false;
            cardTimer = 0;
            digitalWrite(ledPin, LOW);
            showNormal();
          }

          // Close after fire recovery
          if (!fireAlert && !fireRecovering && lcdState == "fire") {
            digitalWrite(ledPin, LOW);
            showNormal();
          }
        }


        // Background tasks
        handleBeep();
        servoSmoothRun();
      }
