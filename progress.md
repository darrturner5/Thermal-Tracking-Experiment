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
- Installed and Imported the MLX90640 Library
  
      cd thermal_tracker
      source venv/bin/activate
      pip install adafruit-blinka
      pip install adafruit-circuitpython-mlx90640


- Python Code:
  
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

      plt.imshow(frame)
      plt.colorbar()
      plt.show()

  
      
-  This was to get the sensor to run and continously display an image. I kept getting:
  
        ModuleNotFoundError: No module named 'board'

- Error messages that the board module isnt being read. This is pointing towards an interpreter issue in Thonny. I tried to change it to a different interpreter that has these libraries installed.  Both the busio and board imports still arent found inside the code. These should be included inside the adafruit-blinka library. Maybe im not changing it to the right interpreter. So we'll try again tomorrow.

  # 3/11/26
  - I changed my Thonny interpreeter to my thermal_tracking folder:
 
        /home/darrturner5/thermal_trackier/venv/bin/python3

  - The libraries are being the detected but the only problem now is this line
 
        thermal = adafruit_mlx90640(i2c)
        TypeError: 'module' object is not callable

  - The "adafruit_mlx90640" is not a function. I made the mistake of doing so. It is a module. Inside the module is "MLX90640". So I cahnged it to
 
        thermal = adafruit_mlx90640.MLX90640(i2c)
  - The code works but nothing seems to pop up. Thats expected as now were going to need to plot it with matplotlib and numpy.
  - I also amde the mistake of putting "mlx.getFrame(frame)" when mlx isnt defined. I have thermal defined as my sensor.
  - I had my matplotlib functions after my while True function which resulted in it not plotting anything beause the while True ran a continous loop
 
        
        import numpy as np
        import matplotlib.pyplot as plt
        import pyserial
        import board
        import busio
        import adafruit_mlx90640

        i2c = busio.I2C(board.SCL, board.SDA)
        thermal = adafruit_mlx90640.MLX90640  #Initialize the sensor
  
        frame = [0] * 768  #24x32 sensor 768 pixels
        while True:
        try:
            thermal.getFrame(frame)
            data = np.reshape(frame,(24,32))
 
            plt.imshow(frame)
            plt.colorbar()
            plt.show()
            

            except ValueError:
              continue

    - This gets me my first ever pixel graph of my camera:
      ![IMG_8474](https://github.com/user-attachments/assets/bd8c54d8-d460-484f-987b-cabfb0769be7)
      ![IMG_8475](https://github.com/user-attachments/assets/4631f97b-7501-4a3a-8e53-a8b43da4d074)


    - We can make it a continuous loop by using the plt.ion() function just before the while True:
    - plt.ion() allows us to keep updating the graph
    - plt.clf() clears the whole current graph. (Inside the while True: function
    - plt.pause(0.01) pauses for a few before bringing the next frame


          frame = [0] * 768  #24x32 sensor 768 pixels
          plt.ion()
   
      
          while True:
            try:
            thermal.getFrame(frame)
            data = np.reshape(frame,(24,32))
   
            
            plt.clf()
            plt.imshow(frame)
            plt.colorbar()
            plt.show()
            plt.pause(0.001)
            
            
            

            except ValueError:
              continue
   





    -  The amount of time it takes to swtch between each frame is very slow and kinda laggy almost. This will be an issue going forward       especially if were going to track the hottest region or centroid because the servos will start to lag behind.
   
    - Although I learned new matplotlib functions, plt.ion(), plt.clf(), plt.pause()
    - Learned and how to properly setup the sensor
    - Numpy reshaping of the graph to plot on matplotlib.
    - Properly switch and change my python interpreter on Linux

      Tomorrow I am going to see how I can make it switch and detect each frame much quicker and have less lag. As it'll be very important to get this right going forward.
   
    

      

  
      
