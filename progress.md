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
  - I also made the mistake of putting "mlx.getFrame(frame)" when mlx isnt defined. I have thermal defined as my sensor.
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
    -  [![MLX90640 DEMO](![IMG_8478](https://github.com/user-attachments/assets/68177241-9e32-45f5-9738-c07b22cdcfbe)
)](https://www.youtube.com/watch?v=pKJAl4Ml11M)
   
    - Although I learned new matplotlib functions, plt.ion(), plt.clf(), plt.pause()
    - Learned and how to properly setup the sensor
    - Numpy reshaping of the graph to plot on matplotlib.
    - Properly switch and change my python interpreter on Linux

      Tomorrow I am going to see how I can make it switch and detect each frame much quicker and have less lag. As it'll be very important to get this right going forward.

      # 3/12/26
       In the MLX90640 Datasheet, The refresh rate goes from 0.5Hz to 64Hz, which is roughly 4 seconds to 0.03125 seconds. I        definitely want the quickest refresh time but i gotta see if my Pi could handle all of that. It probably could but I want to try        that out.
   
      I added a refresh rate right underneath the intializing of the sensor:
      
            thermal.refresh_rate = adafruit_mlx90640.RefreshRate.REFRESH_64_HZ
   
      Only to get hit with an error of:
      
            raise RuntimeError ("Too many retries")
            RuntimeError: Too many retries
   
      I believe since the sensor might be producing more data much quicker, Its running through my code much quicker as well. So each new frame is getting updated and closed really really fast. Thats my guess on maybe thats whats going on. Or maybe my Raspberry Pi cant handle all of that.
      After some trial and error, the only highest possible Refresh Rate I coulkd go with with 4HZ which still moved a little slow for my liking.
      - Increasing the baudrate on my I2C pins on my Raspberry Pi could work. Although this may use up more power and produce more heat. Especially with the little heat sink that I currently hae on the GPU. So id have to adjust accordingly so I wouldnt damage my Raspberry PI.

      I changed this by going into the terminal :

          sudo nano /boot/firmware/config.txt

      And adding:

          dtparam=i2c_arm=on, i2c_arm_baudrate=400000
   
      - (400kHz Baudrate). The PI can go much quicker up to 1MHZ but I am really afraid of overheating and frying the PI. I know thats going to get really hot when the camera is on. A future upgrade would be to install heatsinks and fans if I ever want to approach those numbers. But for now ill test the performance of the 400kHz Baudrate and see its effects on the PI and the speed of the sensor.
      - Back inside my code, I added the frequency to the i2c variable:

            i2c = busio.I2C(board.SCL, board.SDA, frequency=400000)
    

      - The Sensor data is actually a little bit faster than before. Its updating slightly quicker but still not to the area that I want it to be as its still kind of jittery and laggy ish. Im running my refresh rate at 4Hz which I think is slowing it down. The problem im running into is just the runtime error that I keep getting.
      - Tomorrows goal is to address that issue and see if i could get a faster refresh rate than 4Hz.

      # 3/13/26
      - I started by using subplots instead of one whole graph. This actually worked better in my favor because it allowed me to increase my refresh rate from 4 to 16Hz which is really good. The plot is still very laggy and not suitable enough for precise tracking.
     
              plt.ion()
              fig, ax = plt.subplots(figsize=(12,7))
              therm1 = ax.imshow(np.zeros((24,32)), vmin=0, vmax=60)

        i might just push the I2C to 1MHz just to see what happens. I also have a question regading if matplotlib is actually slowing down the sensor? It might be a little weird to say but could that be the case? Because its conbtinously generating and deleting plots.

        
        
   
       
   
    

      

  
      
