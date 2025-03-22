Listener.py Listens for USB Device with the VID of Your Microcontroller to Be Unplugged

Set the VID with this code:

TARGET_DEVICE_NAME = "Enter Your USB VID Here"  # Replace with the USB VID or name you're checking for
To find your VID, look in Device Manager or use USBDeview by Nirsoft.

main.py gets saved to your microcontroller (RP2040). I use the RP2040 Touch LCD by Waveshare, as it has a built-in accelerometer.
Waveshare RP2040 Touch LCD Documentation

The sensor in it is called a QMI8658. You may need to update the code to match the sensor that you use and refer to the documentation provided by the manufacturer.

main.py monitors the sensor in the RP2040 and plays a message to the system when movement is detected.

The threshold for movement can be set under:

# Threshold for gentle movement (calibrate as needed)
MOVEMENT_THRESHOLD = 3.2
in main.py.
For me, 3.2 detected picking up the PC. However, changing this value adjusts the sensitivity of movement detection.

shutdownpc.py listens for messages from the microcontroller. When the microcontroller detects movement (for example, if an intruder is detected picking up your PC), it shuts down the PC promptly.

This should be used with full disk encryption so that when a device is picked up, the PC will shut down, and your encrypted data should theoretically be safe from the intruder.

There is a case available from some sellers of the Waveshare LCD Touch RP2040 microcontroller. This case can be mounted to a PC case or laptop using double-sided tape or, in the case of a PC, could be glued to the case. For testing purposes, I used tape.

This software is open-source. The source code will be provided, and it is for educational purposes.