#include <Wire.h>
#include "SparkFun_VL53L1X.h"

// Pin-Zuweisungen
#define MAGNET_SENSOR_PIN 7
#define RELAY_DIST_PIN 8
#define RELAY_MAGNET_PIN 9

SFEVL53L1X distanceSensor;

int baselineDistance = 0;     // Zur Kalibrierung
bool calibrated = false;

void setup(void)
{
  Wire.begin();

  Serial.begin(115200);
  Serial.println("VL53L1X Qwiic Test");

  if (distanceSensor.begin() != 0) //Begin returns 0 on a good init
  {
    Serial.println("Sensor failed to begin. Please check wiring. Freezing...");
    while (1)
      ;
  }
  Serial.println("Sensor online!");
  delay(200);

  pinMode(MAGNET_SENSOR_PIN, INPUT_PULLUP);  // Magnetsensor mit internem Pull-up
  pinMode(RELAY_DIST_PIN, OUTPUT);
  pinMode(RELAY_MAGNET_PIN, OUTPUT);

  digitalWrite(RELAY_DIST_PIN, false);   // Relais AUS initial
  digitalWrite(RELAY_MAGNET_PIN, false);


  delay(1000); // Sensorstabilisierung

  // === Kalibrierung ===
}

void loop(void)
{

  distanceSensor.startRanging(); //Write configuration bytes to initiate measurement
  while (!distanceSensor.checkForDataReady())
  {
    delay(1);
  }
  int distance = distanceSensor.getDistance(); //Get the result of the measurement from the sensor
  distanceSensor.clearInterrupt();
  distanceSensor.stopRanging();

  Serial.print("Distance(mm): ");
  Serial.print(distance);

}
