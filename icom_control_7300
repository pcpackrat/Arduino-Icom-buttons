#include <SoftwareSerial.h>

// Create a new SoftwareSerial object for communication with the Icom IC-7300
SoftwareSerial icomSerial(-0, 2);  // no-RX, TX

int lastButton = 0;
int debounceDelay = 50; // debounce delay in milliseconds
unsigned long lastDebounceTime = 0;
bool buttonPressed = false;

void setup() {
  // initialize serial communication at 9600 baud
  Serial.begin(9600);
  
  // Initialize the SoftwareSerial object
  icomSerial.begin(9600);

  // Set the pinMode for Port D2 as an output
  pinMode(2, OUTPUT);
}
// Define the voltage ranges for each button
// This should be close for most but I noticed odd voltages with some arduinos.
// you can use the voltage test sketch to see where each of your buttons land.

const float BUTTON_VOLTAGES[][2] = {
  { 0.41, 0.52 }, // Button 4 or 8
  { 0.70, 0.89 }, // Button 3 or 7
  { 1.10, 1.33 }, // Button 2 or 6
  { 1.60, 1.87 }, // Button 1 or 5
  { 0.00, 0.05 }, // Red Button
 };

// Define the CI-V commands for each button 0x94 is address for IC-7300
// Button 1-8 have 2 commands.  The first command stops anything playing back. (In case you hit the wrong button)
const byte BUTTON_COMMANDS[][25] = {
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x01, 0xfd }, // Button 1
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x02, 0xfd }, // Button 2
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x03, 0xfd }, // Button 3
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x04, 0xfd }, // Button 4
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x05, 0xfd }, // Button 5
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x06, 0xfd }, // Button 6
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x07, 0xfd }, // Button 7
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd, 0xfe, 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x08, 0xfd }, // Button 8
  { 0xfe, 0xfe, 0x94, 0xe0, 0x28, 0x00, 0x00, 0xfd }, // Red Button
};

void loop() {
  // read the voltage on analog input pin A5
  int sensorValue = analogRead(A5);

  // convert the sensor value to voltage
  float voltage = sensorValue * (5.0 / 1023.0);

  // check the voltage range and return a message
  int currentButton = 0;
  if (voltage >= BUTTON_VOLTAGES[0][0] && voltage <= BUTTON_VOLTAGES[0][1]) {
    currentButton = 1;
  } else if (voltage >= BUTTON_VOLTAGES[1][0] && voltage <= BUTTON_VOLTAGES[1][1]) {
    currentButton = 2;
  } else if (voltage >= BUTTON_VOLTAGES[2][0] && voltage <= BUTTON_VOLTAGES[2][1]) {
    currentButton = 3;
  } else if (voltage >= BUTTON_VOLTAGES[3][0] && voltage <= BUTTON_VOLTAGES[3][1]) {
    currentButton = 4;
  }
 // read the voltage on analog input pin A4
  int sensorValue1 = analogRead(A4);

  // convert the sensor value to voltage
  float voltage1 = sensorValue1 * (5.0 / 1023.0);

  // check the voltage range and return a message
  if (voltage1 >= BUTTON_VOLTAGES[0][0] && voltage1 <= BUTTON_VOLTAGES[0][1]) {
    currentButton = 5;
  } else if (voltage1 >= BUTTON_VOLTAGES[1][0] && voltage1 <= BUTTON_VOLTAGES[1][1]) {
    currentButton = 6;
  } else if (voltage1 >= BUTTON_VOLTAGES[2][0] && voltage1 <= BUTTON_VOLTAGES[2][1]) {
    currentButton = 7;
  } else if (voltage1 >= BUTTON_VOLTAGES[3][0] && voltage1 <= BUTTON_VOLTAGES[3][1]) {
    currentButton = 8;
  } else if (voltage1 >= BUTTON_VOLTAGES[4][0] && voltage1 <= BUTTON_VOLTAGES[4][1]) {
    currentButton = 9;
  }

  // check if a button is currently being pressed and debounce
  if (currentButton != 0 && (millis() - lastDebounceTime) > debounceDelay && !buttonPressed) {
    // check the voltage range again to make sure the button is still pressed
    int sensorValue2 = analogRead(A5);
    float voltage2 = sensorValue2 * (5.0 / 1023.0);
    int sensorValue3 = analogRead(A4);
    float voltage3 = sensorValue3 * (5.0 / 1023.0);
    
    if (currentButton == 1 && voltage2 >= BUTTON_VOLTAGES[0][0] && voltage2 <= BUTTON_VOLTAGES[0][1]) {
      Serial.println("Button 1 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[0], sizeof(BUTTON_COMMANDS[0]));
      
    } else if (currentButton == 2 && voltage2 >= BUTTON_VOLTAGES[1][0] && voltage2 <= BUTTON_VOLTAGES[1][1]) {
      Serial.println("Button 2 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[1], sizeof(BUTTON_COMMANDS[1]));
      
    } else if (currentButton == 3 && voltage2 >= BUTTON_VOLTAGES[2][0] && voltage2 <= BUTTON_VOLTAGES[2][1]) {
      Serial.println("Button 3 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[2], sizeof(BUTTON_COMMANDS[2]));

    } else if (currentButton == 4 && voltage2 >= BUTTON_VOLTAGES[3][0] && voltage2 <= BUTTON_VOLTAGES[3][1]) {
      Serial.println("Button 4 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[3], sizeof(BUTTON_COMMANDS[3]));  

    } else if (currentButton == 5 && voltage3 >= BUTTON_VOLTAGES[0][0] && voltage3 <= BUTTON_VOLTAGES[0][1]) {
      Serial.println("Button 5 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[4], sizeof(BUTTON_COMMANDS[4]));  
      
    } else if (currentButton == 6 && voltage3 >= BUTTON_VOLTAGES[1][0] && voltage3 <= BUTTON_VOLTAGES[1][1]) {
      Serial.println("Button 6 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[5], sizeof(BUTTON_COMMANDS[5]));

    } else if (currentButton == 7 && voltage3 >= BUTTON_VOLTAGES[2][0] && voltage3 <= BUTTON_VOLTAGES[2][1]) {
      Serial.println("Button 7 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[6], sizeof(BUTTON_COMMANDS[6]));

    } else if (currentButton == 8 && voltage3 >= BUTTON_VOLTAGES[3][0] && voltage3 <= BUTTON_VOLTAGES[3][1]) {
      Serial.println("Button 8 pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[7], sizeof(BUTTON_COMMANDS[7]));

    } else if (currentButton == 9 && voltage3 >= BUTTON_VOLTAGES[4][0] && voltage3 <= BUTTON_VOLTAGES[4][1]) {
      Serial.println("Red button pressed");
      buttonPressed = true;
      icomSerial.write(BUTTON_COMMANDS[8], sizeof(BUTTON_COMMANDS[8]));

    }
    // save the current button state and time of last debounce
    lastButton = currentButton;
    lastDebounceTime = millis();
  }

  // reset buttonPressed flag when button is released
  if (currentButton == 0) {
    buttonPressed = false;
  }

  // wait a short time before repeating the loop
  delay(100);
}
