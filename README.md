# AnninRobotics v4 Robot Arm   
   
Hi! 👋
 
This repo is home to all the notes, thoughts and future plans I have for my build of the amazing [AnninRobotics v4 robot arm](https://www.anninrobotics.com). Below, you'll find some basic info covering some of the physical and electrical attributes of the arm and the software stack used to control it; There's also a TODO list of features I'm hoping to add in the near future.  

If you have any questions or comments, feel free to post on the [discussions board](https://github.com/btoms20/RobotArm/discussions)! 🙋
   
## 🦾 Structure
- [AnninRobotics v4 Design & Kit](https://www.anninrobotics.com)
- 6 axis robot arm, aluminum core with 3D printed cosmetic panels
   
## 🧠 Logics   
- Duet 3 6XD running RepRapFirmware with custom IK code    
- DWC interface with http gcode server exposed
   
   
## ✅ TODOs   
- [x] Mechanical Build
- [x] Control/Junction Box (panel cutouts, air vents, estop, board brackets, etc)
- [x] Electronics (cable routing, wiring, soldering, etc)
- [x] Electronics (endstops and encoders)
- [x] Software Basics (provision Duet 3 6XD)
- [x] Software Basics (external drivers configured, steps/deg, speed, accel)
- [x] Software Basics (homing files, homing sequence)
- [x] Open loop control (all 6 axis working)
- [x] End Effector (servo gripper installed, configured and running)
- [x] Upgrade J2 to new [higher precision gearbox](https://www.anninrobotics.com/post/j2-gearbox-update) (https://github.com/btoms20/RobotArm/discussions/2) 
- [x] Get robo viewer plugin set up on DWC   
- [x] Change DWC from mm to deg (our config is doing everything in mm, it would make more sense if we started using degrees instead)   
- [x] Enable Closed Loop Control (https://github.com/btoms20/RobotArm/discussions/3)
- [ ] [Joint Encoders](https://github.com/ongdexter/ar3_hardware_mods) as well
- [x] Get IK set up    
- [x] Software based backlash compensation (3.5 - M425)
- [ ] Use accelerometer to fine tune ee pose
- [ ] Install Panel Due on front of junction box 

   
## 🎛️ Duet DWC Config File   
```
; Enable network
if {network.interfaces[0].type = "ethernet"}
	M551 P"***"           ; set password (used when you connect Duet Web Control or via FTP)
	M552 P192.168.1.24 S1 ; enable network and bind to, 24 <or your favorite number>
	M586 P0 S1            ; enable HTTP
	M586 P1 S0            ; disable FTP
	M586 P2 S0            ; disable Telnet
else
    M552 S1

; Fan
M950 F0 C"!out3" Q250  ; Fan 1 is connected to out_3 pin, PWM at 250Hz (noctua says 25khz)
M106 F0 S0.5           ; 0.0 == off 1.0 == full

; Servo / Gripper
M950 S1 C"vfd" ; Minimum 680 (closed) - Maximum 1080 (opened)
;M280 P1 S1080  ; Open Gripper 

; External Driver Config
M569 P0 R0 T2.5:2.5:5:5 S1 ; physical drive 0 goes forwards (change to S0 to reverse it) (with min pulse width commands)
M569 P1 R0 T2.5:2.5:5:5 S1 ; physical drive 1 goes forwards (change to S0 to reverse it) (with min pulse width commands)
M569 P2 R0 T2.5:2.5:5:5 S0 ; physical drive 2 goes reverse (change to S1 to reverse it) (with min pulse width commands)
M569 P3 R0 T2.5:2.5:5:5 S0 ; physical drive 3 goes reverse (change to S1 to reverse it) (with min pulse width commands)
M569 P4 R0 T2.5:2.5:5:5 S1 ; physical drive 4 goes forwards (change to S0 to reverse it) (with min pulse width commands)
M569 P5 R0 T2.5:2.5:5:5 S0 ; physical drive 5 goes reverse (change to S1 to reverse it) (with min pulse width commands)

; Drive to Axis Mapping
M584 X0 Y1 Z2 U3 V4 W5 

; Endstops
M574 X1 S1 P"io1.in"  ; configure switch-type (e.g. microswitch) endstop for high end on X via pin io1.in
M574 Y1 S1 P"io2.in"
M574 Z1 S1 P"io3.in"
M574 U1 S1 P"io4.in"
M574 V1 S1 P"io5.in"
M574 W1 S1 P"io6.in"

; Configure microstepping with interpolation
M350 X1 Y1 Z1 U1 V1 W1 I0  

; Steps per mm
M92 X200.00 Y200.00 Z200.00 U200.00 V200.00 W200.00

; Set maximum instantaneous speed changes (mm/min)
M566 X100.00 Y100.00 Z100.00 U100.00 V100.00 W100.00

; Set maximum speeds (mm/min)
M203 X2000.00 Y2000.00 Z2000.00 U2000.00 V2000.00 W1000.00

; Set accelerations (mm/s^2)
M201 X50.00 Y50.00 Z50.00 U25.00 V25.00 W10.00      

;M906 X1460 I30   ; set motor currents (mA) and motor idle factor in per cent

; Set idle timeout
M84 S30          

; Axis Limits
; Set axis maxima and high homing switch positions (adjust to suit your machine)
M208 X70 Y35 Z40 U55 V25 W35 S0 
; Set axis minima and low homing switch positions (adjust to make X=0 and Y=0 the edges of the bed)
M208 X0 Y0 Z0 U0 V0 W0 S1 
```
   
## 📸 Media   
![RobotArm](https://user-images.githubusercontent.com/32753167/213881860-788fda76-0838-43bd-9a7b-2eec156446b1.jpeg)

| Homing to Ready | Block De-Stacking |
| :-------------: | :-------------: |
| ![Homing](https://user-images.githubusercontent.com/32753167/213882039-ebf8dee0-3288-44c1-89bc-e9ffb9ed38bb.mp4) | ![Block Stack](https://user-images.githubusercontent.com/32753167/213882056-57d40fd9-df9f-4b78-9aec-2173c790e646.mp4) |

| Closed Loop |
| :-------------: |
| ![Closed Loop](https://user-images.githubusercontent.com/32753167/233811881-bad81b96-132a-4ea8-9189-a250a28ba883.mp4) |
