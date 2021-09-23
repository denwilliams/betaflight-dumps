# RPM Filtering

Ref1: https://www.youtube.com/watch?v=6a69juWsqyU&t=877s&ab_channel=JoshuaBardwell
Ref2: https://oscarliang.com/setup-rpm-filter-for-blheli_s-esc-for-free/

## Pre-requisites

Flash BLHeli_M to ESCs using https://github.com/Asizon/blheli-configurator/releases

ESC will probably have BLHeli_S 16.7, update to BLHeli_M 16.9.

1. Read
2. Flash All -> 16.9
3. Set PWM to 48khz or 96khz
4. Save

## Update Betaflight (4.2.9)

Use Betaflight configurator https://github.com/betaflight/betaflight-configurator/releases

Use `diff all` in the CLI, save to a text file. Clean it up a bit.

Go to "Upgrade Firmware".

Make sure the correct board is selected (see diff from before).

Pick 4.2.9 (or newer if available)

"Load Firmware (Online)"

"Flash Firmware"

### Reconfigure BF

Go to Betaflight CLI

Copy the diff from before. Paste in, enter.

Everything should be good now.

## Set Up Bidirectional DShot

### Which Frequency

> F4 flight controllers will most likely need DSHOT300 if using bidirectional

Start with `DSHOT300`, Gyro update `4kHz`, PID loop `4hHz`.

Go to CLI and run `tasks`. Make sure Gyro/PID rate/hz is close to `4000` and maxload is less than `100%` (not maxing out).

Switch to `DSHOT600`, Gyro update `8kHz`, PID loop `8kHz`.

Go to CLI and run `tasks`. Make sure Gyro/PID rate/hz is close to `8000` and maxload is less than `100%` (not maxing out). If it is 50% plus stick with `DSHOT300`.

Count the number of magnets on each motor. Mobula 0802 motors are 12.

Enter this number into `Motor poles`, enable `Bidirectional DShot`, "Save and Reboot".

### Check for Errors

Plug in batteries.

Go to `Motors` tab.

Errors should be at 0 (`E: 0.0%`). If on DSHOT600 you can try to go to DSHOT300.

Select `I understand the risks` then slowly bring the motors up and check for errors. We should be able to see the RPM listed here.

## Turn up RPM Filters

Go to `PID Tuning` -> `Filter Settings` -> `Gyro RPM Filter` and enable the checkbox (might already be on).

Set "Gyro RPM Filter Harmonics Number" to `1` to only filter primary (reduce latency).

## Reconfigure Other Filters

Notch filters can target non-motor vibrations now.

"Dynamic Notch Width Percent" to `0` (probably was 8) to make just 1 notch for 1 freq.

"Dynamic Notch Filter Range" to `LOW` (probably was MEDIUM) because we now want it to focus on frequencies below about 330Hz. _This was not in 4.2.9, maybe should be setting the max instead to something like 200Hz   ??_

"Dynamic Notch Q" to `200` (probably was 120) doesn't need to be so high now, will reduce latency.

"Dynamic Notch Min Hz" to `90` (prob was 150) to allow it to focus on lower frequencies.

## (Optional) Reduce Filtering

Run quad, check temps, if OK slide "Gyro Filter Multiplier" and "DTerm Filter Multiplier" into the red a couple of notches. Try again a couple of times or until motor is hot (and back off if so).