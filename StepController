const int M01 = 2;
const int M11 = 3;
const int M02 = 4;
const int M12 = 5;
const int M03 = 6;
const int M13 = 7;
const int M04 = 8;
const int M14 = 9;
const int M05 = 10;
const int M15 = 11;
const int M06 = 12;
const int M16 = 13;

String incomingCommand = "";

bool isAllDigits(const char* s) {
  if (s == NULL || *s == '\0') return false;
  for (int i = 0; s[i] != '\0'; i++) {
    if (!isdigit((unsigned char)s[i])) return false;
  }
  return true;
}

void handleCommand(const char* command) {
  char buf[16];
  strncpy(buf, command, sizeof(buf));
  buf[sizeof(buf) - 1] = '\0';

  char* motor = strtok(buf, ":");
  char* value = strtok(NULL, ":");

  if (motor == NULL || value == NULL) return;
  if (isAllDigits(value)) {
    int v = atoi(value);
    if (strcmp(motor, "M1") == 0) {
      digitalWrite(M01, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M11, v == 4 || v == 8 ? HIGH : LOW);
    }
    else if (strcmp(motor, "M2") == 0) {
      digitalWrite(M02, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M12, v == 4 || v == 8 ? HIGH : LOW);
    }
    else if (strcmp(motor, "M3") == 0) {
      digitalWrite(M03, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M13, v == 4 || v == 8 ? HIGH : LOW);
    }
    else if (strcmp(motor, "M4") == 0) {
      digitalWrite(M04, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M14, v == 4 || v == 8 ? HIGH : LOW);
    }
    else if (strcmp(motor, "M5") == 0) {
      digitalWrite(M05, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M15, v == 4 || v == 8 ? HIGH : LOW);
    }
    else if (strcmp(motor, "M6") == 0) {
      digitalWrite(M06, v == 2 || v == 8 ? HIGH : LOW);
      digitalWrite(M16, v == 4 || v == 8 ? HIGH : LOW);
    }
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(M01, OUTPUT); pinMode(M11, OUTPUT);
  pinMode(M02, OUTPUT); pinMode(M12, OUTPUT);
  pinMode(M03, OUTPUT); pinMode(M13, OUTPUT);
  pinMode(M04, OUTPUT); pinMode(M14, OUTPUT);
  pinMode(M05, OUTPUT); pinMode(M15, OUTPUT);
  pinMode(M06, OUTPUT); pinMode(M16, OUTPUT);  // was M15 duplicate
  digitalWrite(M01, LOW); digitalWrite(M11, LOW);
  digitalWrite(M02, LOW); digitalWrite(M12, LOW);
  digitalWrite(M03, LOW); digitalWrite(M13, LOW);
  digitalWrite(M04, LOW); digitalWrite(M14, LOW);
  digitalWrite(M05, LOW); digitalWrite(M15, LOW);
  digitalWrite(M06, LOW); digitalWrite(M16, LOW);
}

void loop() {
  while (Serial.available() > 0) {
    char c = Serial.read();
    if (c == '\n') {
      incomingCommand.trim();
      if (incomingCommand.length() > 0) {
        char buf[16];
        incomingCommand.toCharArray(buf, sizeof(buf));
        handleCommand(buf);
      }
      incomingCommand = "";
    } else {
      incomingCommand += c;
    }
  }
}
