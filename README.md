# vtol-quadplane
A vtol quadplane based on Teensy4.0 flight controller with dRehmFlight code with certain modifications for including smooth transitions, control mixing and usage of PWM type ESCs. 
<br> <br>

This project aimed at building a vtol (vertical takeoff and landing) quadplane testbed(4 fixed thrust motors for the quadcopter configuration and 1 forward thrust motor for the plane configuration). The quadplane is essentially a mashup between a quadcopter and a fixed wing aircraft, thus giving the quadplane vertical takeoff and landing as well as hovering capabilities like a quadcopter while also retaining the efficiency and endurance of a fixed-wing aircraft during longer flights.
<br>
The quadplane comes with another advantage, its lack of mechanical movement or vectoring on thrust providing motors - hence making it easier to design, build and also minimize points of failure. Other vtol designs often contain vectoring of the thrust motors giving them better efficiency than the quadplane but the reliability might become reduced because of the extra moving parts and that is one of the reasons majority of the commercial vtol uavs are in a quadplane configuration.
<br><br>


Coming back to the technical side of the project, we will first look at the electronic components required
<br>
<ol type=A>
<head>Electronics</head>
<ol>
  <li> SG90 Servo Motors </li>
  <ul>
    <li>2 for ailerons</li>
    <li>1 for elevator</li>
    <li>1 for rudder</li>
  </ul>
  
  <li>Teensy4.0 MCU</li>

  <li>MPU6050 IMU</li>
</ol>
</ol>
The source code and relevant documentation can be found on https://github.com/nickrehm/dRehmFlight
