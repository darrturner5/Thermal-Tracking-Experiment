# THERMAL TRACKING EXPERIMENT:
## How do different PID tuning methods affect Thermal Camera accuracy?
- How do different PID tuning methods affect Thermal Camera accuracy?


# What is Thermal Imaging and why does it matter?
Thermal imaging uses a special type of camera that picks up infrared radiation emitted by objects or the environment and graphs them into a visual image(Thermograph). It is very useful in applications such as:
- Troubleshooting, Maintenence by visualising heating faults or defects in industrial machinery
- Safety and Precaution by seeing hotspots. (Overheating Electrical wiring, equipment)
- Search and Rescue (Seeing objects or people hidden in smoke, darkness)

 ## What is Infrared Radiation?
  - Infrared radiation is a type of electromagnetic radiation on the electromagnetic spectrum. It is longer than visible light but shorter than microwaves which are used for applications such as radar. Infrared radiation is invisible to the human eye because of its longer wavelength than red light, (longest wave in visible light spectrum humans can see).
  - Infrared Radiation has a wavelength of about 300GHz to 430THz. Broken up into 3 different categories.  Near Infrared, Mid Infrared, far infrared. All 3 have amazing qualities for skin and wound healing
  - Near Infrared = Wavelength closest to the visible light spectrum. Shortest wavelength able to penetrate skin, it is used for anti - aging, or skin health such as reduced inflammation, stimulating healing, and increasing local circulation.
  - Mid Infrared = Gas sensing, Molecular spectroscopy, 
  - Far Infrared = Thermal imaging, Astronomy, Environmental analysis( Air Quality Monitoring)
  
    
  - <img width="1435" height="685" alt="image" src="https://github.com/user-attachments/assets/4d53a084-92ef-4b97-adf8-498411a7549d" />

  


These are just some of the many amazing aspects of Thermal Imaging. I want to test this Thermal imaging by seeing how accurate I can get it to track heat sources using different controller methods, comparing each one, and doing some cool experiments with them like the ones mentioned above. 


## System Architecture
I am using a Melexis MLX90640 32x24 Pixel Infrared Thermal Camera 
![IMG_8445](https://github.com/user-attachments/assets/4552b028-94a0-4f67-8c92-e761d135f967)
![IMG_8446](https://github.com/user-attachments/assets/914fcc75-37a9-4001-807d-5d35adbd946d)




# CONTROL THEORY
This is a PID Controller (Proportional - Integral - Derivative). It is a feedback closed loop controller used in machines, or a process
that need continous control. Commonly used in Robotics and Automation systems, HVAC (Heating, Ventilation, Air Conditioning), Industrial systems in factories etc. It works by comparing a specific setpoint and measured value of the system. This is known as the error. The controller tries to make the measured value as close as it can towards the setpoint of the system.
- (P) = Corrects by setting an output directly proportional to the error (Output = Kp * Error)
- (I) = Sum of all past errors, Reduce Steady State error ( Error that persists when the system stops correcting)
- (D) = Rate of change of error, Eliminates Overshoot and Oscillation.

  There are different variations to this controller where each serve a diffent purpose for whatever system you're dealing with.
  There are:
- (P) Only Control
- (PI) Proportional - Integral
- (PD) Proportional - Derivative
- (PID) Proportional - Integral - Derivative

This controller is excellent as Ive used only P and the PD control in Camera Guided Laser Tracking System and they've worked really well. Ive never experimented with the Integral component or the full PID controller. I will test each one and test their accuracy, and flaws. 

Now getting my actual measured value for my tracking Im thinking either to get the hottest pixel and track that way, or see if I could still use contour and centroid detection.

# Sources
- https://celliant.com/pulse/all/infrared-light/#:~:text=Infrared%20energy%20is%20typically%20divided,penetrate%20the%20skin%20and%20tissues.
- https://www.icnirp.org/en/frequencies/infrared/index.html#:~:text=Wavelength%20range%20and%20sources,fall%20into%20the%20infrared%20region.
- https://en.wikipedia.org/wiki/Infrared
- https://www.nist.gov/image/electromagnetic-spectrum
- https://nlir.com/infrared-spectrum-wavelengths/


