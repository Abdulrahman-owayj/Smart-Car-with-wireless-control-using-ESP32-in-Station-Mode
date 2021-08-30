# Smart-Car-with-wireless-control-using-ESP32-in-Station-Mode
In this repository i made a Smart Car with wireless control using ESP32 in station Mode <br/>
<br/>


## About the Project <br/>

This project about a Smart Car with wireless controlling using microcontroller ESP32 in STA mode (Station Mode).
Here i used ESP32 to control 2 motors with 4 wheels with H-bridge motor controller (TB6612FNG)
<br/>

<img src="https://user-images.githubusercontent.com/5675794/130445014-ffd060d6-66bc-4091-ab76-dcd984f68b8a.png" alt="alt text" width="300" height="300"> <img src="https://user-images.githubusercontent.com/5675794/130444999-84827549-35cc-4e8e-a57a-6a3a4e895658.png" alt="alt text" width="300" height="300"> <img src="https://user-images.githubusercontent.com/5675794/130445030-8f4e3c18-57fb-46a1-a897-eefda05399f3.png" alt="alt text" width="300" height="300">



<br/>

## Background <br/>
### What is ESP32 
ESP32 is a low-cost, low-power system on a chip microcontrollers with dual-core and integrated Wi-Fi and dual-mode Bluetooth. it has different power modes including deep-sleep mode with 5uA only.
<br/>

### Why ESP32
ESP32 has advantage over other microcontrollers because of its small form factor and lower power consumtions as well as the integerated WIFI and BLE.
<br/>

### Previoues Projects <br/>
Before working in this project, i implemented alot of small projects which helped me with the car project like: <br/> 
**1- Controlling the ESP32's LED through Wifi.** <br/>
**2- Controlling a single motor using ESP32 as access point** <br/>
**3- Controlling 2 or more LED using webpage with ESP32 as server (AP)** <br/>
  
<p align="center">
  <video src="https://user-images.githubusercontent.com/5675794/130485089-36435a6d-22dd-44b7-adea-b4d47004394c.mp4">
</p>

<br/>
<br/>

## ESP32
<p align="center">
  <img src="https://user-images.githubusercontent.com/5675794/130447355-1972fd3d-514d-4743-ae26-0bb2fc784c8a.jpg" >
</p>

### ESP32 Station Mode (STA) <br/>
The ESP32 connects to an existing WiFi network (created by My wireless router) is called Station (STA)
<br/>

<br/>

## Motor Driver (TB6612FNG)
<p align="center">
<img src="https://user-images.githubusercontent.com/5675794/130448700-76826a1a-bd1a-4916-82d6-4a4471a27508.jpeg" alt="alt text" width="600" height="400"> 
</p>

# Smart Car with wireless control using ESP32 in Station Mode (and Web Server) <br/>
<p align="center">
<img src="https://user-images.githubusercontent.com/5675794/130461118-36ee4123-e561-4844-8394-6100970635a0.jpeg" alt="alt text" width="500" height="400">
<img src="https://user-images.githubusercontent.com/5675794/130461128-4dc63c90-3b1c-4dea-8c14-9c9504438776.jpeg" alt="alt text" width="500" height="400">
</p> <br/>

## Control Panel
<br/>

<br/>
<p align="center">
  <img src="https://user-images.githubusercontent.com/5675794/131364040-5a3091df-6160-49f0-8f71-2ee23db3a22a.jpeg" alt="alt text" width="400" height="550" > &nbsp; &nbsp;&nbsp;&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp; <img src="https://user-images.githubusercontent.com/5675794/131364062-469f2156-6a59-4407-8013-fcb1cb80d189.jpeg" alt="alt text" width="400" height="550" >
</p>

<br/>

## Real-Time Simulation 
<br/>

https://user-images.githubusercontent.com/5675794/130477223-57eb97de-285b-4be8-b12d-701100f3e614.mp4





<br/>

