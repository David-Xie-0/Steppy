const int stepPin1 = 2;
const int stepPin2 = 4;
const int stepPin3 = 6;
const int stepPin4 = 8;
const int stepPin5 = 10;
const int stepPin6 = 12;
const int enPin1 = 3;
const int enPin2 = 5;
const int enPin3 = 7;
const int enPin4 = 9;
const int enPin5 = 11;
const int enPin6 = 13;
const int fanPin = A0;

unsigned long M1Interval, M2Interval, M3Interval, M4Interval, M5Interval, M6Interval;
unsigned long M1Step, M2Step, M3Step, M4Step, M5Step, M6Step;

String incomingCommand = "";

unsigned long microstep1 = 1, microstep2 = 1, microstep3 = 1;
unsigned long microstep4 = 1, microstep5 = 1, microstep6 = 1;
unsigned long lastStep1 = 0, lastStep2 = 0, lastStep3 = 0;
unsigned long lastStep4 = 0, lastStep5 = 0, lastStep6 = 0;

struct Note { const char* name; unsigned long interval; };

const Note notes[] = {
  { "C0",  30578UL }, { "C#0", 28860UL }, { "D0",  27240UL }, { "D#0", 25712UL },
  { "E0",  24270UL }, { "F0",  22908UL }, { "F#0", 21620UL }, { "G0",  20408UL },
  { "G#0", 19262UL }, { "A0",  18182UL }, { "A#0", 17153UL }, { "B0",  16198UL },
  { "C1",  15289UL }, { "C#1", 14430UL }, { "D1",  13620UL }, { "D#1", 12856UL },
  { "E1",  12135UL }, { "F1",  11454UL }, { "F#1", 10810UL }, { "G1",  10204UL },
  { "G#1",  9631UL }, { "A1",   9091UL }, { "A#1",  8576UL }, { "B1",   8099UL },
  { "C2",   7645UL }, { "C#2",  7215UL }, { "D2",   6810UL }, { "D#2",  6428UL },
  { "E2",   6068UL }, { "F2",   5727UL }, { "F#2",  5405UL }, { "G2",   5102UL },
  { "G#2",  4816UL }, { "A2",   4545UL }, { "A#2",  4288UL }, { "B2",   4050UL },
  { "C3",   3822UL }, { "C#3",  3608UL }, { "D3",   3405UL }, { "D#3",  3214UL },
  { "E3",   3034UL }, { "F3",   2863UL }, { "F#3",  2703UL }, { "G3",   2551UL },
  { "G#3",  2408UL }, { "A3",   2273UL }, { "A#3",  2144UL }, { "B3",   2025UL },
  { "C4",   1911UL }, { "C#4",  1804UL }, { "D4",   1703UL }, { "D#4",  1607UL },
  { "E4",   1517UL }, { "F4",   1432UL }, { "F#4",  1351UL }, { "G4",   1276UL },
  { "G#4",  1204UL }, { "A4",   1136UL }, { "A#4",  1073UL }, { "B4",   1012UL },
  { "C5",    956UL }, { "C#5",   902UL }, { "D5",    851UL }, { "D#5",   804UL },
  { "E5",    758UL }, { "F5",    715UL }, { "F#5",   676UL }, { "G5",    638UL },
  { "G#5",   602UL }, { "A5",    568UL }, { "A#5",   536UL }, { "B5",    506UL },
  { "C6",    478UL }, { "C#6",   451UL }, { "D6",    426UL }, { "D#6",   402UL },
  { "E6",    379UL }, { "F6",    358UL }, { "F#6",   338UL }, { "G6",    319UL },
  { "G#6",   301UL }, { "A6",    284UL }, { "A#6",   268UL }, { "B6",    253UL },
  { "C7",    239UL }, { "C#7",   225UL }, { "D7",    213UL }, { "D#7",   201UL },
  { "E7",    189UL }, { "F7",    179UL }, { "F#7",   169UL }, { "G7",    159UL },
  { "G#7",   150UL }, { "A7",    142UL }, { "A#7",   134UL }, { "B7",    126UL },
  { "C8",    119UL },
};

const int NOTE_COUNT = sizeof(notes) / sizeof(notes[0]);

