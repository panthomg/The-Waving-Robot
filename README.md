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

---

### 🎨 Design Your Own Body
**The Waving Robot** is just a brain—it needs a body! 
*   **Materials:** Use cardboard, foam, 3D printing, or wood.
*   **The Arm:** Attach the Servo to any "arm" part.
*   **The Eyes:** Place the LCD screen in the "head" area.
*   **The Vision:** Make sure the Ultrasonic Sensor has a clear view forward.

**Share your custom designs using the hashtag #SmartWavingRobot!**
