

/**
 * =====================================================================
 * STATE TRANSITION DIAGRAM
 * =====================================================================
 *
 * +-----------------------[ Power On ]-----------------------+
 * |                                                          |
 * v                                                          |
 * +--------+      btnCoin       +---------------+                 |
 * |  IDLE  |------------------->| COIN_INSERTED |                 |
 * +--------+                    +---------------+                 |
 * ^                             |      |                     |
 * |                             |      | btnCancel           |
 * |                             |      v                     |
 * |                             |  [ Go to IDLE ]            |
 * |                             |                            |
 * |                             v btnSelect                  |
 * |                    +---------------+                     |
 * |                    | ITEM_SELECTED |                     |
 * |                    +---------------+                     |
 * |                             |      |                     |
 * |                             |      | btnCancel           |
 * |            Auto Transition  v      v                     |
 * |           +-------------+  [ Go to IDLE ]                |
 * +-----------| DISPENSING  |                                |
 * +-------------+                                |
 * =====================================================================
 */


enum VendingState {
  IDLE,
  COIN_INSERTED,
  ITEM_SELECTED,
  DISPENSING
};


void transitionTo(VendingState newState);
String stateToString(VendingState state);
void updateLEDs();

// --- 3. Pin Definitions ---
const int btnCoin   = 2;
const int btnSelect = 3;
const int btnCancel = 4;

const int ledCoin   = 8;  
const int ledSelect = 9;  
const int ledDisp   = 10; 

VendingState currentState = IDLE;


bool lastBtnCoinState   = HIGH;
bool lastBtnSelectState = HIGH;
bool lastBtnCancelState = HIGH;

unsigned long dispenseStartTime = 0;
const unsigned long dispenseDuration = 3000; 

void setup() {
  Serial.begin(9600);
  
  pinMode(btnCoin, INPUT_PULLUP);
  pinMode(btnSelect, INPUT_PULLUP);
  pinMode(btnCancel, INPUT_PULLUP);
  
  pinMode(ledCoin, OUTPUT);
  pinMode(ledSelect, OUTPUT);
  pinMode(ledDisp, OUTPUT);
  
  Serial.println("Vending Machine Initialized. State: IDLE");
  updateLEDs();
}

void loop() {
  bool btnCoinPressed   = (digitalRead(btnCoin) == LOW);
  bool btnSelectPressed = (digitalRead(btnSelect) == LOW);
  bool btnCancelPressed = (digitalRead(btnCancel) == LOW);

  switch (currentState) {
    case IDLE:
      if (btnCoinPressed && lastBtnCoinState == HIGH) {
        transitionTo(COIN_INSERTED);
      }
      break;
      
    case COIN_INSERTED:
      if (btnCancelPressed && lastBtnCancelState == HIGH) {
        Serial.println("Action: Refund issued.");
        transitionTo(IDLE);
      } 
      else if (btnSelectPressed && lastBtnSelectState == HIGH) {
        transitionTo(ITEM_SELECTED);
      }
      break;
      
    case ITEM_SELECTED:
      if (btnCancelPressed && lastBtnCancelState == HIGH) {
        Serial.println("Action: Transaction cancelled. Refund issued.");
        transitionTo(IDLE);
      } 
      else {
        transitionTo(DISPENSING);
        dispenseStartTime = millis(); 
      }
      break;
      
    case DISPENSING:
      if (millis() - dispenseStartTime >= dispenseDuration) {
        Serial.println("Action: Item dropped! Thank you.");
        transitionTo(IDLE);
      }
      break;
  }

  lastBtnCoinState   = digitalRead(btnCoin);
  lastBtnSelectState = digitalRead(btnSelect);
  lastBtnCancelState = digitalRead(btnCancel);
  
  delay(50); 
}


void transitionTo(VendingState newState) {
  Serial.print("Transition: ");
  Serial.print(stateToString(currentState));
  Serial.print(" -> ");
  Serial.println(stateToString(newState));
  
  currentState = newState;
  updateLEDs();
}

void updateLEDs() {
  switch (currentState) {
    case IDLE:
      digitalWrite(ledCoin, LOW);
      digitalWrite(ledSelect, LOW);
      digitalWrite(ledDisp, LOW);
      break;
    case COIN_INSERTED:
      digitalWrite(ledCoin, HIGH);
      digitalWrite(ledSelect, LOW);
      digitalWrite(ledDisp, LOW);
      break;
    case ITEM_SELECTED:
      digitalWrite(ledCoin, LOW);
      digitalWrite(ledSelect, HIGH);
      digitalWrite(ledDisp, LOW);
      break;
    case DISPENSING:
      digitalWrite(ledCoin, LOW);
      digitalWrite(ledSelect, LOW);
      digitalWrite(ledDisp, HIGH);
      break;
  }
}

String stateToString(VendingState state) {
  switch (state) {
    case IDLE:          return "IDLE";
    case COIN_INSERTED: return "COIN_INSERTED";
    case ITEM_SELECTED: return "ITEM_SELECTED";
    case DISPENSING:    return "DISPENSING";
    default:            return "UNKNOWN";
  }
}