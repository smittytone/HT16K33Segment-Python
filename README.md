# HT16K33Segment 2.0.0 #

A hardware driver for the [Adafruit 0.56-inch 4-digit, 7-segment LED display](http://www.adafruit.com/products/878), which is based on the Holtek HT16K33 controller. The LED communicates over any I&sup2;C bus.

Three versions of the driver are available, one for [CircuitPython](https://circuitpython.readthedocs.io/en/latest/docs/index.html) applications, another for code written in [MicroPython](http://docs.micropython.org/en/latest/index.html) and a third for regular Python, for use on the Raspberry Pi, for example.

## Characters ##

The class incorporates its own (limited) character set, accessed through the following codes:

- Digits 0 through 9: codes 0 through 9
- Characters A through F: codes 10 through 15
- Space character: code 16
- Minus character: code 17
- Degree character: code 18

## Display Digits ##

The display’s digits are numbered 0 to 3, from left to right.

## Class Usage ##

### Constructor: HT16K33Segment(*anI2cBus[, i2cAddress]*) ###

To instantiate a HT16K33Segment object pass the I&sup2;C bus to which the display is connected and, optionally, its I&sup2;C address. If no address is passed, the default value, `0x70` will be used. Pass an alternative address if you have changed the display’s address using the solder pads on rear of the LED’s circuit board.

The passed I&sup2;C bus must be configured before the HT16K33Segment object is created.

#### Examples ####

```python
# Micropython
from ht16k33segment_micropython import HT16K33Segment
from machine import I2C

DEVICE_I2C_SCL_PIN = 5
DEVICE_I2C_SDA_PIN = 4

i2c = I2C(scl=Pin(DEVICE_I2C_SCL_PIN), sda=Pin(DEVICE_I2C_SDA_PIN))
led = HT16K33Segment(i2c)
```

```python
# Circuitpython
from ht16k33segment_circuitpython import HT16K33Segment
import busio
import board

i2c = busio.I2C(board.SCL, board.SDA)
led = HT16K33Segment(i2c)
```

```python
# Python
from ht16k33segment_python import HT16K33Segment
import smbus

PI_I2C_BUS = 1

i2c = smbus.SMBus(PI_I2C_BUS)
led = HT16K33Segment(i2c)
```

## Class Methods ##

### set_brightness(*[brightness]*) ###

To set the LED’s brightness (its duty cycle), call *setBrightness()* and pass an integer value between 0 (dim) and 15 (maximum brightness). If you don’t pass a value, the method will default to maximum brightness.

#### Example ####

```python
# Turn down the display brightness
led.set_brightness(1)
```

### set_colon(*is_set*) ###

Call *set_colon()* to specify whether the display’s center colon symbol is illuminated (`true`) or not (`false`).

#### Example ####

```python
# Set the display to --:--
led.set_char("-", 0)
led.set_char("-", 1)
led.set_char("-", 2)
led.set_char("-", 3)
led.set_colon(true)
led.update()
```

### set_blink_rate(*rate*) ###

This method can be used to flash the display. The value passed into *rate* is the flash rate in Hertz. This value must be one of the following values, fixed by the HT16K33 controller: 0.5Hz, 1Hz or 2Hz. You can also pass in 0 to disable flashing, and this is the default value.

#### Example ####

```python
# Blink the display every second
led.set_blink_rate(1)
```

### set_glyph(*glyph[, digit][, has_dot]*) ###

To write a character that is not in the character set *(see [above](#characters))* to a single digit, call *set_glyph()* and pass a glyph-definition pattern and the digit number (0, 1, 2 or 3, left to right) as its parameters. You can also provide a third, optional parameter: a boolean value indicating whether the decimal point to the right of the specified digit should be illuminated. By default, the decimal point is not lit.

Calculate the glyph pattern value using the following chart. The segment number is the bit that must be set to illuminate it (or unset to keep it unlit):

```
    0
    _
5 |   | 1
  |   |
    - <----- 6
4 |   | 2
  | _ |
    3
```

For example, to define the letter 'P', we need to set segments 0, 1, 4, 5 and 6. In bit form that makes 0x73, and this is the value passed into *pattern*.

#### Example ####

```python
# Display 'SYNC' on the LED
letters = [0x6D, 0x6E, 0x37, 0x39]

for index in range(0, len(letters)):
    led.set_glyph(letters[index], index)
led.update()
```

### set_number(*number[, digit][, hasDot]*) ###

To write a number to a single digit, call *set_number()* and pass the digit number (0, 1, 2 or 4, left to right) and the number to be displayed (0 to 9, A to F) as its parameters. You can also provide a third, optional parameter: a boolean value indicating whether the decimal point to the right of the specified segment should be illuminated. By default, the decimal point is not lit.

#### Example ####

```python
# Display '42.42' on the LED
led.set_number(4, 0)
led.set_number(2, 1, True)
led.set_number(4, 2)
led.set_number(2, 3)
led.update()
```

### set_char(*character[, digit][, hasDot]*) ###

To write a number to a single digit, call *set_char()* and pass the digit number (0, 1, 2 or 3, left to right) and the number to be displayed (0 to 9, A to F) as its parameters. You can also provide a third, optional parameter: a boolean value indicating whether the decimal point to the right of the specified segment should be illuminated. By default, the decimal point is not lit.

#### Example ####

```python
# Display 'bEEF' on the LED
led.set_char("b", 0)
led.set_char("e", 1)
led.set_char("e", 2)
led.set_char("f", 3)
led.update()
```

### clear() ###

Call *clear()* to zero the class’ internal display buffer. If the optional *clearChar* parameter is not passed, no characters will be displayed. Pass a character code *(see [above](#characters))* to zero the display to a specific character.

*clear()* does not update the display, only the buffer. Call *update()* to refresh the LED.

#### Example ####

```python
# Clear the display
led.clear()
led.update()
```

### update() ###

Call *update()* after changing any or all of the internal display buffer contents in order to reflect those changes on the display itself.

## Release Notes ##

- 2.0.0 *Unreleased*
    - Correct library filenames.
    - Add smbus-based version.
    - Fix documentation issues.
- 1.0.1 *17 March 2020*
  - Rename internal constants.
  - Code improvements.
- 1.0.0 *4 march 2020*
    - Initial public release.

## License ##

The HTK16K33Segment libraries are licensed under the [MIT License](LICENSE).
