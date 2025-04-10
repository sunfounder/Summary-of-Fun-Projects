 
FAQ
=========

.. #. If your computer is win7 and Pico 2 W cannot be detected.
    * Download the USB CDC driver from http://aem-origin.microchip.com/en-us/mindi-sw-library?swsearch=Atmel%2520USB%2520CDC%2520Virtual%2520COM%2520Driver
    * Unzip the ``amtel_devices_cdc.inf`` file to a folder named ``pico-serial``.
    * Change the name of ``amtel_devices_cdc.inf`` file to ``pico-serial.inf``.
    * Open/edit the ``pico-serial.inf`` in a basic editor like notepad
    * Remove and replace the lines under the following headings:

    .. code-block::

        [DeviceList]
        %PI_CDC_PICO%=DriverInstall, USB\VID_2E8A&PID_0005&MI_00

        [DeviceList.NTAMD64]
        %PI_CDC_PICO%=DriverInstall, USB\VID_2E8A&PID_0005&MI_00

        [DeviceList.NTIA64]
        %PI_CDC_PICO%=DriverInstall, USB\VID_2E8A&PID_0005&MI_00

        [DeviceList.NT]
        %PI_CDC_PICO%=DriverInstall, USB\VID_2E8A&PID_0005&MI_00

        [Strings]
        Manufacturer = "ATMEL, Inc."
        PI_CDC_PICO = "Pi Pico Serial Port"
        Serial.SvcDesc = "Pi Pico Serial Driver"

    #. Close and save and make sure your retain the name as pico-serial.inf
    #. Go to your pc device list, find the pico under Ports, named something like CDC Device. A yellow exclamation mark indicates it.
    #. Right click on the CDC Device and update or install driver choosing the file you created from the location you saved it at.




.. Piper Make
.. ------------------

.. #. How to set up the Pico 2 W on Piper Make?
    For detailed tutorials, please refer to :ref:`per_setup_pico`.

.. #. How to download or import code?
    For detailed tutorials, please refer to :ref:`per_save_import`.

.. #. How to connect to Pico 2 W?
    For detailed tutorials, please refer to :ref:`connect_pico_per`.


