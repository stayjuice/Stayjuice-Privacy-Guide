import os
import subprocess
import time

TARGET_DEVICE_NAME = "Enter You Usb VID Here"  # Replace with the USB VID or name you're checking for

def shutdown_pc():
    """Shuts down the PC."""
    print("Target USB device disconnected. Shutting down the PC...")
    os.system("shutdown /s /t 1")  # Windows shutdown command

def is_usb_device_connected(device_name):
    """Checks if a USB device with the specified name is connected."""
    try:
        # Run the WMIC command to list connected USB devices
        result = subprocess.check_output("wmic path CIM_LogicalDevice where 'Description like \"%USB%\"' get /value", shell=True, text=True)
        # Check if the target device name appears in the output
        return device_name in result
    except subprocess.CalledProcessError as e:
        print(f"Error checking USB devices: {e}")
        return False

def monitor_usb():
    """Monitors the USB devices and shuts down if the target device is disconnected."""
    print(f"Monitoring for USB device: {TARGET_DEVICE_NAME}...")
    while True:
        if not is_usb_device_connected(TARGET_DEVICE_NAME):
            shutdown_pc()
            break
        time.sleep(1)  # Wait 1 second before checking again

if __name__ == "__main__":
    monitor_usb()