unsigned long getNoteInterval(const char* name) {
  for (int i = 0; i < NOTE_COUNT; i++) {
    if (strcmp(notes[i].name, name) == 0) return notes[i].interval;
  }
  return 0;
}

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
    unsigned long ms = atol(value);
    if (ms < 1) ms = 1;
    if      (strcmp(motor, "M1") == 0) { microstep1 = ms; M1Step = M1Interval / ms; }
    else if (strcmp(motor, "M2") == 0) { microstep2 = ms; M2Step = M2Interval / ms; }
    else if (strcmp(motor, "M3") == 0) { microstep3 = ms; M3Step = M3Interval / ms; }
    else if (strcmp(motor, "M4") == 0) { microstep4 = ms; M4Step = M4Interval / ms; }
    else if (strcmp(motor, "M5") == 0) { microstep5 = ms; M5Step = M5Interval / ms; }
    else if (strcmp(motor, "M6") == 0) { microstep6 = ms; M6Step = M6Interval / ms; }
    return;
  }

  if (strcmp(value, "OFF") == 0) {
    if      (strcmp(motor, "M1") == 0) { digitalWrite(enPin1, HIGH); M1Interval = 0; M1Step = 0; }
    else if (strcmp(motor, "M2") == 0) { digitalWrite(enPin2, HIGH); M2Interval = 0; M2Step = 0; }
    else if (strcmp(motor, "M3") == 0) { digitalWrite(enPin3, HIGH); M3Interval = 0; M3Step = 0; }
    else if (strcmp(motor, "M4") == 0) { digitalWrite(enPin4, HIGH); M4Interval = 0; M4Step = 0; }
    else if (strcmp(motor, "M5") == 0) { digitalWrite(enPin5, HIGH); M5Interval = 0; M5Step = 0; }
    else if (strcmp(motor, "M6") == 0) { digitalWrite(enPin6, HIGH); M6Interval = 0; M6Step = 0; }
    return;
  }

  unsigned long interval = getNoteInterval(value);
  if (interval == 0) return;

  if      (strcmp(motor, "M1") == 0) { M1Interval = interval; M1Step = interval / microstep1; digitalWrite(enPin1, LOW); }
  else if (strcmp(motor, "M2") == 0) { M2Interval = interval; M2Step = interval / microstep2; digitalWrite(enPin2, LOW); }
  else if (strcmp(motor, "M3") == 0) { M3Interval = interval; M3Step = interval / microstep3; digitalWrite(enPin3, LOW); }
  else if (strcmp(motor, "M4") == 0) { M4Interval = interval; M4Step = interval / microstep4; digitalWrite(enPin4, LOW); }
  else if (strcmp(motor, "M5") == 0) { M5Interval = interval; M5Step = interval / microstep5; digitalWrite(enPin5, LOW); }
  else if (strcmp(motor, "M6") == 0) { M6Interval = interval; M6Step = interval / microstep6; digitalWrite(enPin6, LOW); }
}

void setup() {
  Serial.begin(115200);
  pinMode(stepPin1, OUTPUT); pinMode(stepPin2, OUTPUT); pinMode(stepPin3, OUTPUT);
  pinMode(stepPin4, OUTPUT); pinMode(stepPin5, OUTPUT); pinMode(stepPin6, OUTPUT);
  pinMode(enPin1, OUTPUT);   pinMode(enPin2, OUTPUT);   pinMode(enPin3, OUTPUT);
  pinMode(enPin4, OUTPUT);   pinMode(enPin5, OUTPUT);   pinMode(enPin6, OUTPUT);
  pinMode(fanPin, OUTPUT);
  digitalWrite(enPin1, HIGH); digitalWrite(enPin2, HIGH); digitalWrite(enPin3, HIGH);
  digitalWrite(enPin4, HIGH); digitalWrite(enPin5, HIGH); digitalWrite(enPin6, HIGH);
  analogWrite(fanPin, 100);
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

  unsigned long now = micros();

  if (M1Step > 0 && now - lastStep1 >= M1Step) {
    lastStep1 = now;
    digitalWrite(stepPin1, HIGH); delayMicroseconds(2); digitalWrite(stepPin1, LOW);
  }
  if (M2Step > 0 && now - lastStep2 >= M2Step) {
    lastStep2 = now;
    digitalWrite(stepPin2, HIGH); delayMicroseconds(2); digitalWrite(stepPin2, LOW);
  }
  if (M3Step > 0 && now - lastStep3 >= M3Step) {
    lastStep3 = now;
    digitalWrite(stepPin3, HIGH); delayMicroseconds(2); digitalWrite(stepPin3, LOW);
  }
  if (M4Step > 0 && now - lastStep4 >= M4Step) {
    lastStep4 = now;
    digitalWrite(stepPin4, HIGH); delayMicroseconds(2); digitalWrite(stepPin4, LOW);
  }
  if (M5Step > 0 && now - lastStep5 >= M5Step) {
    lastStep5 = now;
    digitalWrite(stepPin5, HIGH); delayMicroseconds(2); digitalWrite(stepPin5, LOW);
  }
  if (M6Step > 0 && now - lastStep6 >= M6Step) {
    lastStep6 = now;
    digitalWrite(stepPin6, HIGH); delayMicroseconds(2); digitalWrite(stepPin6, LOW);
  }
}
