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

        The CPU is warm but Its actually not that bad. Im gonna keep it at 1MHz. Once again the speed of the graph is much better but not the greatest in tracking that were going to be doing.

      # 3/14/26

        - Installed OpenCV to capture thermal frames instead. I first normalized the image from temperature readings from the sensor to image pixels to be displayed on OpenCV. I added interpolation which fixes our laggy issue between each frame.
          
               import cv2
              
               img = cv2.normalize(data,None,0,255,cv2.NORM_MINMAX)
               img = uint(img)

               img = cv2.resize(img,(640,380), interpolation=cv2.INTER_LINEAR) # I interchanged between INTER_CUBIC and INTER_LINEAR.                  both work really well
          
               cv2.imshow('t',img)
               cv2.waitKey(1) 


        - The result was the error code:
          
              (-215:Assertion failed) func != 0 in function 'resize'

          Which all points back to our normalization code. OpenCV did not get the sufficient data it needed to convert the temperature readings. My MLX90640 sends floating point temperatures like (23.1C). OpenCV expects data in uint8 or uint16 format. I set up my code to run at 8 bit format. This means we need nonnegative integers ranging from 0 to 255. So really all the fix is to put the 8 in the uint(img) part of the code to get it working again.

               import cv2
              
               img = cv2.normalize(data,None,0,255,cv2.NORM_MINMAX)
               img = uint8(img)

               img = cv2.resize(img,(640,380), interpolation=cv2.INTER_LINEAR) # I interchanged between INTER_CUBIC and INTER_LINEAR.                  both work really well
          
               cv2.imshow('t',img)
               cv2.waitKey(1)
          - The results worked really well and is much smoother than the matplotlib. This works much much better for tracking and Im going to see if I could make it more smoother. The image is a black and white video with white being the hotspots and black being the spots that emit no heat.
            
          [![OPENCV PROCESSING WITH MLX90640]](https://www.youtube.com/watch?v=iVqEDPPg1hQ)

          To take off the black and white image, I applied the inferno colormap.

                img = cv2.applyColormap(img,cv2.COLORMAP_INFERNO)

          - [![OPENCV INFERNO COLORMAP]](https://www.youtube.com/shorts/rCxOWqm78tQ)

          - Taken from my last project, Ive implemented an exponential filter which smoothes movement between each frame. I put this in now since I plan on implementing PID control soon. 

                alpha = 0.5 # adjust; alpha closer to 1 is slower but smoother, alpha closer to zero is faster but less smooth
                filtered = None

                while True:
                   try:
                       thermal.getFrame(frame)
                       data = np.reshape(frame, (24,32))

                  if filtered is None:
                       filtered = data
                  else:
                    filtered = alpha*filtered + (1-alpha)*data


            # 3/15/26
            
            - I put a flame right in front of the camera. Everything blacked out. Heat from  my face and hands became dark while the flame captured all the heat.
            What likely happened is that the flame is so hot compared to everything else it kinda broke the scale of what it considered hot. the value range is 0 to 255 where 0 is the lowest temperature and 255 being the brightest. The flame was the highest so it stretched the scale. So now my body heat is likely at 0 which shows up relatively cold. I dont know if this would be a problem for tracking later. It really depends on what I want to track honestly. I may end up wanting to track the flame. That would be really neat.

            So far I like how everything looks. I think here is where ill start adding the tracking to try and set up the PID controller. Only problem is that I am not dealing with object detetction and color tracking but instead temperature ranges. So right now I am a little puzzled on how I am gonna go about that to get my measured value.

            Error = Measured Value - setpoint.

            I could to go with 2 routes.
         
            
            1. Detetcting the hottest pixel and following that, or:
            2. Detecting the hottest region
           
          I didnt know which one would produce the ebst result so I just played around with both to see what its like. Detecting the hottest pixel was not bad. I used the numpy argmax function and plugged the data inside.

                  hottest = np.argmax(filtered)

          This returns a flattened index, or a 1 dimensional array of temperature values. We want both an x and y values to calculate our error. We unravel the flattened index into coordinates:

              x, y = np.unravel_index(hottest, filtered.shape

          np.shape turns this into numbered coordinates.

          I printed (x,y) to see how it was looking. It didnt come out too bad but I realized taht sometimes this hottest pixel could shift as I kept getting different coordinates even when I had the object sitting still. This could cause a lot of problems down the line. Especially with servo jittering. So instead I opted for detecting the hottest region and going off of that.

          I used the np.mean function which get the average of my temperature data from the sensor and added 2 to it to get a threshold of values.
          
              threshold = np.mean(filtered) + 2
              region = filtered > threshold


          So everytime my data detected something greater than say 20C, that becomes my hottest region. I printed out the region adn I like this way better. The data seemed more consistent and not fidgety and all over the place compared to my hottest pixel idea. Now that I have my hottest region, I can find the centroid or better yet, where that hottest region is using the np.where function.
          

              y, x = np.where(region)

              cx = np.mean(x)
              cy = np.mean(y)

          With this I finally have my measured value and now able to find my error. Simply put the error is simple to find
          (Measured value - setpoint)
          We now just found our measured value and now we just need a setpoint.
          - My MLX90640 is a 32x24 (Width, Height) camera.
          - The x setpoint is the middle of the screen. so we take half of 32 to make 16
          - the y setpoint is the middle of the screen. So we take half of 24 to make 12
         


          The first of the controller that Ill be using is the P controller
          - Output = Gain * error
     
            
                Correction_x = KpX * error_x
                Correction_y = KpY * error_y

          - The hardest part is over now honestly all we have to do now is map these to the servos to send over to the Serial port. This took much less code then I thought and thought it would be much longer and much tougher than my prevous project with the logitech camera. I guess because were dealing with arrays numpy made it easier? I dont know if that makes any sense ebecause the camera pixels in a sense are arrays themselves. So could I have done the same for my previous project? I may test that theory out soon.
         
                servo_x = int(90 - correction_x*40)
                servo_y = int(90 - correction_y*40)

                Arduino.write(f"{int(servo_x)},{servo_y}\n".encode())

          - For now our python code is essentially done. Now all thst left is switching over to the arduino side and sending this over the serial. The the arduino code I used the same code I used for my Camera Guided System because everything going on is pretty much the same. I have a pretty rogue setup going on with how I am connecting everything, but I put more time into it tomorrow.
          - its just about setting up and finding the port for python to send data over to. Then we'll test and see what went wrong with my code because I dont expect this to be completely flawless at all. So if youre reading up to this point, expect some mistakes. We'll find out what they are tomorrow.
         
          # 3/16/26

      - Got the serial port id connected to my arduino to the raspberry pi


                  python3 -m serial.tools.list_ports -v

      - Plugged my port name /dev/tty50 into:

                  Arduino = serial.Serial("/dev/tty50", 115200)

    It doesnt respond to any of my data being sent from python. The servos also were jittering and acting out even when powered on and I spent a good 15 minutes trying to examine why. Turns out I forgot to add a common ground between my arduino and my power supply. Just wow. 


# 3/17/26
- My arduino servos arent moving at all or responding to the responses being sent by python.

        sudo raspi-config
        Interface Options - Serial Port - Enable Serial Port Hardware


  Which didnt seem to do anything. First Im checking if the ports are in sync and actually sending things over.


  # 3/18/26

  - I believe its the cable that im using. I typed dmesg -w into the terminal which lets me view what was plugged in and what was not. I plugged in and out the cable connecting to the arduino and it did not show up as anything. So this tells me that the cable that Im using is for power only and not data. Which is bumming because Ive spend a lot of time on this part and have to find a new cable to work with. The Arduino lights up but no data is being sent over.

  I went and got aother cable and that didnt seem to get the job done either. Im using a USB A/C Cable and both cables seem not to work and just powers the arduino. I tried typing ls/dev/tty* and not seeing any cable.

  I plugged in my Arduino Uno R3 to see if that worked and it popped up on my Raspberry Pi dmesg -w and the serial and everything else popped up as "ttyACM0". I am so happy to see this outcome. The past few days were spent in the dark. It is a minor hardware downgrade (well not really) but Ill be happy to use it if it works better with my project

  Its a little old and dusty as I just had this in a box of tools in my closet and havent been used in like 4 years but it came out much better than my Arduino UNO R4 Minima could have.

  ## Results
  - Servos are moving in proportion to where the hottest region is
  - OpenCV camera frame start to lag after about 20 seconds sending slow servo positions
  - [![P CONTROLLED SERVO MAPPING]](https://www.youtube.com/shorts/Og03rHnTCiA)


  I also increased the threshold:

      threshold = np.mean(filtered) + 3

  I started to notice that the servos would react to any sort of what in the area. Even when I add 3 it still kind of reacts to noise in the background. Slightly less but this will be an issue that were going to have to clean up in the future

  I added a clamp to the servo positions using the np.clip function:

      np.clip(servo_x, 0, 180)
      np.clip(servo_y, 0, 180)
  
  I noticed that the servo positions went over 180 and below 0 when I printed both servo positions. This only occured when I raised the gain (Kp) on both servos from 0.1 to 0.2. So to not damage my servos I added these clamps just to keep it in check.

    I really like the hottest region design because the frames are much much more stable. I can even see it in the servo positions. As soon as I stop moving the object emitting heat, the servos lock in the right position. Not much jitter really. The only problem is the Camera frame locking up and start to lag and produce minimal servo positions to send over. Its definitely something in the Python loop that may be overwhelming the frames causing it to lag? thats just my guess

    - Overall today was a really good day. I figured out and solved the problem that held me up for a few days by switching the hardware from the Arduino Minima to the Arduino Uno R3. This led me down to more problems that need to solved such as the frame reezing problem and the background noise affecting the servo



# 3/19/26

-  Tried checking if the CPU on the Raspberry PI was getting too hot. At 40C So I guess its fine
-  Checking if there were memory build up as the frames kept going but it stayed the same.
-  tried moving around my centroid detection, servo mapping, outside the while True loop. Nothing started.


I dont get what the problem is. It works perfectly fine up until about 30 seconds or so and starts to lag tremendously.

# 3/20/26
- Soldered together a power board for the servos.
- RC snubber circuit to suppress current spikes of servos.
          
  ![IMG_8512](https://github.com/user-attachments/assets/bb98e616-a400-40fd-8a2a-7dc0f0accb32)



# 3/21/26

- Well I finally connected it altogether today to see what happens when its powered on.
- Servos made a buzzing sound
- Servos did not respond to the thermal camera movements

  All this led me to conclude that the RC concoction that I threw together was too long of a delay for the servos to get any power
  I used 2 470uF and 2 100uF Capacitors in parallel for the bank and a 1k resistor. Thats 1140uF combined. A 1140uF capacitor and 1k resistor RC circuit has a full charging time of 5.7 seconds which is why I didnt see the servos moving at all.

  I suspected that that was the isue so I disconnected the resistor and applied power to the capacitors themselves and the servos ran fine. Any resistor Ive noticed adds this delay or makes the servos response very slow or unresponsive. The bigger capacitor bank works pretty well though. The current spikes arent that extreme anymore (over 0.40A). My highest Ive gotten was about 0.30A.


Still the only problem is the freezing/ lagging camera frames on opencv after 30 or so seconds. I still havent gotten a fix for that.


# 3/22/26
-  Put together a frame and case for the Camera, Power board, Arduino. I left the Raspberry Pi out separately.
-  Took out the tilt motion servo,
![IMG_8517](https://github.com/user-attachments/assets/cd7d084c-08e8-4558-8001-7c0d83763a57)
![IMG_8518](https://github.com/user-attachments/assets/0b2c614f-32da-483a-a158-615a5d472ada)
![IMG_8519](https://github.com/user-attachments/assets/b37d9ae7-134d-4924-95dd-76b9527db0be)




I like the design of everything and how it looks.

[![P CONTROL Thermal Camera]](https://www.youtube.com/shorts/4j-wBSsUC-M)

## P Control Results:
- Jittery, Robotic movements
- Overshooting slightly
- Tracks and follows the flame decently
- Gain of 0.05. Higher gain causes more oscillation and overshooting.


# 3/23/26

- Added the tilt servo back
- Both Pan and Tilt Gains are 0.06. Any higher causes oscillations and overshoot
- Follows flame semi accurately
- Slight Jitter
- Needs a harder Threshold in Python to only react to the flame
![IMG_8533](https://github.com/user-attachments/assets/eac3ada0-6328-4255-8195-600ada70c391)
![IMG_8531](https://github.com/user-attachments/assets/cd84f8cf-948f-4729-893f-3108fc8262f5)
![IMG_8532](https://github.com/user-attachments/assets/0069cddf-42bf-42b6-8877-9c2603d45662)




Overall not terrible but its certaintly not the best. Tomorrow were going to test and code up the other versions of the controller.


[[P Control Pan and Tilt]](https://www.youtube.com/shorts/tgETwxEr1io)


# 3/24/26
- Attempted to add filter to detect objects emitting heat greater than 100C (fire)

# 3/25/26
- implemented PD Control (Derivative term)
- Calculated dt using:

      prev_time = time.time()
      current_time = time.time()
      dt = current_time - prev_time

      print (dt)

  I got an average of about 8 - 10 fps thts about 0.1 dt

  Right now the PD Control is terrible. Jumping around frame to frame and there are delays in the timing of the derivative term because the frames of the thermal camera arent stable. SO changing dt is a massive issue causing different delays and a whole bunch of non accurate readings. Also the filter that I made yesterday did not work. I going to have to change that. Its reading evevry little thing that emits heat so thats an issue im going to have to fix. I dont really like the PD control on here so far.

  The Derivative kick is definetly there no matter how close to zero I set the gain. Its amplifying every single noise thats present in the thermal camera. Its either I put a stronger filter or take it out completely. Im going to put in and try PI and then the full PID to see what I like. but so far:

  # PD Controller Results
  - Excessive derivative kick
  - Increased and excessive Jittering
  - More sensitivity
  - Not stable at all

  # 3/26/26
  - No data


  # 3/27/26
  - Added in the Integral portion of the controller to make the full PID Structure.

          Integral_x += error_x * dt
          Integral_y += error_y * dt
          integral_x = max(min(integral_x, 50), -50)
          integral_y = max(min(integral_y, 50, -50)


  - Today I am just going to tune around with the different Gain values to find a perfect balance between each one of them.
  I definitely Take back my statement regarding the terrible and utter uselessness of the PD control on my Thermal tracking camera. I set the gain to 0.01 and it worked really well actually. Any higher than this would cause extra jittering and kick. 
  - Kp Gain of 0.05
  - Kd Gain of 0.01
  - Ki Gain of 0.02
          
![IMG_8551](https://github.com/user-attachments/assets/673d0b10-87ca-4071-89a2-25f876cfa8c0)
![IMG_8549](https://github.com/user-attachments/assets/9d24362b-b783-4514-a721-57836380ecd1)



PID Works really well too. Everything is limited by the mechanical structure of the servos



      

          

            

        
        
   
       
   
    

      

  
      
