import serial
import os

# Serial port settings
SERIAL_PORT = 'COM3'  # Update this to the correct port (e.g., COM3)
BAUD_RATE = 9600      # Match the baud rate used by your microcontroller

def shutdown_pc():
    """Shuts down the PC."""
    print("Shutdown command received. Shutting down the PC...")
    os.system("shutdown /s /t 1")  # Windows shutdown command

def handle_unrecognized_message(message):
    """Handles unrecognized messages."""
    print(f"Unrecognized message received: {message}")

def main():
    # Initialize serial communication
    try:
        serial_connection = serial.Serial(SERIAL_PORT, BAUD_RATE)
        print(f"Listening for signals on {SERIAL_PORT}...")

        while True:
            # Read data from the serial port
            signal = serial_connection.readline().decode().strip()
            signal = signal[:8]
            if signal == "Intruder":
                shutdown_pc()
            else:
                handle_unrecognized_message(signal)
#                shutdown_pc()
    except Exception as e:
        print(f"Error: {e}")
    finally:
        if 'serial_connection' in locals() and serial_connection:
            serial_connection.close()

if __name__ == "__main__":
    main()
