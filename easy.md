//# smart-home
//simple smart home project for begginers
//curt 
#include <Stepper.h>
const int stepsPerRevolution = 260;  
Stepper myStepper(stepsPerRevolution, 8, 6, 5, 3);


//living room 
const int living_1=13; 


//master room
const int master_1=12;
const int masterfan=11;

//reception
const int reception_1=10;
const int receptionfan=9;
const int ldr=A0;
const int fanldr=A2;
const int securityldr=A3;
const int temp=A1;
const int buzzerPin = 7;

// flags 
int flag_1=0;
int sens = 0 ;
int fla = 0 ;
int countpeople=1;
int delta=0 ;
int star=1 ;
int flag=0;
int state;
int automatic=0;


float tempreture;
float valuetemp;
float volt;
float valueldr;
float valuefanldr;
float valuesecurityldr;


void setup() 
{
 Serial.begin(9600);

 //curt 
 myStepper.setSpeed(15);


 //living room

 pinMode(living_1,OUTPUT);

 //master room
 pinMode(master_1,OUTPUT);

 pinMode(masterfan,OUTPUT);

//reception
 pinMode(reception_1,OUTPUT);

 pinMode(receptionfan,OUTPUT);
 // temp

pinMode(7 , OUTPUT);

}




void loop() 
{


if (Serial.available()>0)
{
  state=Serial.read();
flag=0;
}
//-------------------------------------------------------------------------------------------------------------------------------------------------------------------


if (state=='1')
{
  digitalWrite(living_1,HIGH); 

if (flag==0)
{
Serial.println("Bed rooms is lit");  
flag=1;

}

}
else if (state=='0')
{
 digitalWrite(living_1,LOW);
  
if (flag==0)
{
Serial.println("bed room is dark");  
flag=1;
}
}                                                                //bed room
 

//----------------------------------------------------------------------------------------------------------------------------------------------------------------
if (state=='2')
{ //lamp htnwr
digitalWrite(master_1,HIGH);  

if (flag==0)
{
Serial.println("living room is lit");
flag=1;
}
}
else if(state=='3')
{ //lamp ht2fl
digitalWrite(master_1,LOW);  

if (flag==0)
{
Serial.println("living room is dark");  
flag=1;
}
}
// open fan
if (state=='4')                                                   //living room
{if (delta==0)
{
Serial.println("fan now is on");  
 digitalWrite(masterfan,HIGH);  
delta=1;
 
}
}
// close fan
 if (state=='5')
{
  countpeople = 0 ;
 if (delta==1)
{
Serial.println("fan now is off");  
 digitalWrite(masterfan,LOW);   
delta=0;
}
}
//-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

if (state=='6')
{
digitalWrite(reception_1,HIGH);  

if (flag==0)
{
Serial.println("reception is lit");
flag=1;
}
}
else if(state=='7')
{
digitalWrite(reception_1,LOW);  

if (flag==0)
{
Serial.println("reception is dark");  
flag=1;
}
}
                                                          //reception 

//---------------------------------------------------------\
//curt 


if (state=='c') 
{  
  
if (flag_1==0){
 Serial.println("curtains are closed");
  myStepper.step(stepsPerRevolution);
  flag_1=1;}
  

} 

if(state=='o')
{ 
 
   if( flag_1==1){
   Serial.println("curtains are open");
 myStepper.step(-stepsPerRevolution); 
  flag_1=0;}

}

// automatic control 

// -------------------------------------
// -------------------------------------
// -------------------------------------



if (state  == 'a')
{automatic=1;}
if(automatic==1){
  // curtains auto 
  
  if (fla==0){
   Serial.println("you are now on automatic mode");

  fla=1;}
  
//Serial.println(valueldr); 
delay(20);
if (valueldr < 700)                   // change value 
{
  digitalWrite(12, LOW); 
  
if( star==1){
   Serial.println("curtains are open");
 myStepper.step(-stepsPerRevolution); 
  star=0;}
} 
  else
{ 
    digitalWrite(12, HIGH); 

  if (star==0){
 Serial.println("curtains are closed");
  myStepper.step(stepsPerRevolution);
  star=1;} 

 }
  
  // --------------------------------------------
  
  // fan auto 
  
  //Serial.println(valuefanldr); 
delay(200);
if (valuefanldr > 600)                     // change value 
{sens=0;
if (sens==0){
  
  countpeople = countpeople +1 ;
  Serial.println(countpeople);

  sens=1;
}
 

} 
if (countpeople==4)
{

  if (delta==0)
{
Serial.println("master room is dark");  
 digitalWrite(masterfan,HIGH);  
delta=1;
 }

} 

}

valuesecurityldr = analogRead(A3);
valuefanldr = analogRead(A2);
valueldr=analogRead(A0);
volt=1023.0*5.0/valueldr;
if (state=='m')
{automatic=0;}
// temp sensor 

int val = analogRead(temp);

//Serial.println(val); 

delay(20);} 
            
            if (val > 50) 
                 {
                      tone(7, 200);

                      digitalWrite(13, 100);

                      delay(100);

                      noTone(7);

                      digitalWrite(13, LOW);                                 
                  } 
else 
{ 
   noTone(7);
digitalWrite(13, LOW);

 }
 
 
 //// security alarm 


 // Serial.println(valuesecurityldr); 
delay(200);

 
  int secure = 10 ;

if (valuesecurityldr > 750) 
{

   Serial.println("Silent alarm went off ... please deaciate if you just entered the house ");
         for( int i = 4 ; i >= 0 ; i --)
        { 
              Serial.println(i); 
                        delay(1000);


        }

    
secure = 0 ;

}


 if ( state != 's' && secure == 0 ) 
  
 
 {            for(int j = 10 ; j >0 ; j -- )
 
          {          tone(7, 200);

                      digitalWrite(13, 100);

                      delay(1000);

                      noTone(7);

                      digitalWrite(13, LOW);
} 
}
}
