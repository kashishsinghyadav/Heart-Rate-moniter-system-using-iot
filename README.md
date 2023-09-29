𝐈𝐎𝐓 𝐁𝐚𝐬𝐞𝐝 𝐇𝐞𝐚𝐫𝐭 𝐑𝐚𝐭𝐞 𝐌𝐨𝐧𝐢𝐭𝐨𝐫 𝐮𝐬𝐢𝐧𝐠 𝐀𝐫𝐝𝐮𝐢𝐧𝐨 𝐚𝐧𝐝 𝐄𝐒𝐏𝟖𝟐𝟔𝟔

we are  make a Heart Rate Monitoring System using Arduino, Pulse sensor, and ESP8266 WiFi module. The pulse sensor will detect the heart rate, and Arduino will send it to Thingspeak using the ESP8266 WiFi module

* 𝐂𝐨𝐦𝐩𝐨𝐧𝐞𝐧𝐭𝐬 𝐑𝐞𝐪𝐮𝐢𝐫𝐞𝐝 *
* 
->𝐏𝐮𝐥𝐬𝐞 𝐬𝐞𝐧𝐬𝐨𝐫
  
->𝐄𝐒𝐏𝟖𝟐𝟔𝟔 𝐖𝐢-𝐅𝐢 𝐦𝐨𝐝𝐮𝐥𝐞

->𝐀𝐫𝐝𝐮𝐢𝐧𝐨 𝐔𝐧𝐨

->𝐁𝐫𝐞𝐚𝐝 𝐁𝐨𝐚𝐫𝐝

->𝟐𝟐𝟎-𝐨𝐡𝐦 𝐫𝐞𝐬𝐢𝐬𝐭𝐨𝐫𝐬

->𝐋𝐄𝐃

->𝐂𝐨𝐧𝐧𝐞𝐜𝐭𝐢𝐧𝐠 𝐰𝐢𝐫𝐞𝐬


 *𝐏𝐫𝐨𝐠𝐫𝐚𝐦𝐦𝐢𝐧𝐠 𝐂𝐨𝐝𝐞 𝐄𝐱𝐩𝐥𝐚𝐧𝐚𝐭𝐢𝐨𝐧 *
 
 First of all, install all the required libraries and then enter your Wi-Fi name, password, and thingspeak.com api.
 
 #include <SoftwareSerial.h>
#include <stdlib.h>
#define DEBUG true
#define SSID "WiFi Name"     // Enter Your WiFi Name Here 
#define PASS "WiFi Password"       // Enter Your WiFi Password Here
#define IP "api.thingspeak.com"// thingspeak.com api

Define the pins where you connected the esp8266 Tx and Rx and enter your Thingspeak API key.


SoftwareSerial ser(2,3); 
String msg = "GET /update?key=Your Api Key"; //Enter your API key


* 𝐃𝐞𝐟𝐢𝐧𝐞 𝐚𝐥𝐥 𝐭𝐡𝐞 𝐢𝐧𝐭𝐞𝐠𝐞𝐫 𝐚𝐧𝐝 𝐯𝐨𝐥𝐚𝐭𝐢𝐥𝐞 𝐯𝐚𝐫𝐢𝐚𝐛𝐥𝐞𝐬 𝐭𝐡𝐚𝐭 𝐰𝐢𝐥𝐥 𝐛𝐞 𝐮𝐬𝐞𝐝 𝐭𝐨 𝐜𝐚𝐥𝐜𝐮𝐥𝐚𝐭𝐞 𝐭𝐡𝐞 𝐁𝐏𝐌 (𝐁𝐞𝐚𝐭𝐬 𝐏𝐞𝐫 𝐌𝐢𝐧𝐮𝐭𝐞). *

/Variables
int error;
int sensorPin = 0;         // Connect Pulse Sensor Signal Pin to Analog Pin A0
int ledpin = 9;        //  Connect Led Positive Pin to Arduino Pin 9
volatile int BPM;           // int that holds raw Analog in 0. updated every 2mS
volatile int Signal;              // holds the incoming raw data
volatile int IBI = 600;     // int that holds the time interval between beats 
volatile boolean Pulse = false;     // "True" when heartbeat is detected. "False" when not a "live beat". 
volatile boolean QS = false;        // becomes true when Arduino finds a beat.


