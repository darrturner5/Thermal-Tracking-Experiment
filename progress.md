## 3/8/26
- Booted Raspberry Pi
- Installed Numpy, Matplotlib, Pyserial libraries

## 3/9/26
- Soldered wire pins onto MLX90640 Sensor and connected pins to Raspberry Pi
- SDA = GPIO 2 (Pin 3)
- SDl = GPIO 3 (Pin 5)
- VIN = 5V (Pin 4)
- GND = Ground (Pin 6)
![IMG_8463](https://github.com/user-attachments/assets/0f34e6f3-ccc4-4284-8fc1-5b039c986a9a)
![IMG_8464](https://github.com/user-attachments/assets/fb9d09ef-a993-4adb-a440-0e120e80ca49)

- Basic Python code
  
      import numpy as np
      import matplotlib.pyplot as plt
      import pyserial
      import smbus ( Communication between I2C pins)

      data = (I2C) Pins

 - Goal for 3/10:
 - DISPLAY BASIC HEAT MAP FROM MLX90640
      
