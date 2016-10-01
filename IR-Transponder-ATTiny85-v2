// IR-Transponder-85-v2 by Aether4E0A 9/30/2016,
// with contributions from GitHub user Janbobis.
// This is meant to be compatible with I-Laps systems.
// Compiles using Arduino IDE 1.6.6.

// *** this code is currently UNTESTED with the I-Laps system ***

// This code meant to run on the ATTiny85 w/ 16MHz ceramic resonator.
// IR diode is OSRAM SFH 4641, with 90 Ohm series resistor, 5v supply.
// pin 1 generates the carrier frequency of 455KHz, and is turned on and off for Serial.

// #include <avr/io.h>       // these libraries are already included by arduino IDE.
// #include <util/delay.h>   // they are here for you to use in atmel studio if you like.

#define usSerialDelay 25 // delay time in us (micro-seconds).

void setup() {
  pinMode(1, OUTPUT); // Set output pin for fastPWM

  // Read the datasheet to learn about the registers and fastPWM mode.
  TCCR1 &= 0; // zero the register
  TCCR1 |= (1<<CTC1) | (1<<PWM1A) | (1<<CS10) | (1<<COM1A1); // set registers
  
  OCR1A = 18; // Results in 455KHz w/ 51% duty cycle
  OCR1C = 34;
}

// Transponder code "1234567" (0E AD DC BA 98 D6)
// visit http://blog.owenson.me/reversing-ilap-race-transponders/ for more info on I-Laps packets.
void loop() {
  sendByte(0x0e);
  sendByte(0xad);
  sendByte(0xdc);
  sendByte(0xba);
  sendByte(0x98);
  sendByte(0xd6);
  delay(4); // wait 4ms before repeating. sending 6 bytes takes roughly 1.56 ms. Period is 5.56 ms.
}

// Special thanks to GitHub user Janbobis.
// This following code inspired by your contribution;
// connecting/disconnecting the fastPWM signal is a nice solution.
// This is done by using "TCCR1 |= _BV(COM1A1);" and "TCCR1 &= ~(_BV(COM1A1));"

void sendByte(uint8_t myBuffer){ // uint8_t is smaller and faster to process than an int or long.
  //write the Serial start bit
  TCCR1 |= _BV(COM1A1);
  delayMicroseconds(usSerialDelay);
    
  //write the 8 data bits
  for (uint8_t i = 0; i < 8; i++){
    if (myBuffer & 1){
      TCCR1 |= _BV(COM1A1); // send '1'
    }
    else{
      TCCR1 &= ~(_BV(COM1A1));  // send '0'
    }
    delayMicroseconds(usSerialDelay);
    myBuffer >>= 1;
  }

  //write the Serial stop bit
  TCCR1 &= ~(_BV(COM1A1));
  delayMicroseconds(usSerialDelay);
}
