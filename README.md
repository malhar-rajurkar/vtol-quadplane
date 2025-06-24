# vtol-quadplane

![WhatsApp Image 2025-06-24 at 19 22 57_c7b98f99 (1)](https://github.com/user-attachments/assets/16a6a688-7d13-4f22-ba16-c0ad7b89e303)


<br><br>
A vtol quadplane based on Teensy4.0 flight controller with dRehmFlight code with certain modifications for including smooth transitions, control mixing and usage of PWM type ESCs. 
<br> <br>

This project aimed at building a vtol (vertical takeoff and landing) quadplane testbed(4 fixed thrust motors for the quadcopter configuration and 1 forward thrust motor for the plane configuration). The quadplane is essentially a mashup between a quadcopter and a fixed wing aircraft, thus giving the quadplane vertical takeoff and landing as well as hovering capabilities like a quadcopter while also retaining the efficiency and endurance of a fixed-wing aircraft during longer flights.
<br>
The quadplane comes with another advantage, its lack of mechanical movement or vectoring on thrust providing motors - hence making it easier to design, build and also minimize points of failure. Other vtol designs often contain vectoring of the thrust motors giving them better efficiency than the quadplane but the reliability might become reduced because of the extra moving parts and that is one of the reasons majority of the commercial vtol uavs are in a quadplane configuration.
<br><br>


Coming back to the technical side of the project, we will first look at the electronic components and materials required
<br>

<head>Electronics</head>
<ol>
  <li> SG90 Servo Motors </li>
  <ul>
    <li>2 for ailerons</li>
    <li>1 for elevator</li>
    <li>1 for rudder</li>
  </ul>
  
  <li>Teensy4.0 MCU*1</li>

  <li>MPU6050 IMU 6dof*1</li>

  <li>RC Receiver</li>

  <li>LiPo Battery*1</li>
  
  <li>A2212 BLDC Motors</li>
  <ul>
    <li>4*2300kv for quad</li>
    <li>1*1400kv for plane</li>
  </ul>

  <li>SimonK 30A Brushless ESCs*6</li>

  <li>PDB and wire harness</li>

  <li>Small Perfboard or prototyping board and jumpers</li>
</ol>

<head>Hardware </head>
<ol>
  <li>Propellors</li>
  <ul>
    <li>6045 prop*2</li>
    <li>8045 prop*2</li>
    <li>5045 prop*1</li>
  </ul>

  <li>2mm Ply</li>

  <li>Thermocol</li>

  <li>D type aluminum extrusions</li>

  <li>Sandpaper and hotwire cutter</li>

  <li>Push rods</li>

  <li>Nuts and Bolts</li>
</ol>

## The Flight Controller
The flight controller is the brain of the entire thing. It reads the rc inputs from receiver, acquires current linear and angular acceleration in x,y,z axes from the IMU, then runs the PID controller action and then commands the ESCs with the required motor speeds. Let us now look at how to build it.

<head>Hardware</head>
We will be using a perfboard as our base. Attach the teensy4.0 to the perfboard using female headers so that it is removable. Solder male headers in line with the exposed gpio pins and also solder another 2 columns of headers along with them. They will act as our buses and will receive power from the inbuilt 5v BEC in our ESCs and then distribute the power to the servos as well as the teensy and mpu. <br>
Now connect the 5v rail to the 5v pin and the ground rail to the GND pin on the teensy4.0. IMP - Teensy operates on 3.3v logic, applying 5v to any pin other than the designated 5v pin will fry it.
<br><br>


![WhatsApp Image 2024-12-05 at 01 11 57_970144d2 (2)](https://github.com/user-attachments/assets/0f54631e-2ae4-4975-8fe4-5c40c9a2242e)
<br><br>
Also check the jumper between the USB power and our rails is not connected as shown in the diagram. No need to add diodes(it is for reverse polarity protection) <br><br>
![teensy-3-6-back-vin-to-vusb-jumper (1)](https://github.com/user-attachments/assets/cbec6f0e-2ea7-403c-a367-a2228cf54f90)


<br><br>
Now attach via some jumpers the MPU6050 Vcc and GND to our rails and the SDA,SCL to pin 18 and 19 respectively. Also attach the receiver channels to the exposed gpio pins and connect our rails to the receiver rails for powering it
<br><br>

![WhatsApp Image 2024-12-05 at 00 42 10_bfb31de6 (1)](https://github.com/user-attachments/assets/a030e697-306b-4526-9570-0a17789808c0)

<br><br>
The flight controller build is now done.
A more detailed version is available on https://github.com/nickrehm/dRehmFlight
<br><br>

<head>Flight Controller Code Modifications</head>
It is strongly recommended that one reads the dRehmFlight documentation https://github.com/nickrehm/dRehmFlight and understand how the code works and familiarise themselves with the functions in the code. I will not be going into the details of each function but will only cover the functions that have been modified for the purpose of our project.
<br> <br>
<ol>
  <li>Addition of PWM ESCs</li> <br>
  As mentioned in the electronic component list, we are using PWM ESCs but the default dRehmFlight code has assumed the ESCs to be Oneshot ESCs. To change this do these modifications:
  <ol>
    <li> In the declare pins section, create servo objects to control the ESC with PWM signal. add 
    <br>
      PWMServo m1;<br>
      PWMServo m2;<br>
      PWMServo m3;<br>
      PWMServo m4;<br>
      PWMServo m5;<br>
      PWMServo m6;<br>
   </li>
    <li> In the void setup, initialize all pins, attach the created objects with PWM width for their range i.e 1000-2000<br>
      m1.attach(m1Pin, 1000, 2000);<br>
      m2.attach(m2Pin, 1000, 2000);<br>
      m3.attach(m3Pin, 1000, 2000);<br>
      m4.attach(m4Pin, 1000, 2000);<br>
      m5.attach(m5Pin, 1000, 2000);<br>
      m6.attach(m6Pin, 1000, 2000);<br>
    </li>
    <li> In the void setup, arming servo channels, initialize our motors as 0 degrees <br>
      m1.write(0);<br>
      m2.write(0);<br>
      m3.write(0);<br>
      m4.write(0);<br>
      m5.write(0);<br>
      m6.write(0);<br>
    </li>
    <li> In the main loop, command actuators, and add<br>
      m1.write(m1_command_PWM); //Writes PWM value to servo object<br>
  m2.write(m2_command_PWM);<br>
  m3.write(m3_command_PWM);<br>
  m4.write(m4_command_PWM);<br>
  m5.write(m5_command_PWM);<br>
  m6.write(m6_command_PWM);<br>
    </li>
    <li> Now go in the functions, scaleCommands(), comment out the oneshot motor commands and add this<br>
      
  m1_command_PWM = m1_command_scaled*180;<br>
  m2_command_PWM = m2_command_scaled*180;<br>
  m3_command_PWM = m3_command_scaled*180;<br>
  m4_command_PWM = m4_command_scaled*180;<br>
  m5_command_PWM = m5_command_scaled*180;<br>
  m6_command_PWM = m6_command_scaled*180;<br>

  m1_command_PWM = constrain(m1_command_PWM, 0, 180);<br>
  m2_command_PWM = constrain(m2_command_PWM, 0, 180);<br>
  m3_command_PWM = constrain(m3_command_PWM, 0, 180);<br>
  m4_command_PWM = constrain(m4_command_PWM, 0, 180);<br>
  m5_command_PWM = constrain(m5_command_PWM, 0, 180);<br>
  m6_command_PWM = constrain(m6_command_PWM, 0, 180);<br>
    </li>
    <li> In the functions, calibrateESCs(), comment out existing motor commands and add<br>
     m1_command_scaled = thro_des;<br>
      m2_command_scaled = thro_des;<br>
      m3_command_scaled = thro_des;<br>
      m4_command_scaled = thro_des;<br>
      m5_command_scaled = thro_des;<br>
      m6_command_scaled = thro_des;<br>
      <br><br>
      m1.write(m1_command_PWM);<br> 
      m2.write(m2_command_PWM);<br>
      m3.write(m3_command_PWM);<br>
      m4.write(m4_command_PWM);<br>
      m5.write(m5_command_PWM);<br>
      m6.write(m6_command_PWM);<br>
      </li>
      <li> In the functions, throttleCut(), we will be using channel 5 as the kill switch hence add<br>
        if (channel_5_pwm > 1500) {
    m1_command_PWM = 0;<br>
    m2_command_PWM = 0;<br>
    m3_command_PWM = 0;<br>
    m4_command_PWM = 0;<br>
    m5_command_PWM = 0;<br>
    m6_command_PWM = 0;<br>
      </li>
  </ol> 
  <li>Control Mixing and Transitions</li><br>
  In the control mixing part, we will be adding how the calculated PID controller outputs will be applied to the motors and actuators. For a quadplane setup, control mixing is a bit simpler because of the 2 distinct forms of quad and plane are not interfering with each other. In the current quadplane setup, I have implemented 3 modes (which are controlled by a 3 pos switch) as well as flaperons:<br>
  <ol>
    <li>Hover Mode<br>
    <ul>
      <li> Fading variable = 1 and Channel 6 > 1750</li>
      <li> Flaperons in fully extended position</li>
      <li> Quad motors are completely active</li>
      <li> Plane motor is completely inactive</li>
      <li> Elevator and Rudder completely inactive</li>
    </ul>
    </li>
    <li>Slow Forward Flight Mode<br>
    <ul>
      <li> Fading variable = 0.8 and 1750> Channel 6 > 1350</li>
      <li> Flaperons in partially extended position with mild aileron control</li>
      <li> Quad motors are partially active</li>
      <li> Plane motor is partially active</li>
      <li> Elevator and Rudder partially active</li>
    </ul>
    </li>
     <li>Forward Flight Mode<br>
    <ul>
      <li> Fading variable = 0 and Channel 6 < 1350</li>
      <li> Flaperons are fully retracted and with complete aileron control</li>
      <li> Quad motors are completely inactive</li>
      <li> Plane motor is completely active</li>
      <li> Elevator and Rudder is completely active</li>
    </ul>
    </li>
        <br><br>
        The fading variable is a constant int between 1 and 0, with each of the flight modes being assigned the values as mentioned above. The value of fading variable is changing linearly between the modes, thus ensuring a smoother transition. The rate of change of the fading variable can be changed by changing the values in the linear fader function. <br><br>
        Finally the entire control mixing code is:<br><br>
      if(channel_6_pwm > 1750){ //HOVER <br>
    fading_variable = 1;//floatFaderLinear(fading_variable, 0.8, 1.0, 5, 1, 2000); <br>
    s1_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru - fading_variable*0.4; <br>
    s3_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru + fading_variable*0.4; <br>
    m5_command_scaled = (1-fading_variable)*(thro_des);<br>
  }<br><br>

  if(channel_6_pwm >=1300 && channel_6_pwm <=1750){ //SLOW FORWARD FLIGHT<br>
    fading_variable = floatFaderLinear(fading_variable, 0.8, 1.0, 5, 0, 2000) ; <br>
    s1_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru - fading_variable*0.2;<br>
    s3_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru + fading_variable*0.2;<br>
    m5_command_scaled = 0.5*(thro_des);<br>
  }<br><br>
  if(channel_6_pwm < 1300){ //FORWARD FLIGHT<br>
    fading_variable = floatFaderLinear(fading_variable, 0.0, 0.8, 1.8, 0, 2000);<br>
    s1_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru - fading_variable*0.4;<br>
    s3_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru + fading_variable*0.4;<br>
    m5_command_scaled = 1*(thro_des);<br>
  }<br><br>

  m1_command_scaled = fading_variable*(thro_des - pitch_PID + roll_PID + yaw_PID); //Front left<br>
  m2_command_scaled = fading_variable*(thro_des - pitch_PID - roll_PID - yaw_PID); //Front right<br>
  m3_command_scaled = fading_variable*(thro_des + pitch_PID - roll_PID + yaw_PID); //Back Right<br>
  m4_command_scaled = fading_variable*(thro_des + pitch_PID + roll_PID - yaw_PID); //Back Left<br>
  m5_command_scaled = (1-fading_variable)*(thro_des);<br>
  m6_command_scaled = 0;<br><br>

  //0.5 is centered servo, 0.0 is zero throttle if connecting to ESC for conventional PWM, 1.0 is max throttle<br>
  //s1_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru - fading_variable*0.4;<br>
  s2_command_scaled = 0.4 - (1-fading_variable)*0.9*pitch_passthru;<br>
  //s3_command_scaled = 0.5 - (1-fading_variable)*0.7*roll_passthru + fading_variable*0.4;<br>
  s4_command_scaled = 0.5 + (1-fading_variable)*0.6*yaw_passthru;<br>
  s5_command_scaled = 0;<br>
  s6_command_scaled = 0.55 + (1-fading_variable)*0.6*yaw_passthru;;<br>
  s7_command_scaled = 0;<br>
 
}
  </ol>
</ol> 

![WhatsApp Image 2025-06-25 at 01 51 10_5ba73834 (1)](https://github.com/user-attachments/assets/5d73cc08-a1f1-49f5-afba-6147f4daa6f3)




