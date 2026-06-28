### 1. `setup()` Function

* `setup()` runs **only once** when the Arduino is powered on or reset.
* It is used to initialize hardware and settings.

Example:

void setup() {
  pinMode(13, OUTPUT);    
  Serial.begin(9600);      
}


---

### 2. `loop()` Function

* `loop()` runs **continuously** after `setup()` finishes.
* The Arduino repeatedly executes the code inside `loop()` until power is turned off or the board is reset.

**Example:**


void loop() {
  digitalWrite(13, HIGH);
  delay(1000);
  digitalWrite(13, LOW);
  delay(1000);
}

---

### 3. What happens if you put a long `delay()` inside `loop()`?

A long `delay()` **blocks** the Arduino from doing anything else during that time.

**Example:**


void loop() {
  delay(5000);   // Wait 5 seconds
  Serial.println("Reading Sensor");
}


During the 5-second delay:

* No sensor readings occur.
* No button presses are detected.
* No communication is processed.
* The Arduino simply waits.

---

### 4. Effect on Sensor Reading Responsiveness

Long delays make the system **slow and unresponsive**.

**Example:**
If a motion sensor detects movement during a `delay(5000)`, the Arduino won't read it until the delay finishes. The event may be delayed or even missed.

---

### 5. Non-blocking Alternative

Use the `millis()` function instead of `delay()`.

`millis()` returns the number of milliseconds since the Arduino started, allowing the program to perform multiple tasks without stopping execution.

**Example:**

unsigned long previousTime = 0;
const long interval = 1000;

void setup() {
  Serial.begin(9600);
}

void loop() {
  unsigned long currentTime = millis();

  if (currentTime - previousTime >= interval) {
    previousTime = currentTime;
    Serial.println("Read Sensor");
  }

  // Other tasks run continuously
}

