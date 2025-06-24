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
<br>


![WhatsApp Image 2024-12-05 at 01 11 57_970144d2 (2)](https://github.com/user-attachments/assets/0f54631e-2ae4-4975-8fe4-5c40c9a2242e)
<br>
Also check the jumper between the USB power and our rails is not connected as shown in the diagram. No need to add diodes(it is for reverse polarity protection) <br><br>
![teensy-3-6-back-vin-to-vusb-jumper (1)](https://github.com/user-attachments/assets/cbec6f0e-2ea7-403c-a367-a2228cf54f90)


<br><br>
Now attach via some jumpers the MPU6050 Vcc and GND to our rails and the SDA,SCL to pin 18 and 19 respectively. Also attach the receiver channels to the exposed gpio pins and connect our rails to the receiver rails for powering it
<br><br>

![WhatsApp Image 2024-12-05 at 00 42 10_bfb31de6 (1)](https://github.com/user-attachments/assets/a030e697-306b-4526-9570-0a17789808c0)

<br><br>
The flight controller build is now done.
A more detailed version is available on https://github.com/nickrehm/dRehmFlight

<head>Flight Controller Code Modifications</head>



