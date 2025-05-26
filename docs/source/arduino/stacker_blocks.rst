.. _stacker_blocks:

Stacker Blocks
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
    *   - Ultimate Sensor Kit	
        - Arduino Uno R4 Minima
        - |link_arduinor4_buy|
    *   - Electronic Kit	
        - ×
        - |link_electronic_buy|
    *   - Universal Maker Sensor Kit
        - ×
        - |link_umsk_buy|

Course Introduction
------------------------

In this lesson, you’ll learn how to use a MAX7219 Dot Matrix Module, a button with the Arduino R4 UNO to create a stacker blocks game. 

The MAX7219 Dot Matrix Module will display the game, and players can use the button to control the gameplay in the stacker blocks game.

.. raw:: html

    <iframe width="700" height="394" src="https://www.youtube.com/embed/zlKPKK3Qink" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

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
        - MAX7219 Dot Matrix Module
        - 1
        - |link_martix_buy|
    *   - 6
        - Button
        - 1
        - |link_button_buy|

**Wiring**

.. image:: img/Stacker_Blocks_bb.png

**Common Connections:**

* **MAX7219 Dot Matrix Module**

  - **CLK:** Connect to **5** on the Arduino.
  - **CS:** Connect to **3** on the Arduino.
  - **DIN:** Connect to **6** on the Arduino.
  - **GND:** Connect to breadboard’s negative power bus.
  - **VCC:** Connect to breadboard’s red power bus.

* **Button**

  - Connect to breadboard’s negative power bus.
  - Connect to **11** on the Arduino.

**Writing the Code**

.. note::

    * You can copy this code into **Arduino IDE**. 
    * To install the library, use the Arduino Library Manager and search for **LedControl** and install it.
    * Don't forget to select the board(Arduino UNO R4 Minima) and the correct port before clicking the **Upload** button.

