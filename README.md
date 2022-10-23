# Supported Web APIs in Tesla Browser for communication with peripherals (IO)

The following tests are intended to find an interface to exchange data with Tesla MCU2.
The following use cases might benefit of such an API:
* Tesla as an Access Point for Smartphone
* Screen sharing from Smartphone to Tesla Browser while still using Tesla GSM connection
* Controlling additional peripheral while driving without public web server e.g. Garage door opener

So far only the Web Gamepad API was successfully tested for communication from peripheral to Tesla.
The feedback channel of this API might be used for communication from Tesla to peripheral.
But this was not yet tested.

To test these APIs the following setups were used:
- Debian bookworm with Chromium 106.0.5249.91
- Tesla Model 3 2021 Long Range MCU2 2022.28.2


## https://developer.mozilla.org/en-US/docs/Web/API/WebHID_API
test http://wpt.live/webhid/unload-iframe.https.window.html
In chromium and Tesla passed
test https://wpt.live/webhid/idlharness.https.window.html
In chromium and Tesla HIDInputReportEvent interface object length fails with
assert_equals: wrong value for HIDInputReportEvent.length expected 2 but got 0
test https://raw.githack.com/twischer/webhid-demos/master/blinkstick-strip/index.html
- In chromium and tesla fails with
DragonRise Inc.   Generic   USB  Joystick  NotAllowedError: Failed to open the device.


## https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API
test https://larsgk.github.io/webusb-tester
- in tesla no devices are shown as connected (open might fail)
test https://wpt.live/webusb/usbDevice_claimInterface-manual.https.html
In Chromium fails for all except USBtoRS232
promise_test: Unhandled rejection with value: object "SecurityError: Failed to execute 'claimInterface' on 'USBDevice': The requested interface implements a protected class."
In Tesla fails claimInterface() for all with
SecurityError: Access denied


## https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API
- Bluetooth Gamepad cannot be connected
- Bluetooth Jabra headset can be connected but jitsi still do not access any mic
test https://wpt.live/tools/runner/index.html /bluetooth
- no manual test available
- 203 failing
test https://wpt.live/bluetooth/idl/idl-Bluetooth.https.window.html
- In Chromium and Tesla failing with
- assert_throws_js: the constructor should not be callable with "new" function "() => new Bluetooth()" threw object "ReferenceError: Bluetooth is not defined" ("ReferenceError") expected instance of function "function TypeError() { [native code] }" ("TypeError")
test https://wpt.live/bluetooth/idl/idlharness.tentative.https.window.html
- In chromium and Tesla 187 failing
test https://wpt.live/bluetooth/idl/idl-NavigatorBluetooth.https.window.html
- In chromium and Tesla
- navigator.bluetooth IDL test
- assert_true: navigator.bluetooth exists. expected true got false
Web Bluetooth API is not enabled by default in chrome. Has to be enabled with the following line
chrome://flags/#enable-web-bluetooth
test https://googlechrome.github.io/samples/web-bluetooth/device-info.html
- In Chromium and Tesla fails with
  Web Bluetooth API is not available. Please make sure the "Experimental Web Platform features" flag is enabled.
test https://webbluetoothcg.github.io/demos/


## https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API
test https://wpt.live/file-system-access/showSaveFilePicker-manual.https.html
- In chromium passing
- In Tesla failing with
  AbortError: The user aborted a request


## https://developer.mozilla.org/en-US/docs/Web/API/Gamepad_API
test https://www.soft8soft.com/gamepad_diagnostics/gamepad_diagnostics.html
- In Chromium and Tesla successfully reading all axes and buttons from gamepad
TODO test GamepadHapticActuator API
test https://wpt.live/gamepad/
- In chromium and tesla fails with "done() was called without first defining any tests" but keypress is detected
Downlink only via vibration modes => quite limited
See https://developer.mozilla.org/en-US/docs/Web/API/GamepadHapticActuator
websockify --cert /home/timo/Entwicklung/Linux/multimedia/noVNC/self.pem 6080 192.168.178.22:5900 --web /home/timo/Entwicklung/Java-Script/tesla_access_point/
Throughput
- 2.5sec for 1000000 times reading all axes => max speed 1000000*4/2.5sec = 1.6MByte/sec
- uplink every 100ms 4+2bytes => 60Byte/sec
- downlink 35 byte
Setup Data
    bmRequestType: 0x21
    bRequest: SET_REPORT (0x09)
    wValue: 0x0201
    wIndex: 0
    wLength: 35
    Data Fragment: 01ff00ff000000000002ff27100032ff27100032ff27100032ff271000320000000000
(((((((usb.data_fragment)) && 
!(usb.data_fragment == 01:ff:01:ff:00:00:00:00:00:02:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:00:00:00:00:00))) && 
!(usb.data_fragment == 01:ff:00:ff:00:00:00:00:00:02:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:00:00:00:00:00))) && 
!(usb.data_fragment == 01:ff:01:ff:7f:00:00:00:00:02:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:00:00:00:00:00)) && 
!(usb.data_fragment == 01:ff:00:ff:7f:00:00:00:00:02:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:ff:27:10:00:32:00:00:00:00:00)
- downlink 2bits 20packete/6,5ms 3077packete/sec * 35byte => 108kByte/sec
- Only https (not possible to connected to non https vpn server


## https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API
test https://tasmota.github.io/install/
- In tesla no device to select
https://wpt.live/serial/serialPort_disconnect-manual.https.html


## https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API
test https://raw.githack.com/AppGeo/web-audio-examples/master/microphone.html
- In chromium and Tesla no spectrum is seen but alos no error message


## https://developer.mozilla.org/en-US/docs/Web/API/MediaStream_Image_Capture_API
test https://wpt.live/mediacapture-streams/MediaDevices-getUserMedia.https.html
In Chromium only
- groupId is correctly supported by getUserMedia() for audio devices
- assert_equals: expected "OverconstrainedError" but got "NotReadableError"
In Tesla 5/6 test failing
- groupId is correctly support by getUserMedia() for video/audio devices
- assert_equals: expected "OverconstrainedError" but got "NotReadableError"
See getUserMedia.jpeg for details


## https://developer.mozilla.org/en-US/docs/Web/API/Web_MIDI_API
test https://wpt.live/webmidi/idlharness.https.window.html
- In Chromium 2 test are failing
- In Tesla 12 tests are failing (See wpt_midi.jpeg)
- USB might be able to poll every 1ms
- AVR ATmega32 midi transfers up to 8*4 bytes
- down and up link of >=32kBytes/sec
test https://editor.p5js.org/cdaein/sketches/01tCK67N_
- In Chromium successful even with `input.value.open()`
- In Tesla navigator.requestMIDIAccess() fails with
InvalidStateError: Platform dependent initialization failed
See midi.jpeg
TODO connect MIDI device to USB port and try again


## https://developer.mozilla.org/en-US/docs/Web/API/Storage_Access_API
test https://wpt.live/storage-access-api/storageAccess.testdriver.sub.html
- Failed in Chromium
https://wpt.live/tools/runner/index.html /storage
- 65 test passing


## https://developer.mozilla.org/en-US/docs/Web/API/Sensor_APIs
- only for Android known internal sensor
- not expecting any USB device

## https://developer.mozilla.org/en-US/docs/Web/API/Streams_API
- accessing network streams
- therefore not relevant

