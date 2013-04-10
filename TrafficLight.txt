// constants won't change. They're used here to 
// set pin numbers:
const int nsbuttonPin = 2;     // the number of the pushbutton pin
const int nsledbuttonPin =3;
const int webuttonPin = 4;     // the number of the pushbutton pin
const int weledbuttonPin =5;

// variables will change:
int nsbuttonstate = 0;         // variable for reading the pushbutton status
int webuttonstate = 0;

int seqcounter = 0;
int counter = 0;
int anticounter = 0;
int ledstate = LOW;
//This is the array I use to active the correct LED
int ns[]={7,8,9};//green, yellow, red
int we[]={10,11,12};//green, yellow, red
//Millisecond counter
int led13 = 13;


long previousMillis = 0;
long previousbuttonMillis = 0;
long PreviousNSbuttonmillis = 0;
long PreviousWEbuttonmillis = 0;
long previouslightcounter = 0;




//The two variables below I want to use
//for the car sensors
long previousns = 0;
long previouswe = 0;


//These variables you change for different lgths
long interval = 500;  //LED 13 counter interval
long gointerval = 10000;  //This is how long the light stays green for either way
long yellowinterval = 2000;  //this is how long the light stays on yellow
long notrafficwait = 3000;  //This is how long the traffic waiting will wait wtih no traffic going the opposite way.

void setup() {
  // initialize serial communication for trouble shooting code.
  //open monitor to see output
  Serial.begin(9600); 
   // initialize the LED pins:
   pinMode(led13,OUTPUT);
  for(int i =0;i<3;i++){
    pinMode(ns[i],OUTPUT);  
    pinMode(we[i],OUTPUT);
  }
  

  // initialize the activate LED pin as an output:
  pinMode(nsledbuttonPin, OUTPUT); 
  pinMode(weledbuttonPin, OUTPUT); 
  
  // initialize the pushbutton pin as an input:
  pinMode(nsbuttonPin, INPUT);
  pinMode(webuttonPin, INPUT);
}

void loop() {
  
  
  
  nsbuttonstate = digitalRead(nsbuttonPin);
  webuttonstate = digitalRead(webuttonPin);
  
  //This if is to blink the led pad light for ns button
  if (nsbuttonstate == HIGH) {     
    // turn LED on:    
    digitalWrite(nsledbuttonPin, HIGH);  
  } 
  else {
    // turn LED off:
    digitalWrite(nsledbuttonPin, LOW); 
  }
  //This if is to blink the led pad light for we button  
  if (webuttonstate == HIGH) {     
    // turn LED on:    
    digitalWrite(weledbuttonPin, HIGH);  
  } 
  else {
    // turn LED off:
    digitalWrite(weledbuttonPin, LOW); 
  }
  
  
  unsigned long currentMillis = millis();
 
 //light counter 
 if(currentMillis - previouslightcounter > interval) {
    // save the last time you blinked the LED 
    previouslightcounter = currentMillis;   

    // if the LED is off turn it on and vice-versa:
    if (ledstate == LOW)
      ledstate = HIGH;
    else
      ledstate = LOW;

    // set the LED with the ledState of the variable:
    digitalWrite(led13, ledstate);
  }
  

//Switch statement for the 6 different states that can exist
    switch (seqcounter) {
      
    case 0://we is green first    
      digitalWrite (we[2], LOW);//Turn off previous led
      digitalWrite (ns[2], LOW);//Turn off previous led
      digitalWrite (we[0], HIGH);//Turn on previous led
      digitalWrite (ns[2], HIGH);//Turn on previous led
      
    //Standard without traffic buttons  
    if(currentMillis - previousMillis > gointerval) {
      previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
      
      //whenever you see a "Serial.println" this info will show up on the serial monitor
      //Serial monitor is activated from the Arduino app Ctrl-Shift-M
      Serial.println(seqcounter);
      seqcounter++;
      Serial.println("Standard no button logic in case 0 if");
    }
    
    //with traffic breakout of case reset opposite stop traffic time
    if (webuttonstate == HIGH){
      previousns = currentMillis;
      Serial.println("I am traffic reset I have been waiting time in case 0");      
    }
    
    //No traffic and a car is wainting on the red
    if (nsbuttonstate==HIGH && currentMillis - previousns >= notrafficwait){
      previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
      Serial.println(seqcounter);
      seqcounter++;
      Serial.println("No traffic and I have been waiting the right time in case 0");
    }
    break;
    
    case 1:    
      digitalWrite (we[0], LOW);//Turn off previous led
      digitalWrite (ns[2], LOW);//Turn off previous led
      digitalWrite (we[1], HIGH);//Turn on previous led
      digitalWrite (ns[2], HIGH);//Turn on previous led
    if(currentMillis - previousMillis > yellowinterval) {
      previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
       Serial.println(seqcounter);
      seqcounter++;
    }
      break;
      
    case 2:    
      digitalWrite (we[1], LOW);//Turn off previous led
      digitalWrite (ns[2], LOW);//Turn off previous led
      digitalWrite (we[2], HIGH);//Turn on previous led
      digitalWrite (ns[2], HIGH);//Turn on previous led
    if(currentMillis - previousMillis > interval) {
       previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
       Serial.println(seqcounter);
      seqcounter++;
    }
      break;
      
    case 3: //ns is now green   
      digitalWrite (we[2], LOW);//Turn off previous led
      digitalWrite (ns[2], LOW);//Turn off previous led
      digitalWrite (we[2], HIGH);//Turn on previous led
      digitalWrite (ns[0], HIGH);//Turn on previous led
    if(currentMillis - previousMillis > gointerval) {
       previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
      Serial.println(seqcounter);
      seqcounter++;
      Serial.println("Standard no button logic in case 3 if");
    }
    
    //with traffic breakout of case reset opposite stop traffic time
    if (nsbuttonstate == HIGH){
      previouswe = currentMillis;
      Serial.println("I am traffic reset I have been waiting time in case 3");      
    }
    
    //No traffic and a car is wainting on the red
    if (webuttonstate==HIGH && currentMillis - previouswe >= notrafficwait){
      previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
      Serial.println(seqcounter);
      seqcounter++;
      Serial.println("No traffic and I have been waiting the right time in case 3");
    }
      break;
      
    case 4:    
      digitalWrite (we[2], LOW);//Turn off previous led
      digitalWrite (ns[0], LOW);//Turn off previous led
      digitalWrite (we[2], HIGH);//Turn on previous led
      digitalWrite (ns[1], HIGH);//Turn on previous led
    if(currentMillis - previousMillis > yellowinterval) {
       previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
       Serial.println(seqcounter);
      seqcounter++;
      
    }
      break;
      
    case 5:    
      digitalWrite (we[2], LOW);//Turn off previous led
      digitalWrite (ns[1], LOW);//Turn off previous led
      digitalWrite (we[2], HIGH);//Turn on previous led
      digitalWrite (ns[2], HIGH);//Turn on previous led
    if(currentMillis - previousMillis > interval) {
       previousMillis = currentMillis;
      previousns = currentMillis;
      previouswe = currentMillis;
       Serial.println(seqcounter);
      seqcounter=0;
    }
      break;
      
    default:
      // turn all the LEDs off:
      for (int thisPin = 7; thisPin < 13; thisPin++) {
        digitalWrite(thisPin, LOW);
      }
    } 
  
}
