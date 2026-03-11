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

# 3/10/26
- Changed 5V to 3.3V on MLX90640
- Imported the MLX90640 Library, Python Code:
  
      import numpy as np
      import matplotlib.pyplot as plt
      import pyserial
      import smbus
      import board
      import busio
      import adafruit_mlx90640

      i2c = busio.I2C(board.SCL, board.SDA)
      thermal = adafruit_mlx90640  #Initialize the sensor
  
      frame = [0] * 768  #24x32 sensor 768 pixels
      while True:
        try:
            mlx.getFrame(frame)
            except ValueError:
              continue
      
    

- Error messages that the board module isnt being read. In Terminal:
  
      cd thermal_tracker
      source venv/bin/activate
      pip install adafruit-blinka
      pip install adafruit-circuitpython-mlx90640

- This still did not solve the issue. Im still getting ModuleNotFound errors regarding busio and board imports. They are installed and Ive tried to change the Python interpreter in Thonny but nothing seemed to work today. 
      
      

  
      
