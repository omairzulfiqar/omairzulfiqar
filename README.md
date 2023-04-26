- 👋 Hi, I’m @omairzulfiqar
- 👀 I’m interested in robotics, embeddedC, linux
- 🌱 I’m currently learning robotics
- 💞️ I’m looking to collaborate on robotics

<!---
Wyvern91/Wyvern91 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
#include <SPI.h>

// ADS8665 command codes
#define CMD_READ 0x80
#define CMD_WRITE 0x00

// ADS8665 register addresses
#define REG_CONFIG 0x00
#define REG_CHAN0 0x01

// Arduino pins connected to ADS8665
#define PIN_CS 10
#define PIN_DRDY 9

void setup() {
  // initialize serial communication
  Serial.begin(9600);
  
  // initialize ADS8665 SPI communication
  SPI.begin();
  SPI.setClockDivider(SPI_CLOCK_DIV8);
  
  // initialize ADS8665 pins
  pinMode(PIN_CS, OUTPUT);
  pinMode(PIN_DRDY, INPUT);
  
  // set ADS8665 configuration register
  writeRegister(REG_CONFIG, 0x00);
}

void loop() {
  // wait for DRDY signal to go low (data ready)
  while(digitalRead(PIN_DRDY));
  
  // read voltage from channel 0
  uint16_t value = readRegister(REG_CHAN0);
  
  // convert value to voltage (assuming 5V reference voltage and 16-bit resolution)
  float voltage = (value * 5.0) / 65536.0;
  
  // print voltage to serial monitor
  Serial.print("Channel 0 voltage: ");
  Serial.print(voltage, 3);
  Serial.println(" V");
  
  // wait for 1 second before reading again
  delay(1000);
}

void writeRegister(uint8_t reg, uint8_t data) {
  digitalWrite(PIN_CS, LOW);
  SPI.transfer(CMD_WRITE | reg);
  SPI.transfer(data);
  digitalWrite(PIN_CS, HIGH);
}

uint16_t readRegister(uint8_t reg) {
  digitalWrite(PIN_CS, LOW);
  SPI.transfer(CMD_READ | reg);
  uint16_t value = SPI.transfer(0) << 8;
  value |= SPI.transfer(0);
  digitalWrite(PIN_CS, HIGH);
  return value;
}

