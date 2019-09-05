# MicroPython ESP8266/ESP32 driver for TEA5767 FM radio module (BETA)

![FQTC302J70QTXEA MEDIUM](https://user-images.githubusercontent.com/44191076/64347645-d7d66f00-d026-11e9-87b5-4ae21e6115e9.jpg)

[TEA5767 modules](https://www.sparkfun.com/datasheets/Wireless/General/TEA5767.pdf) are one of the cheaper FM radio receiver modules, which allow you to build DIY FM radios real quick.

This is the driver for MicroPython on ESP8266/ESP32 boards. I've tested it on my NodeMCU V2 (ESP8266) and BPI:bit (ESP32).

## Wiring

* +5V -> power (both 3.3V and 5V works)
* SDA -> SDA
* SLC -> SCL
* GND -> GND

## Import on ESP8266

The simplest way is as below:

```python
import TEA5767

radio = TEA5767.Radio(freq=99.7)
```

The module would immediately tune on the frequency.

There are also some other options:

```python
radio = TEA5767.Radio(freq=99.7, scl=5, sda=4, debug=True, stereo=True, soft_mute=True, noise_cancel=True, high_cut=True)
```

* scl = SCL pin (default 5 of ESP8266 boards)
* sda = SDA pin (default 4 of ESP8266 boards)
* debug = output some info text via REPL/serial port whenever you read data from the module.
* stereo = stereo mode (default True; set as False = forced mono)
* soft_mute = soft mute (default True)
* noise_cancel = stereo noise cancelling (default True)
* high_cut = high cut control (default True)

if you turn on the debug option, you'll see something like

```
FM frequency: 99.7 MHz
In search mode: False
Station ready: True
Station has stereo: True
Signal ADC level: 10
```

## Import on ESP32

It's basic the same as ESP8266, however you'll have to specify the SCL and SDA pins as 22 and 21:

```python
radio = TEA5767.Radio(freq=99.7, scl=22, sda=21)
```
## Set/change frequency

Directly choosing another frequency:

```python
radio.set_frequency(freq=104.9)
```

Or change the frequency bit by bit:

```python
radio.change_freqency(change=0.1)
radio.change_freqency(change=-0.1)
```

## Search mode

You can set the TEA5756 to auto-select a possible working station near a specific frequency.

```python
radio.search(True)
radio.search(False)
radio.search(mode=True, dir=1, adc=5)
radio.search(mode=True, freq=90.0)
```

When the search mode is on (True), the actual frequency may change right after you selected any frequency.

* dir = search direction; 1 means go up (default), 0 means go down on frequency.
* adc = signal ADC resolution, default 5. The adc level can be set as 0 (no search), 5, 7 or 10.

And if you change frequency by using radio.change_freqency(), the search direction will be set to the same direction of frequency change.

## Mute/standby

```python
radio.mute(True)
radio.standby(True)
```

Mute is simply turn off the sound. If you want to save more power, use radio.standby().