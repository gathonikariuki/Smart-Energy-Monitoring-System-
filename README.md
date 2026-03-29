# Smart-Farm-Energy-Monitoring-System-
This is a smart farm energy system using Arduino and sensors

##MY CONTRIBUTUIONS
-Circuit design using TinkerCAD and KiCAD
-Sensor Integration
-System testing and debugging

##TECNOLOGIES USED
-Arduino / C++
-IoT Concepts
-Basic networking

##FEATURES
-Real-time energy monitoring
-Automated control relay

##PROJECT FILE
Code for Arduino 1
// C++ code
//
#include<Wire.h>
#include<LiquidCrystal.h>

LiquidCrystal lcd(3,4,5,6,7,8);

const int moisturePin =A0;  //Analog input for soil moisture
const int LEDPin=2;//Digital output to control realy/LED
 
  void setup(){
  
  //Initialize LCD
  lcd.begin(16,2);
  lcd.print("Smart Farm Init");
  
  //Initialize relay/LED pin
  pinMode(2,OUTPUT);
  
  //Start serial communication
  Serial.begin(9600);
  
  delay(500);
  lcd.clear();
}

void loop(){
  //Read soil moisture (0-1023)
  int moistureValue = analogRead(moisturePin);
  
  //Simulated temperature value 
  int temperature = 25;
  
  //Display on LCD
  lcd.setCursor(0,0);
  lcd.print("Moisture:");
  lcd.print(moistureValue);
  
  lcd.setCursor(0,1);
  lcd.print("Temperature:");
  lcd.print(temperature);
  lcd.print((char)223);  //Degree symbol
  lcd.print("C ");
  
  // Control relay/LED (turn ON if moisture is low)
  if (moistureValue < 400){
    digitalWrite(LEDPin, HIGH); //Turn pump on
  }
  else { digitalWrite(LEDPin, LOW); //Turn pump off
       }
  
  // Send data to Arduino 2 via Serial
  Serial.print(moistureValue);
  Serial.print(",");
  Serial.println(temperature);
  
  delay(3000);  //Wait for 3 seconds before next reading
}

Code for Arduino 2
// C++ code
//
#include<Wire.h>
#include <Servo.h>
#include<SoftwareSerial.h>

//Simulate Bluetooth module on pins D0(RX) and D1(TX)
SoftwareSerial bluetooth(0,1); //RX,TX

Servo valveServo;

const int servoPin =9;
const int moistureThreshold =400;



void setup()
{
  Serial.begin(9600);    //Serial to communicate with Arduino1
  bluetooth.begin(9600);  //Simulated Bluetooth
  valveServo.attach(servoPin);
  
  //Initialize
  valveServo.write(0);  //Close valve
  Serial.println("Arduino 2 ready.");
  bluetooth.println("System ready. Waiting for data...");
          
}

void loop()
{
  if (Serial.available()){
    // Read incoming string from Arduino 1
    String input =Serial.readStringUntil('\n');
    
    //Expected formar: "moisture,temperature"
    int commaIndex =input.indexOf(',');
    if (commaIndex>0){
      int moisture =input.substring(0,commaIndex).toInt();
      int temperature=input.substring(commaIndex + 1).toInt();
      
      //Display received data (simulate Bluetooth&SMS feedback)
      bluetooth.print("Moisture: ");
      bluetooth.print(moisture);
      bluetooth.print("Temp: ");
      bluetooth.println(temperature);
      
      //SMS Simulation via Serial
      if (moisture<moistureThreshold){
        Serial.println("SIMULATED SMS; Soil dry! Moisture =" + String (moisture));
      }
      
      //Servo logic; simulate valve control
      if (moisture < moistureThreshold){
        valveServo.write(90);  //Open valve
      }else {
        valveServo.write(0);  //Close valve
      }
    }
  }
}
