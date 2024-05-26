#include <ArduinoBleOTA.h>
#include <BleOtaSecurityOnConnect.h>
#include <ArduinoBLE.h>
#include <BleOtaMultiservice.h>

#define DEVICE_NAME "BLE LED OTA"
#define LED_SERVICE_UUID "19B10000-E8F2-537E-4F6C-D104768A1214"
#define LED_BUILTIN 33

BLEService ledService(LED_SERVICE_UUID); // Bluetooth Low Energy LED Service
BLEByteCharacteristic switchCharacteristic("19B10001-E8F2-537E-4F6C-D104768A1214", BLERead | BLEWrite);

const int ledPin = LED_BUILTIN;


#ifdef ARDUINO_ARCH_ESP32
  #define HW_NAME "Example ESP32"
#elif ARDUINO_ARCH_SAMD
  #define HW_NAME "Example SAMD"
#else
  #define HW_NAME "Example HW"
#endif

#define HW_VER {1, 0, 0}
#define SW_NAME "Example SW"
#define SW_VER {1, 0, 0}

#ifdef USE_NIM_BLE_ARDUINO_LIB
BleOtaSecurityOnConnect security;
#endif

void setup() {
  Serial.begin(115200);
  while (!Serial);
pinMode(ledPin, OUTPUT);

  if (!initBle(DEVICE_NAME)) {
    Serial.println("starting Bluetooth Low Energy module failed!");
    while (1);
  }

  ledService.addCharacteristic(switchCharacteristic);
  BLE.addService(ledService);

  // set the initial value for the characeristic:
  switchCharacteristic.writeValue(0);



  ArduinoBleOTA.begin(DEVICE_NAME, InternalStorage, HW_NAME, HW_VER, SW_NAME, SW_VER);
 advertiseBle(DEVICE_NAME, LED_SERVICE_UUID);
#ifdef USE_NIM_BLE_ARDUINO_LIB
  ArduinoBleOTA.setSecurityCallbacks(security);
  security.begin();
#endif
}

void loop() {
  BLEDevice central = BLE.central();

  if (central) {
    Serial.print("Connected to central: ");
    Serial.println(central.address());

    while (central.connected()) {
      if (switchCharacteristic.written()) {
        if (switchCharacteristic.value()) {
          Serial.println("LED on");
          digitalWrite(ledPin, HIGH);
        } else {
          Serial.println(F("LED off"));
          digitalWrite(ledPin, LOW);
        }
      }
      ArduinoBleOTA.pull();
    }

    Serial.print(F("Disconnected from central: "));
    Serial.println(central.address());
  }
}
