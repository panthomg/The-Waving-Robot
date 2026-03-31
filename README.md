Here is your project rebranded as **The Waving Robot**. I have updated the code headers, the startup screen text, and the project documentation to reflect the new name while keeping the advanced gesture-recognition engine intact.

---

# 👋 The Waving Robot (Open Source)
**A friendly companion that knows when you're saying hello!**

**The Waving Robot** is an interactive open-source project designed to be the "brain" for any character you want to build. Whether you make a body out of cardboard, 3D-printed parts, or LEGO, this code gives your creation a personality.

### ✨ How it Works
*   **Awareness:** It uses an ultrasonic sensor to "see" how far away you are.
*   **Gesture Recognition:** If you wave your hand 3 times quickly in front of it, it enters "Handshake Mode" with heart-eyes!
*   **Smooth Motion:** Instead of robotic, jerky movements, it uses math (Sine waves) to move its arm naturally.
*   **Expressions:** The LCD screen changes eyes and status messages based on its "mood."

---

### 🛠 Hardware Needed
1.  **Arduino Uno** (or any compatible board)
2.  **Ultrasonic Sensor** (HC-SR04)
3.  **LCD Display** (16x2 with I2C adapter)
4.  **Servo Motor** (SG90 or MG90S)
5.  **DHT11 Sensor** (Optional: for temperature/humidity)

---

### 🔌 Wiring Guide
| Component | Arduino Pin |
| :--- | :--- |
| **Ultrasonic Trig** | Pin 4 |
| **Ultrasonic Echo** | Pin 5 |
| **Servo Signal** | Pin 9 |
| **DHT11 Data** | Pin 2 |
| **LCD SDA** | A4 |
| **LCD SCL** | A5 |
| **Power** | 5V and GND |

---

### 💻 The Official Code

