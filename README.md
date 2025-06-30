
# 🎮 Virtue and Vice Game

The **Virtue and Vice Game** is an interactive arcade-style Java game where the player controls a cannon to shoot falling bars representing virtues and vices. The goal is to shoot only the positive bars (virtues) to earn points and avoid the negative ones (vices) to preserve lives.

---

## 🎯 Objective

* Encourage moral decision-making by rewarding the player for hitting virtues and avoiding vices.
* Test hand-eye coordination with fast-paced bar drops and dynamic visuals.
* Demonstrate key Java concepts including object-oriented design, GUI programming, and real-time event handling.

---

## 🛠️ Built With

* **Programming Language**: Java (tested with Java 17, works with Java 8+)
* **IDE Used**: Visual Studio Code
* **GUI Library**: Java Swing / AWT
* **Build Tools**: Command Line / (Optional: Maven or Gradle)
* **Version Control**: Git and GitHub

---

## 👨‍💻 Concepts Used

* Object-Oriented Programming (OOP): Modular design with classes like Cannon, Ball, Bar, and GamePanel.
* Java Standard APIs: Including `javax.swing.Timer`, `KeyListener`, `Graphics`, and `ActionListener`.
* Event-driven programming to handle real-time keyboard input and game logic.
* GUI rendering using Java Swing for interactive visual elements.
* Collision detection and game state management.

---

## 🕹️ Game Objective

* Shoot falling **virtue bars** (green) to earn points.
* Avoid or do not shoot **vice bars** (red), or you lose a life.
* The game starts with 3 lives. Game ends when all lives are lost.

---

## 🎮 Controls

* **Left Arrow (←)** — Move cannon to the left
* **Right Arrow (→)** — Move cannon to the right
* **Spacebar** — Shoot a ball from the cannon
* **Start Button** — Begin the game
* **Pause Button** — Pause/unpause the game

---

## 💡 Gameplay Features

* Each virtue hit gives **+10 points**.
* Each missed bar or hit on a vice results in **−1 life**.
* Game includes **cloud, rain, and star visual effects**.
* **Color themes and cannon appearance change every 4 seconds**.
* Positive and negative bars display relevant motivational messages.

---

## 📊 Results

* Average game session lasts around 2–3 minutes.
* Internal testing showed an average score of \~240 points per game.
* The game runs at approximately 60 FPS on most mid-range laptops.
* Users found visual changes engaging and gameplay addictive.

---

## 🔍 Observations

* Dynamic background and theme changes enhance user attention.
* Most player lives were lost due to missed bars, not shooting vices.
* Clean OOP architecture made feature expansion and debugging easier.

---

## ⚙️ Tools and Technologies

* Java SDK (v17 preferred)
* Visual Studio Code with Java Extension Pack
* Java Swing/AWT for GUI
* JDK libraries for timer, threading, input handling
* Git for version control

---

## 🌱 Future Scope

* Add power-ups such as shield, double-shots, or freeze time.
* Implement a local or online leaderboard to track high scores.
* Port the game to mobile (Android/Flutter).
* Add accessibility features like color-blind mode and key remapping.
* Introduce multiplayer (co-op or versus) mode.

---

## 📝 License

This project is licensed under MIT.

## 🚀 How to Run the Project

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-username>/virtue-vice-game.git
   cd virtue-vice-game
   ```

2. **Compile the code**

   ```bash
   javac -d bin src/*.java
   ```

3. **Run the game**

   ```bash
   java -cp bin Main
   ```
