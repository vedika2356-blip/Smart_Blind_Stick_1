#define TRIG_PIN    5
#define ECHO_PIN    18

#define BUZZER_PIN  23
#define MOTOR_PIN   22

long duration;
int distance;

void setup() {

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(MOTOR_PIN, OUTPUT);

  Serial.begin(115200);

  Serial.println("SMART BLIND STICK STARTED");
}

void loop() {

  // =================================
  // SEND ULTRASONIC PULSE
  // =================================
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);

  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);

  digitalWrite(TRIG_PIN, LOW);

  // =================================
  // READ ECHO
  // =================================
  duration = pulseIn(ECHO_PIN, HIGH, 30000);

  if (duration == 0) {

    Serial.println("No Echo");

    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(MOTOR_PIN, LOW);

    delay(100);
    return;
  }

  // =================================
  // CALCULATE DISTANCE
  // =================================
  distance = duration * 0.034 / 2;

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  // =================================
  // VERY CLOSE (<15 cm)
  // =================================
  if (distance > 0 && distance < 15) {

    Serial.println("🚨 VERY CLOSE");

    digitalWrite(MOTOR_PIN, HIGH);

    for (int i = 0; i < 3; i++) {

      digitalWrite(BUZZER_PIN, HIGH);
      delay(70);

      digitalWrite(BUZZER_PIN, LOW);
      delay(70);
    }
  }

  // =================================
  // MEDIUM (15-30 cm)
  // =================================
  else if (distance >= 15 && distance < 30) {

    Serial.println("⚠ MEDIUM");

    digitalWrite(MOTOR_PIN, HIGH);

    digitalWrite(BUZZER_PIN, HIGH);
    delay(200);

    digitalWrite(BUZZER_PIN, LOW);
    delay(200);

    digitalWrite(MOTOR_PIN, LOW);
  }

  // =================================
  // FAR (30-50 cm)
  // =================================
  else if (distance >= 30 && distance <= 50) {

    Serial.println("ℹ FAR");

    digitalWrite(MOTOR_PIN, HIGH);

    digitalWrite(BUZZER_PIN, HIGH);
    delay(400);

    digitalWrite(BUZZER_PIN, LOW);

    digitalWrite(MOTOR_PIN, LOW);

    delay(400);
  }

  // =================================
  // SAFE
  // =================================
  else {

    Serial.println("✅ SAFE");

    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(MOTOR_PIN, LOW);

    delay(100);
  }
}