## Code <br/>
```ruby 
#include <WiFi.h>                                    // calling all the libraries needed
#include <WebServer.h>
#include <analogWrite.h>

/* Put your SSID & Password */
const char* ssid = "Your router network";           // Enter your SSID name here
const char* password = "Your Password";             //Enter Password here

WebServer server(80);                              // webserver variable with port 80

uint8_t motorB2 = 18;                              // declaring all the variables
uint8_t motorB1 = 5;
uint8_t pwmB = 19;
uint8_t motorA1 = 17;
uint8_t motorA2 = 16;
uint8_t pwmA = 4;
bool forward = LOW;
bool backward = LOW;
bool left = LOW;
bool right = LOW;
bool pause1 = HIGH;

void setup() {
  Serial.begin(230400);                            // initialze the Serial Communications
  pinMode(motorB2, OUTPUT);                        // setting the pins Mode
  pinMode(motorB1, OUTPUT);
  pinMode(pwmB, OUTPUT);
  pinMode(motorA1, OUTPUT);
  pinMode(motorA2, OUTPUT);
  pinMode(pwmA, OUTPUT);

  Serial.println("Connecting to ");
  Serial.println(ssid);

                                               
  WiFi.begin(ssid, password);                             //connect to your local wi-fi network


  while (WiFi.status() != WL_CONNECTED) {                  //check if wi-fi is connected to wi-fi network
  delay(1000);
  Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected..!");
  Serial.print("Got IP: "); 
  Serial.println(WiFi.localIP());                         //print the IP the esp32 got from your router

  server.on("/", handle_OnConnect);                       // using the (on) command to let the page respond to each / to execute different code 
  server.on("/forward", handle_forward);
  server.on("/backward", handle_backward);
  server.on("/left", handle_left);
  server.on("/right", handle_right);
  server.on("/pause1", handle_pause1);
  
  server.onNotFound(handle_NotFound);                   // execute when the page /  not recognised 
  
  server.begin();                                       // Initialize the server
  Serial.println("HTTP server started");
}
void loop() {
  server.handleClient();                                  // to execute the handle request
  if(forward)                                               // if the forward button pressed and the forward varabile triggered high
  {
    digitalWrite(motorB1, HIGH);
    digitalWrite(motorB2, LOW);  
    digitalWrite(motorA1, LOW);
    digitalWrite(motorA2, HIGH); 
    analogWrite(pwmA, 150);
    analogWrite(pwmB, 150); 
  }
  else if(backward)                                      // if the backward button pressed and the backward varabile triggered high
  {
    digitalWrite(motorB1, LOW);
    digitalWrite(motorB2, HIGH);  
    digitalWrite(motorA1, HIGH);
    digitalWrite(motorA2, LOW);
    analogWrite(pwmA, 150);
    analogWrite(pwmB, 150); 
    }
 else if (left){                                           // if the left button pressed and the left varabile triggered high
    digitalWrite(motorB1, LOW);
    digitalWrite(motorB2, HIGH);  
    digitalWrite(motorA1, HIGH);
    digitalWrite(motorA2, HIGH);
    analogWrite(pwmA, 250);
    analogWrite(pwmB, 250); 
  } 
   else if (right){                                        // if the right button pressed and the right varabile triggered high
    digitalWrite(motorB1, HIGH);
    digitalWrite(motorB2, HIGH);  
    digitalWrite(motorA1, HIGH);
    digitalWrite(motorA2, LOW);
    analogWrite(pwmA, 250);
    analogWrite(pwmB, 250); 
  } 
 else if (pause1){                                        // if the Pause button pressed and the Pause varabile triggered high
    digitalWrite(motorB1, HIGH);
    digitalWrite(motorB2, HIGH);  
    digitalWrite(motorA1, HIGH);
    digitalWrite(motorA2, HIGH);
    analogWrite(pwmA, 150);
    analogWrite(pwmB, 150); 
  } 
  }

void handle_OnConnect() {
  forward = LOW;
  backward = LOW;
  left = LOW;
  right = LOW;
  pause1 = HIGH ; 
 // Serial.println("GPIO4 Status: OFF | GPIO5 Status: OFF");
  server.send(200, "text/html", SendHTML(forward,backward)); 
}
 
void handle_forward() {                                               // execute when the button pressed via the (server.on) command
  forward = HIGH;
  backward = LOW;
  left = LOW;
  right = LOW;  
  pause1 = LOW; 
  Serial.println("forward: ON");
  server.send(200, "text/html", SendHTML(true,false));                 // .Send allows the us to receive automatic updates from a server via HTTP connection while SendHTML function which rebuild the webpage with the updated values
}

void handle_backward() {
  backward = HIGH;
  forward = LOW;
    left = LOW;
  right = LOW;  
  pause1 = LOW; 
  Serial.println("backward : ON");
  server.send(200, "text/html", SendHTML(false,true));               // .Send allows the us to receive automatic updates from a server via HTTP connection while SendHTML function which rebuild the webpage with the updated values
}
void handle_left() {
  backward = LOW;
  forward = LOW;
    left = HIGH;
  right = LOW;  
  pause1 = LOW; 
  Serial.println("left : ON");
  server.send(200, "text/html", SendHTML(false,true));               // .Send allows the us to receive automatic updates from a server via HTTP connection while SendHTML function which rebuild the webpage with the updated values
}
void handle_right() {
  backward = LOW;
  forward = LOW;
    left = LOW;
  right = HIGH;  
  pause1 = LOW; 
  Serial.println("right : ON");
  server.send(200, "text/html", SendHTML(false,true));               // .Send allows the us to receive automatic updates from a server via HTTP connection while SendHTML function which rebuild the webpage with the updated values
}
void handle_pause1() {
  backward = LOW;
  forward = LOW;
  left = LOW;
  right = LOW;  
  pause1 = HIGH; 
  Serial.println("pause1 : ON");
  server.send(200, "text/html", SendHTML(false,true));                // .Send allows the us to receive automatic updates from a server via HTTP connection while SendHTML function which rebuild the webpage with the updated values
}
void handle_NotFound(){
  server.send(404, "text/plain", "Not found");
}

String SendHTML(uint8_t forward,uint8_t backward){                        //HTML code for the webpage in the ESP32
  char webpage[] PROGMEM = R"=====(

  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Second Task</title>

</head>
<body>
   
        <div class="container">                                                             //parent class
            <div class="card one">                                                            //class for the row
                <p><a href="/forward"><button class="button">forward</button></a></p>           // forward button
            </div>
            <div class="card two">
                <p><a href="/left"><button class="button">Left</button></a></p>                 // left button
                <p><a href="/pause1"><button class="button">pause</button></a></p>              // pause button
                <p><a href="/right"><button class="button">right</button></a></p>              // right button
            </div>
            <div class="card three">
                <p><a href="/backward"><button class="button">backward</button></a></p>         // backward button
            </div>
        </div> 
 
</body>
</html>
)=====";
  return webpage;
}
```
