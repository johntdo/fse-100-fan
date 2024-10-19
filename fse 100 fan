#include <Servo.h>
#include <LiquidCrystal.h>

// user inputs and etc
int base_change = 5; // how fast the base turns - default
int mode_num = 1; // fan mode, counter - units
int mode_num_mod = mode_num % 2;
int motor_speed = 100; // fan speed - default
int motor_speed_norm = (motor_speed/255)*10; // fan speed normalized to 0 - 10 - 5? is the default
int temp_max_F = 80; // max temp - default
int buzz_on = 0;
int sound = 300;

// servo
Servo myservo;
int pos = 0;
const int col = 10;// pin 10
int counter_2 = 0;  // cause servo only spins up to a certain amount, so it counts up til it reaches 180 degrees / number of degrees per turn (base_change)

// lights
int led_green = 7;//pin 7
int led_red = 8;// pin 8

// motor
int motor = 6; //pin 6

// buzzer
int buzzer = 9; //pin 9

// temp
const int TEMP = 0;  //Temp Sensor is on pin A0
int val = 0;         //Variable to hold integer analog reading
float temp_C;
float temp_F;

// lcd
LiquidCrystal lcd_1(12, 11, 5, 4, 3, 2);
int counter;

void setup() {
  Serial.begin(9600);

  // use pin col for servo
  myservo.attach(col);

  // lights are output
  pinMode(led_green, OUTPUT);
  pinMode(led_red, OUTPUT);

  // buzzer is an output
  pinMode(buzzer, OUTPUT);

  lcd_1.begin(16, 2);  // Set up the number of columns and rows on the LCD.
  digitalWrite(led_green, HIGH);
  for (counter = 0; counter < 2; ++counter) {
    lcd_1.clear();
    lcd_1.print("Start-up.");
    delay(500);  // Wait for 500 millisecond(s)
    lcd_1.clear();
    lcd_1.print("Start-up..");
    delay(500);  // Wait for 500 millisecond(s)
    lcd_1.clear();
    lcd_1.print("Start-up...");
    delay(500);  // Wait for 500 millisecond(s)
  }
  digitalWrite(led_green, LOW);
}

void loop() {
  // sets the position of the servo to pos, 0 at start up
  myservo.write(pos);

  // temp readings
  val = analogRead(TEMP);
  double tempK = log(10000.0 * ((1024.0 / val - 1)));
  tempK = 1 / (0.001129148 + (0.000234125 + (0.0000000876741 * tempK * tempK)) * tempK);
  // Temp Kelvin
  temp_C = tempK - 273.15;  // Convert Kelvin to Celcius
  temp_F = (temp_C * 1.8) + 32; // C to F
  Serial.print("T = ");
  Serial.print(temp_F);
  Serial.println(" deg. F");

  // turns on motor when temp_max reaches a threshold, red if yes, green if no
  if (temp_F >= temp_max_F){
    // motor on
    digitalWrite(motor, HIGH);
    // motor speed
    analogWrite(motor, motor_speed);
    switch (buzz_on){
      case 0:
        tone(buzzer, sound);
        buzz_on = 1;
        break;
      case 1:
        noTone(buzzer);
        break;
    }
    digitalWrite(led_green, LOW);
    digitalWrite(led_red, HIGH);

    // servo
    if (counter_2 < 180/base_change) {
      pos += base_change;
      counter_2 += 1;
    } else if (counter_2 <= 360/base_change) {
      pos += -base_change;
      counter_2 += 1;
    } else {
      counter_2 = 0;
    }
    Serial.println("1");
  } else {
    lcd_1.clear();
    noTone(buzzer);
    analogWrite(motor, 0);
    digitalWrite(motor, LOW);
    buzz_on = 0;
    digitalWrite(led_green, HIGH);
    digitalWrite(led_red, LOW);
    Serial.println("2");
    lcd_1.clear();
  }

  // lcd
  switch (mode_num_mod){
    case 1:
      lcd_1.clear();
      lcd_1.print("Temperature ");
      lcd_1.print(temp_F,DEC);
      lcd_1.setCursor(0, 1);
      lcd_1.print("Unit: F");
      Serial.println("3");
      break;
    case 2:
      lcd_1.clear();
      lcd_1.print("Temperature ");
      lcd_1.print(temp_C,DEC);
      lcd_1.setCursor(0, 1);
      lcd_1.print("Unit: C");
      Serial.println("4");
      break;
  }

  delay(1000);  // Delay a little bit to improve simulation performance
}
