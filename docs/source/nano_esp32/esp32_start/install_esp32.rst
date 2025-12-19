.. _install_esp32:

1.4 Install the Arduino Nano ESP32 Board (Important)
============================================================

To program the ESP32 microcontroller, you need to install the ESP32 board package in the Arduino IDE. Follow the step-by-step guide below:

**Install the ESP32 Board**

.. #. Open the Arduino IDE. Go to **File** and select **Preferences** from the drop-down menu.

..     .. image:: img/install_esp321.png

.. #. In the Preferences window, locate the **Additional Board Manager URLs** field. Click on it to activate the text box.

..     .. image:: img/install_esp322.png

.. #. Add the following URL to the **Additional Board Manager URLs** field: https://espressif.github.io/arduino-esp32/package_esp32_index.json. This URL points to the package index file for the ESP32 boards. Click the **OK** button to save the changes.

..     .. image:: img/install_esp323.png

#. In the **Board Manager** window, type **Arduino ESP32 Boards** in the search bar. Click the **Install** button to start the installation process. This will download and install the ESP32 board package.

    .. image:: img/install_board1.png

#. Congratulations! You have successfully installed the ESP32 board package in the Arduino IDE.

**Upload the Code**

#. Now, connect the Arduinio Nano ESP32 to your computer using USB Type-C cable.

    .. image:: img/nano_esp32.png
        :width: 600
        :align: center

#. If your ESP32 is connected to the computer, you can choose the correct port by clicking **Tools** -> **Port**.

    .. image:: img/install_esp326.png

#. In addition, Arduino 2.0 introduced a new way to quickly select the board and port. For ESP32, it is usually not recognized automatically, so you need to click on **Select other board and port**.

    .. image:: img/install_esp327.png

#. In the search box, type **Arduino Nano ESP32** and select it when it appears. Then, choose the correct port and click **OK**.

    .. image:: img/select_board.png

#. Afterwards, you can select it through this quick access window. Note that during later use, it may happen that ESP32 is not available in the quick access window, and you will need to repeat the previous two steps.

    .. image:: img/last_step_esp32.png

#. Both methods allow you to select the correct board and port, so choose whichever is more convenient for you. Now everything is ready to upload the code to the ESP32.
