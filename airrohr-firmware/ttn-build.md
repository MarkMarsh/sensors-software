# Instructions for building TTN version and notes on usage

## Building

When building (using the platformio vscode plugin).

If you're not in the US, you need to make sure you have edited the LMIC config file in **all** the libdeps directories and changed the comments to select the freqency range you need. 

For example if you just build the env:nodemcuv2_en subproject, you will need to edit 
sensors-software\airrohr-firmware\.pio\libdeps\nodemcuv2\MCCI LoRaWAN LMIC library_ID5774\project_config\lmic_project_config.h
and
sensors-software\airrohr-firmware\.pio\libdeps\nodemcuv2_en\MCCI LoRaWAN LMIC library_ID5774\project_config\lmic_project_config.h

I have only updated the en and de language files so building any other language will fail at the moment.

If a build fails with some odd looking error message, try it again.

## Using

When you configure the WiFi information you also need to turn off the OTA updates.

You need to create an application and OTAA device on TTN. Copy the DEV EUI, APP EUI and APP KEY from the device overview page - use the clipboard icon and the default msb / lsb setting. You will get two 16 digit and one 32 digit hex strings which can be pasted into the **TTN Device Config** section (near bottom of config page). You will need to check the **Send to TTN** checkbox as well.

The TTN application decoder for the basic encoding is 

```javascript
function Decoder(bytes, port) {
  // Decode an uplink message from a buffer
  // (array) of bytes to an object of fields.
  var decoded = {};

  if (port === 1) {
    decoded.temperature = (bytes[0] + (bytes[1] * 256)) / 100.0;
    decoded.humidity = (bytes[2] + (bytes[3] * 256)) / 100.0;
    decoded.pm25 = (bytes[4] + (bytes[5] * 256)) / 100.0;
    decoded.pm10 = (bytes[6] + (bytes[7] * 256)) / 100.0;
  }
  return decoded;
}
```

## Debugging

The new code is quite chatty over the serial monitor if you set the debug level to **max info** (on the device home page). If you want to track the LoRa / TTN messages you can also edit the file:

.pio\libdeps\nodemcuv2_en\MCCI LoRaWAN LMIC library_ID5774\src\lmic\config.h 

set LMIC_DEBUG_LEVEL to 2

and uncomment the line
#define LMIC_PRINTF_TO Serial