```cpp
/*
 * =================================================================
 * PROJECT: THE WAVING ROBOT
 * VERSION: 1.0 (Open Source)
 * DESCRIPTION: Interactive robot with gesture-based waving levels.
 * =================================================================
 */

#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Servo.h>
#include <DHT.h>

// --- PINS ---
#define PIN_DHT 2
#define PIN_TRIG 4
#define PIN_ECHO 5
#define PIN_SERVO 9

// --- OBJECTS ---
DHT dht(PIN_DHT, DHT11);
LiquidCrystal_I2C lcd(0x27, 16, 2);
Servo handServo;

// --- GESTURE SYSTEM VARIABLES ---
int currentDist = 200;
int lastDist = 200;
int shakeCount = 0;
unsigned long lastShakeTime = 0;
bool isHandClose = false;

// --- MOOD & WAVE LEVELS ---
enum RobotState { IDLE, SPOTTED, GREETING, HANDSHAKE };
RobotState currentState = IDLE;

// --- CUSTOM SPRITES (LCD Eyes) ---
byte eyeNeutral[8] = {0b00000, 0b01110, 0b11111, 0b11111, 0b11111, 0b01110, 0b00000, 0b00000};
byte eyeHappy[8]   = {0b00000, 0b00000, 0b00100, 0b01010, 0b10001, 0b00000, 0b00000, 0b00000};
byte eyeHeart[8]   = {0b00000, 0b01010, 0b11111, 0b11111, 0b01110, 0b00100, 0b00000, 0b00000};

void setup() {
  Serial.begin(115200);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
  
  handServo.attach(PIN_SERVO);
  handServo.write(90);

  lcd.init();
  lcd.backlight();
  
  lcd.createChar(0, eyeNeutral);
  lcd.createChar(1, eyeHappy);
  lcd.createChar(3, eyeHeart);

  dht.begin();
  
  lcd.setCursor(0, 0); lcd.print("THE WAVING ROBOT");
  lcd.setCursor(0, 1); lcd.print("SYSTEM: ONLINE");
  delay(2000);
  lcd.clear();
}

void loop() {
  updateDistance();
  recognizeGesture();
  processAI();
  updateDisplay();
  waveEngine();
}

// --- 1. RADAR SCANNING ---
void updateDistance() {
  static unsigned long lastPing = 0;
  if (millis() - lastPing > 50) { 
    digitalWrite(PIN_TRIG, LOW); delayMicroseconds(2);
    digitalWrite(PIN_TRIG, HIGH); delayMicroseconds(10);
    digitalWrite(PIN_TRIG, LOW);
    long dur = pulseIn(PIN_ECHO, HIGH, 20000);
    int d = (dur == 0) ? 200 : dur * 0.034 / 2;
    
    lastDist = currentDist;
    currentDist = (currentDist * 0.4) + (d * 0.6); // Filter for smoothness
    lastPing = millis();
  }
}

// --- 2. GESTURE RECOGNITION (The "WAVE" Detector) ---
void recognizeGesture() {
  unsigned long now = millis();

  // If a hand is seen at < 25cm and then pulled away, count it as a "wave pulse"
  if (currentDist < 25 && !isHandClose) {
    isHandClose = true;
    shakeCount++;
    lastShakeTime = now;
  } 
  else if (currentDist > 35 && isHandClose) {
    isHandClose = false;
  }

  // Reset pulse count if user stops waving for 2 seconds
  if (now - lastShakeTime > 2000) {
    shakeCount = 0;
  }

  // If user waves 3 times: Enter Handshake State
  if (shakeCount >= 3) {
    currentState = HANDSHAKE;
    lastShakeTime = now + 3000; // Stay in this state for 3 seconds
    shakeCount = 0;
  }
}

// --- 3. DECISION LOGIC ---
void processAI() {
  unsigned long now = millis();
  
  if (currentState == HANDSHAKE) {
    if (now > lastShakeTime) currentState = IDLE; 
    return;
  }

  if (currentDist < 20) {
    currentState = GREETING;
  } else if (currentDist < 60) {
    currentState = SPOTTED;
  } else {
    currentState = IDLE;
  }
}

// --- 4. THE WAVE ENGINE (Smooth movement) ---
void waveEngine() {
  static float servoPos = 90;
  float targetPos = 90;
  unsigned long ms = millis();

  switch (currentState) {
    case IDLE:
      targetPos = 90 + (sin(ms / 1000.0) * 5); // Subtle "breathing" movement
      break;

    case SPOTTED:
      targetPos = 90 + (sin(ms / 600.0) * 25); // Polite, slow wave
      break;

    case GREETING:
      targetPos = 90 + (sin(ms / 250.0) * 50); // Fast, excited wave
      break;

    case HANDSHAKE:
      targetPos = 140 + (sin(ms / 100.0) * 20); // Rapid "handshake" vibration
      break;
  }

  // Smooth interpolation
  if (servoPos < targetPos) servoPos += 0.5;
  if (servoPos > targetPos) servoPos -= 0.5;
  handServo.write((int)servoPos);
  delay(1); 
}

// --- 5. UI DISPLAY ---
void updateDisplay() {
  static unsigned long lastUI = 0;
  if (millis() - lastUI < 300) return;
  lastUI = millis();

  int offset = (currentState == IDLE) ? 0 : -2;
  
  // Update Eyes
  lcd.setCursor(6 + offset, 0);
  if (currentState == HANDSHAKE) lcd.write(3); 
  else if (currentState == GREETING) lcd.write(1);
  else lcd.write(0); 

  lcd.setCursor(8 + offset, 0);
  lcd.print("."); // Nose

  lcd.setCursor(10 + offset, 0);
  if (currentState == HANDSHAKE) lcd.write(3);
  else if (currentState == GREETING) lcd.write(1);
  else lcd.write(0);

  // Update Status Text
  lcd.setCursor(0, 1);
  switch (currentState) {
    case IDLE:      lcd.print("  STATUS: IDLE  "); break;
    case SPOTTED:   lcd.print("   OH! HI...    "); break;
    case GREETING:  lcd.print(" HELLO FRIEND!  "); break;
    case HANDSHAKE: lcd.print(" <3 HANDSHAKE <3"); break;
  }
}
```

---

### 🎨 Design Your Own Body
**The Waving Robot** is just a brain—it needs a body! 
*   **Materials:** Use cardboard, foam, 3D printing, or wood.
*   **The Arm:** Attach the Servo to any "arm" part.
*   **The Eyes:** Place the LCD screen in the "head" area.
*   **The Vision:** Make sure the Ultrasonic Sensor has a clear view forward.

**Share your custom designs using the hashtag #SmartWavingRobot!**
