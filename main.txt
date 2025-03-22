from machine import I2C, Pin
import time
import math

# Define I2C pins
I2C_SDA = 6
I2C_SDL = 7

# Threshold for gentle egg movement (calibrate as needed)
MOVEMENT_THRESHOLD = 3.2

# Accelerometer class (QMI8658)
class QMI8658:
    def __init__(self, address=0x6B):
        self._address = address
        self._bus = I2C(1, scl=Pin(I2C_SDL), sda=Pin(I2C_SDA), freq=100_000)
        if not self.WhoAmI():
            raise RuntimeError("QMI8658 not detected!")
        self.Config_apply()

    def _read_byte(self, cmd):
        return self._bus.readfrom_mem(self._address, cmd, 1)[0]

    def _read_block(self, reg, length=1):
        return self._bus.readfrom_mem(self._address, reg, length)

    def _write_byte(self, cmd, val):
        self._bus.writeto_mem(self._address, cmd, bytes([val]))

    def WhoAmI(self):
        return self._read_byte(0x00) == 0x05

    def Config_apply(self):
        self._write_byte(0x02, 0x60)  # REG CTRL1
        self._write_byte(0x03, 0x23)  # Acc Range: 8g, ODR: 1000Hz
        self._write_byte(0x04, 0x53)  # Gyro Range: 512dps, ODR: 1000Hz
        self._write_byte(0x05, 0x00)  # REG CTRL4
        self._write_byte(0x06, 0x11)  # Enable LPF
        self._write_byte(0x08, 0x03)  # Enable Accelerometer & Gyroscope

    def Read_XYZ(self):
        raw_data = self._read_block(0x35, 6)
        acc = []
        for i in range(0, 6, 2):
            value = (raw_data[i+1] << 8) | raw_data[i]  # Combine MSB and LSB
            if value >= 32768:  # Handle signed values for 16-bit integers
                value -= 65536
            acc.append(value / 2048.0)  # Scale down to proper units
        return acc

# Initialize the accelerometer
sensor = QMI8658()

# Movement detection loop
print("Monitoring movement...")
while True:
    xyz = sensor.Read_XYZ()
    acc_x, acc_y, acc_z = xyz[0], xyz[1], xyz[2]

    # Calculate the magnitude of acceleration
    movement_magnitude = math.sqrt(acc_x ** 2 + acc_y ** 2 + acc_z ** 2)

    # Check if movement exceeds the threshold
    if movement_magnitude > MOVEMENT_THRESHOLD:
        print("Intruder")

    time.sleep(0.1)  # Small delay for stability
