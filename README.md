// --- Definisi Pin ---
#define SOIL_MOISTURE_PIN 35   // ADC1 channel untuk soil moisture
#define LDR_PIN            34   // ADC1 channel untuk LDR
#define RELAY_PUMP         25   // Relay pompa air (active LOW)
#define RELAY_LAMP         14   // Relay lampu (active LOW)

// --- Threshold (kalibrasi via Serial Monitor) ---
const int soilMoistureThreshold = 3000;  
const int ldrThreshold          = 1800;  

void setup() {
  // Mulai serial untuk kalibrasi
  Serial.begin(115200);

  // Inisialisasi relay
  pinMode(RELAY_PUMP, OUTPUT);
  pinMode(RELAY_LAMP, OUTPUT);
  digitalWrite(RELAY_PUMP, HIGH);  // Pompa OFF
  digitalWrite(RELAY_LAMP, HIGH);  // Lampu OFF

  // Konfigurasi ADC
  analogReadResolution(12);                         // 12-bit → 0..4095
  analogSetPinAttenuation(LDR_PIN, ADC_11db);       // Rentang hingga ~2.6 V
  analogSetPinAttenuation(SOIL_MOISTURE_PIN, ADC_11db);
}

void loop() {
  // Baca nilai mentah
  int soilRaw = analogRead(SOIL_MOISTURE_PIN);
  int ldrRaw  = analogRead(LDR_PIN);

  // Tampilkan di Serial untuk monitoring
  Serial.print("Soil Moisture: "); Serial.print(soilRaw);
  Serial.print(" | LDR Value: ");    Serial.println(ldrRaw);

  // --- Kontrol Pompa ---
  // Tanah kering saat soilRaw < threshold → nyalakan pompa
  if (soilRaw > soilMoistureThreshold) {
    digitalWrite(RELAY_PUMP, LOW);   // Pompa ON
    delay(3000);                    // Siram 30 detik
    digitalWrite(RELAY_PUMP, HIGH);  // Pompa OFF
  } else {
    digitalWrite(RELAY_PUMP, HIGH);  // Pompa OFF
  }

  // --- Kontrol Lampu ---
  // Gelap saat ldrRaw > threshold → nyalakan lampu
  if (ldrRaw > ldrThreshold) {
    digitalWrite(RELAY_LAMP, LOW);   // Lampu ON
  } else {
    digitalWrite(RELAY_LAMP, HIGH);  // Lampu OFF
  }

  //delay(2000); // Jeda antar siklus
}