.. code-block:: arduino

      #include "LedControl.h"

      // Declare a LedControl object to control 4 LED matrices
      // (dataPin = 6, clkPin = 5, csPin = 3, number of modules = 4)
      LedControl lc = LedControl(6, 5, 3, 4);

      // Function prototypes
      void updateDisplay();
      void updatePlacedBlockDisplay(int layerIndex);
      bool checkButton();
      void clearMovingBlock();
      void displayMovingBlock();
      void playSuccessSound();
      void playGameOverSound();
      void updateMaxPosition();
      void placeBlock();
      void displayLayer(int level);

      // Constants: each block occupies 2 columns on the display
      const int buttonPin = 11;    // Button pin for user input
      const int buzzerPin = 9;     // Buzzer pin for sound effects (optional)
      const int blockColumns = 2;  // Each block uses 2 columns

      // Global variables
      // The moving range is determined by the block's effective height (H).
      // Allowed top positions are in the range [-H, 7+H].
      int currentWidth = 4;      
      int currentPos = -4;       // Initial top position for the moving block (starts at -currentWidth)
      int direction = 1;         // Movement direction: 1 = moving down, -1 = moving up
      int moveDelay = 150;       // Speed of block movement (in milliseconds)
      bool gameOver = false;     
      unsigned long lastMoveTime = 0; 
      int maxPosition = 0;       // Maximum allowed top position (calculated as 7 + currentWidth)
      int buttonPressCount = 0;  // Counts how many times the button has been pressed
      int currentLayerCount = 0; // Number of layers (blocks) successfully placed

      // Structure for each block layer:
      // Records the block's top position, height (number of lit rows),
      // starting column on the LED matrix, and the number of columns it occupies.
      struct BlockLayer {
        int position;    // Top row index of this layer
        int width;       // Height (number of rows lit) of this layer
        int startCol;    // Starting column on the LED display
        int colWidth;    // Number of columns occupied (always equal to blockColumns, i.e., 2)
      };

      BlockLayer layers[32];  // Array to store up to 32 layers

      // Global refresh: redraw the entire display (used when resetting or on game over)
      void updateDisplay() {
        // Clear all 4 LED modules
        for (int i = 0; i < 4; i++) {
          lc.clearDisplay(i);
        }
        // Draw all placed layers (the fixed blocks)
        for (int i = 0; i < currentLayerCount; i++) {
          int startCol = layers[i].startCol;
          int colWidth = layers[i].colWidth;
          for (int colOffset = 0; colOffset < colWidth; colOffset++) {
            int currentCol = startCol + colOffset;
            // Calculate which LED module and column within that module
            int module = currentCol / 8;
            int col = 7 - (currentCol % 8);  // Note: columns are reversed within each module
            if (module >= 4) continue;  // Skip if out of bounds
            // Draw the block (each layer's height)
            for (int j = 0; j < layers[i].width; j++) {
              int row = layers[i].position + j;
              if (row >= 0 && row < 8) {
                lc.setLed(module, row, col, true);
              }
            }
          }
        }
        // If the game is not over, draw the currently moving block
        if (!gameOver) {
          int startCol = currentLayerCount * blockColumns; // New block's starting column
          int colWidth = blockColumns;
          for (int colOffset = 0; colOffset < colWidth; colOffset++) {
            int currentCol = startCol + colOffset;
            int module = currentCol / 8;
            int col = 7 - (currentCol % 8);
            if (module >= 4) continue;
            for (int j = 0; j < currentWidth; j++) {
              int row = currentPos + j;
              if (row >= 0 && row < 8) {
                lc.setLed(module, row, col, true);
              }
            }
          }
        }
      }

      // Local refresh: update the display for a specific placed layer
      // It clears the designated columns first and then redraws the block for that layer.
      void updatePlacedBlockDisplay(int layerIndex) {
        int startCol = layers[layerIndex].startCol;
        int colWidth = layers[layerIndex].colWidth;
        for (int colOffset = 0; colOffset < colWidth; colOffset++) {
          int currentCol = startCol + colOffset;
          int module = currentCol / 8;
          int col = 7 - (currentCol % 8);
          if (module >= 4) continue;
          // Clear the entire column
          for (int row = 0; row < 8; row++) {
            lc.setLed(module, row, col, false);
          }
          // Redraw only the rows corresponding to this block layer
          for (int j = 0; j < layers[layerIndex].width; j++) {
            int row = layers[layerIndex].position + j;
            if (row >= 0 && row < 8) {
              lc.setLed(module, row, col, true);
            }
          }
        }
      }

      // Debounce and check if the button is pressed
      bool checkButton() {
        if (digitalRead(buttonPin) == LOW) {
          delay(20);  // Short delay to debounce
          if (digitalRead(buttonPin) == LOW) {
            while (digitalRead(buttonPin) == LOW) { }  // Wait until button is released
            return true;
          }
        }
        return false;
      }

      // Display a specific placed layer (by level index) on the LED matrix
      void displayLayer(int level) {
        int startCol = layers[level].startCol;
        int colWidth = layers[level].colWidth;
        for (int colOffset = 0; colOffset < colWidth; colOffset++) {
          int currentCol = startCol + colOffset;
          int module = currentCol / 8;
          int col = 7 - (currentCol % 8);
          if (module >= 4) continue;
          for (int j = 0; j < layers[level].width; j++) {
            int row = layers[level].position + j;
            if (row >= 0 && row < 8) {
              lc.setLed(module, row, col, true);
            }
          }
        }
      }

      // Draw the moving block on the LED matrix
      void displayMovingBlock() {
        int startCol = currentLayerCount * blockColumns;
        int colWidth = blockColumns;
        for (int colOffset = 0; colOffset < colWidth; colOffset++) {
          int currentCol = startCol + colOffset;
          int module = currentCol / 8;
          int col = 7 - (currentCol % 8);
          if (module >= 4) continue;
          for (int j = 0; j < currentWidth; j++) {
            int row = currentPos + j;
            if (row >= 0 && row < 8) {
              lc.setLed(module, row, col, true);
            }
          }
        }
      }

      // Clear the moving block from the display (turn off its LEDs)
      void clearMovingBlock() {
        int startCol = currentLayerCount * blockColumns;
        int colWidth = blockColumns;
        for (int colOffset = 0; colOffset < colWidth; colOffset++) {
          int currentCol = startCol + colOffset;
          int module = currentCol / 8;
          int col = 7 - (currentCol % 8);
          if (module >= 4) continue;
          for (int j = 0; j < currentWidth; j++) {
            int row = currentPos + j;
            if (row >= 0 && row < 8) {
              lc.setLed(module, row, col, false);
            }
          }
        }
      }

      // Play a sound effect for a successful block placement
      void playSuccessSound() {
        tone(buzzerPin, 523, 100);
      }

      // Play a sound effect to indicate game over
      void playGameOverSound() {
        tone(buzzerPin, 392, 200);
        delay(200);
        tone(buzzerPin, 349, 400);
        delay(400);
      }

      // Update the maximum allowed top position based on the current block height
      // Allowed range for the top is [-currentWidth, 7+currentWidth]
      void updateMaxPosition() {
        maxPosition = 7 + currentWidth;
      }

      // Place the current moving block onto the stack and calculate its overlap with the previous block
      void placeBlock() {
        buttonPressCount++;
        // Speed up movement as more blocks are placed
        if (buttonPressCount == 4) {
          moveDelay = 120;
        } else if (buttonPressCount == 8) {
          moveDelay = 90;
        } else if (buttonPressCount == 12) {
          moveDelay = 60;
        }
        
        // Special handling for the first block: no previous block exists
        if (currentLayerCount == 0) {
          layers[0].position = currentPos;
          layers[0].width = currentWidth;
          layers[0].startCol = 0;
          layers[0].colWidth = blockColumns;
          currentLayerCount = 1;
          
          updateMaxPosition();
          // Generate a new random top position within allowed range for the next block
          currentPos = random(-currentWidth, maxPosition + 1);
          
          playSuccessSound();
          updatePlacedBlockDisplay(0);
          return;
        }
        
        // For subsequent blocks, calculate the overlapping region with the last placed block
        int prevPos = layers[currentLayerCount - 1].position;
        int prevWidth = layers[currentLayerCount - 1].width;
        int overlapTop = max(prevPos, currentPos);
        int overlapBottom = min(prevPos + prevWidth - 1, currentPos + currentWidth - 1);
        
        // If there is no overlap, the game is over
        if (overlapBottom < overlapTop) {
          clearMovingBlock();
          gameOver = true;
          playGameOverSound();
          return;
        }
        
        // Save the overlapping region as the new block layer
        layers[currentLayerCount].position = overlapTop;
        layers[currentLayerCount].width = overlapBottom - overlapTop + 1;
        layers[currentLayerCount].startCol = currentLayerCount * blockColumns;
        layers[currentLayerCount].colWidth = blockColumns;
        
        // Update the moving block's effective height and increase layer count
        currentWidth = overlapBottom - overlapTop + 1;
        currentLayerCount++;
        
        playSuccessSound();
        
        int totalUsedCols = currentLayerCount * blockColumns;
        // If the display is full (using 32 or more columns), end the game
        if (totalUsedCols >= 32) { 
          gameOver = true;
          return;
        }
        
        updateMaxPosition();
        // Set a new random top position for the next moving block
        currentPos = random(-currentWidth, maxPosition + 1);
        
        updatePlacedBlockDisplay(currentLayerCount - 1);
      }

      void setup() {
        // Initialize button and buzzer pins
        pinMode(buttonPin, INPUT_PULLUP);
        pinMode(buzzerPin, OUTPUT);
        
        // Initialize each LED matrix module
        for (int i = 0; i < 4; i++) {
          lc.shutdown(i, false);
          lc.setIntensity(i, 8);
          lc.clearDisplay(i);
        }
        
        currentLayerCount = 0;
        currentPos = -currentWidth;
        randomSeed(analogRead(0));  // Seed random number generator
        updateMaxPosition();
        updateDisplay();
      }

      void loop() {
        // Handle game over state with blinking display
        if (gameOver) {
          static bool blinkState = false;
          static unsigned long lastBlinkTime = 0;
          if (millis() - lastBlinkTime > 500) {
            lastBlinkTime = millis();
            blinkState = !blinkState;
            if (blinkState) {
              updateDisplay();
            } else {
              // Clear display to create blink effect
              for (int i = 0; i < 4; i++) {
                lc.clearDisplay(i);
              }
            }
          }
          // If the button is pressed during game over, reset the game
          if (checkButton()) {
            gameOver = false;
            currentLayerCount = 0;
            currentWidth = 4;
            currentPos = -currentWidth;
            moveDelay = 150;
            direction = 1;
            buttonPressCount = 0;
            updateMaxPosition();
            updateDisplay();
          }
          return;
        }
        
        // Main game loop: update moving block position based on timing
        unsigned long currentTime = millis();
        if (currentTime - lastMoveTime > moveDelay) {
          lastMoveTime = currentTime;
          clearMovingBlock();
          currentPos += direction;
          // Edge detection using a reflection method:
          // When reaching the top or bottom boundary, reverse the moving direction.
          if (currentPos < -currentWidth) {
            int overshoot = (-currentWidth) - currentPos;
            currentPos = -currentWidth + overshoot;
            direction = -direction;
          } else if (currentPos > maxPosition) {
            int overshoot = currentPos - maxPosition;
            // For the bottom boundary, adjust by subtracting an extra (currentWidth - 1)
            currentPos = maxPosition - overshoot - (currentWidth - 1);
            direction = -direction;
          }
          displayMovingBlock();
        }
        
        // Check if the button is pressed to place the block
        if (checkButton()) {
          placeBlock();
        }
      }