* 𝐓𝐡𝐞 𝐯𝐨𝐢𝐝 𝐮𝐩𝐝𝐚𝐭𝐞𝐛𝐞𝐚𝐭() 𝐟𝐮𝐧𝐜𝐭𝐢𝐨𝐧 𝐰𝐢𝐥𝐥 𝐬𝐞𝐧𝐝 𝐝𝐚𝐭𝐚 𝐭𝐨 𝐭𝐡𝐞 𝐬𝐞𝐫𝐢𝐚𝐥 𝐦𝐨𝐧𝐢𝐭𝐨𝐫 𝐚𝐧𝐝 𝐚𝐥𝐬𝐨 𝐩𝐮𝐛𝐥𝐢𝐬𝐡 𝐢𝐭 𝐭𝐨 𝐭𝐡𝐞 𝐭𝐡𝐢𝐧𝐠𝐬𝐩𝐞𝐚𝐤 𝐜𝐡𝐚𝐧𝐧𝐞𝐥. *

  void updatebeat() {
  String cmd = "AT+CIPSTART=\"TCP\",\"";
  cmd += IP;
  cmd += "\",80";
  Serial.println(cmd);
  ser.println(cmd);
  delay(2000);
  if(ser.find("Error")) {
    return;
  }
  cmd = msg ;
  cmd += "&field1=";   
  cmd += BPM;
  cmd += "\r\n";
  Serial.print("AT+CIPSEND=");
  ser.print("AT+CIPSEND=");
  Serial.println(cmd.length());
  ser.println(cmd.length());
  if(ser.find(">")) {
    Serial.print(cmd);
    ser.print(cmd);
  }

* 𝐓𝐡𝐞 𝐛𝐞𝐥𝐨𝐰 𝐜𝐨𝐝𝐞 𝐢𝐬 𝐮𝐬𝐞𝐝 𝐭𝐨 𝐜𝐨𝐧𝐯𝐞𝐫𝐭 𝐭𝐡𝐞 𝐩𝐮𝐥𝐬𝐞 𝐬𝐞𝐧𝐬𝐨𝐫 𝐝𝐚𝐭𝐚 𝐢𝐧𝐭𝐨 𝐁𝐏𝐌 (𝐁𝐞𝐚𝐭𝐬 𝐏𝐞𝐫 𝐌𝐢𝐧𝐮𝐭𝐞). *

  ISR (TIMER2_COMPA_vect){                  // triggered when Timer2 counts to 124
  cli();                                 // disable interrupts while we do this
  Signal = analogRead(sensorPin);              // read the Pulse Sensor 
  sampleCounter += 2;                         // keep track of the time in mS
  int N = sampleCounter - lastBeatTime;       // monitor the time since the last beat to avoid noise
    //  find the peak and trough of the pulse wave

  if(Signal < thresh && N > (IBI/5)*3){      // avoid dichrotic noise by waiting 3/5 of last IBI
    if (Signal < T){                         // T is the trough
      T = Signal;                            // keep track of lowest point in pulse wave 
    }
  }
  if(Signal > thresh && Signal > P){        // thresh condition helps avoid noise
    P = Signal;                             // P is the peak
  }                                         // keep track of highest point in pulse wave
   if (N > 250){                                   // avoid high frequency noise
    if ( (Signal > thresh) && (Pulse == false) && (N > (IBI/5)*3) ){        
      Pulse = true;                               // set the Pulse flag when there is a pulse
      digitalWrite(ledpin,HIGH);                // turn on pin 13 LED
      IBI = sampleCounter - lastBeatTime;         // time between beats in mS
      lastBeatTime = sampleCounter;               // keep track of time for next pulse
      if(secondBeat){                        // if this is the second beat
       secondBeat = false;                  // clear secondBeat flag
        for(int i=0; i<=9; i++){             // seed the running total to get a realistic BPM at startup
          rate[i] = IBI;                      
        }
      }





*******𝐂𝐢𝐫𝐜𝐮𝐢𝐭 𝐃𝐢𝐚𝐠𝐫𝐚𝐦******


![Screenshot (82)](https://github.com/kashishsinghyadav/Heart-Rate-moniter-system-using-iot/assets/117498422/3ec10b05-a67e-486e-bddb-330a44f536ba)
 


![Screenshot (81)](https://github.com/kashishsinghyadav/Heart-Rate-moniter-system-using-iot/assets/117498422/39f29f37-a61d-4a23-97cb-ba3e80b83f3f)


