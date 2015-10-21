#include <LiquidCrystal.h>

// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(8,9,4,5,6,7);

int ledPin = 13;  // use the built in LED on pin 13 of the Uno
int state = 1;
int flag = 0;  // make sure that you return the state only once
char d = 0;
char OP1[10];
char OP2[10];
char oprtr;
int opflag = 1, resultflag = 0;
int sizeofop1 = -1;
int sizeofop2 = -1;
int i = 0, j;
int num1 = 0, num2 = 0, temp, poww, result;

int a = 2,b=3,mm;

void setup() {
lcd.begin(16,2);
lcd.clear();

for(mm=0;mm<8;mm++){ 
    lcd.setCursor(mm,0);
    lcd.print('.');
    lcd.setCursor(15-mm,0);
    lcd.print('.');
    delay(700);
    lcd.clear();
}

lcd.clear();
lcd.setCursor(5,0);
lcd.print("CSE322");
lcd.setCursor(0,1);
lcd.print("Embedded Systems");
delay(2000);
lcd.clear();
lcd.setCursor(0,0);
lcd.print("Dr. Rajesh Kumar");
delay(1000);
lcd.clear();
lcd.setCursor(1,0);
lcd.print("Welcome to our");
lcd.setCursor(5,1);
lcd.print("Project");
delay(1000);
lcd.clear();

for(mm=0;mm<8;mm++){ 
    lcd.setCursor(mm,0);
    lcd.print('.');
    lcd.setCursor(15-mm,0);
    lcd.print('.');
    delay(100);
    lcd.clear();
}
lcd.clear();
lcd.setCursor(3,0);
lcd.print("Calculator");

// sets the pins as outputs:
    pinMode(ledPin, OUTPUT);
    digitalWrite(ledPin, LOW);
    
    Serial.begin(115200); // Default connection rate for my BT module
    //Serial.println("Enter the input to increment : ");

}

int power(int x, int y)
{
int temp =1, k;
for(k=0;k<y;k++)
  temp = temp * x;
return temp;
}

void loop() {
  
  
  //if some data is sent, read it and save it in the state variable
    if(Serial.available()){
       d = Serial.read();
       
       if(d=='+' || d=='-' || d=='*' || d=='/' || d=='%')
       {
         oprtr = d;
         if(resultflag)
          {
            num1 = result;
          }
      //   Serial.println((char)d);
         opflag=0;
         // opflag =1 means first operator is going on. store in OP1. else: OP2
         // Serial.println("Received operator ");
         // Serial.println(oprtr);
         
       }
       else if(d=='x')
       {
         memset(OP1, 0, 10);
         memset(OP2, 0, 10);
         opflag =1;
         sizeofop1=-1;
         sizeofop2=-1;
         num1 = 0;
         num2 = 0;
         //Serial.println("Flushed everything");
         //Serial.println(OP1);
         //Serial.println(OP2);
         //Serial.println("Done.");
       }
       else if(d=='=')
       {
         //Serial.println("Received values: ");
         //Serial.print(OP1);
         //Serial.print(" and ");
         //Serial.println(OP2);
         //Serial.print("Result: ");
         
         // compute op1 and op2
         if(!resultflag)
         {
           temp = sizeofop1;
           for(j=0;j<=sizeofop1;j++)
           {
             poww = (int)power(10,temp);
             num1 = num1 + (OP1[j]-48)*poww;
             temp--;         
           }
         }
         temp = sizeofop2;
         for(j=0;j<=sizeofop2;j++)
         {
           poww = (int)power(10,temp);
           num2 = num2 + (OP2[j]-48)*poww;
           temp--;         
         }
         
         if(oprtr=='+')
           result = num1 + num2;
         else if(oprtr=='-')
           result = num1 - num2;
         else if(oprtr=='*')
           result = num1 * num2;
         else if(oprtr=='/' && num2)
           result = num1 / num2;
         else if(oprtr=='%' && num2)
           result = num1 % num2;
         
         resultflag = 1;
         Serial.println(result);
         lcd.clear();
         lcd.setCursor(0,0);
         lcd.print("Result:");
         lcd.setCursor(0,1);
         lcd.print(result);
         
         
         memset(OP1, 0, 10);
         memset(OP2, 0, 10);
         opflag =1;
         sizeofop1=-1;
         sizeofop2=-1;
         num1 = 0;
         num2 = 0;  
       }
       else if(opflag)
       {
         result =0;
         resultflag=0;
         sizeofop1++;
         OP1[sizeofop1] = d;
         //Serial.print("Received op1 ");
         //Serial.println(OP1);
       }
       else // opflag = 0
       {
         sizeofop2++;
         OP2[sizeofop2] = d;
         //Serial.print("Received op2 ");
         //Serial.println(OP2);
       }
       
       
       //delay(3000);
    }
    
    
  
}
