# ShushEngine

The Python stepper motor driver for Trinamic's TMC5160 motion controller chip.

Built from [Roboteurs](https://roboteurs.com/)'s project [SlushEngine](https://github.com/Roboteurs/slushengine)

This project is made for the Raspberry Pi only at this time.

## Getting Started

### Installing the ShushEngine project

In your Raspberry Pi, open a new Terminal window.

Change to the directory/folder in which you'd like to save your project.  For this example, let's just use the desktop.

```
cd desktop
```

To get the ShushEngine project files onto your desktop, copy/paste or type the following.

```
git clone https://github.com/ZJAllen/ShushEngine.git
```

Now, change directory into the newly created `ShushEngine` folder.

```
cd ShushEngine
```

Next, since the project is made with a structure of folders, you'll need to run the setup to tell the system where all the relative references are.

```
sudo python3 setup.py install
```

You're all ready to start using the ShushEngine!

### Simple Motor Control

The stepper motor driver chips used are [Trinamic TMC5160](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC5160_Datasheet_V1.01.pdf), which use 256 microsteps per full step.  Most common stepper motors have 200 steps per revolution, or 1.8 degrees per step.  Therefore, if you want to go 1 full revolution, the TMC5160 would need a command to go 51,200 microsteps (256 * 200).  Keep this in mind for the following example.

The following example assumes the wiring according to Trinamic’s TMC5160-BOB Raspberry Pi [example](https://blog.trinamic.com/2018/02/19/stepper-motor-with-tmc5160/).

Below is a simple Python script to get the motor spinning. You can copy/paste this or type it into your favorite text editor and save it on your RPi.

``` python
import Shush
import time

s = Shush.Board()
m = Shush.Motor(0)

# This function takes the target position as an input.
# It prints the current position and the iteration.
# The motor spins until it gets to the target position before allowing the next command.
def spin(target):
  m.goTo(target)

  i = 0

  while m.getPosSigned() != target:
    # The getPos() function prints the current position
    print(m.getPosSigned())
    print(i)
    i += 1

while(True):
  spin(2560000) # Spin 5 rotations from start
  time.sleep(0.5)
  spin(0) # Spin back 5 rotations to starting point
  time.sleep(0.5)
```

Save this anywhere you’d like, on your Desktop for example. We’ll call it ShushExample.py. Change directory into that folder by `cd Desktop`.

Now, you can call the script by typing `python3 ShushExample.py` and the motor should start spinning.

## Motor Driver Functionality

This library is being populated with various functionality to drive stepper motors with ease.  Please check back to this list as the library is developed.

The following examples assume you have assigned the Motor object to the variable `m` as in `m = Shush.Motor(0)`, where the argument for Motor() is the identifier for the specific stepper motor.  If you have up to six stepper motors, for example, you can denote them by `m0 = Shush.Motor(0)`, `m1 = Shush.Motor(1)`, and so on.

### Go To Position

The `goTo()` method takes a target position as the input argument, and the driver internals drive the motor to that position according to the set ramp parameters.  The target position is number of microsteps.  The default is 256 microsteps per full stepper motor step.  Most common stepper motors move 1.8 degrees per step, or 200 steps per full revolution.  So to go a full rotation, the target would be +/- 51200 microsteps.

The position can be a signed (positive or negative) number.  The most negative number is (-2^31), or -2,147,483,648, and the most positive number is (2^31 - 1), or 2,147,483,647.

To put this range in perspective, if you set the position to the max or min value, it would spin +/- 41,943 **full rotations** from the 0 position.

#### Syntax:

``` python
m.goTo(position)
```

#### Return:

None

### Get Position

The `getPosSigned()` method retrieves the current position of the motor.  This method doesn't take any input arguments.

As described in the `goTo()` method, the position can be -(2^31) to +(2^31 - 1), so the position read from the register is also signed.

This method can be called during a motion to poll the position in real time.

#### Syntax:

``` python
currentPos = m.getPosSigned()
```

#### Return:

Signed position of motor `[int]`


# License

The ShushEngine drives up to 6 stepper motors using Trinamic TMC5160 drivers.
Copyright (C) 2019 Zach Allen

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.
