#Jarrel's Fork

"Fixed" some issues https://github.com/dzhu/myo-raw/issues/2 

Not really a fix, just duct taping over it so I can use it functionally.

Added some basic training examples - 
FIST = 5
PINKY = 4
SPREAD = 3
FLEX = 2
EXTEND = 1

Wear your myo with the logo facing front in the anatomical position, in the midline of your arm. USB facing up. Right arm for me. 
YMMV so train it yourself. 

Added media keys 
FIST + SPREAD = Play
FIST + FLEX = Back
FIST + EXTEND = Next
If you do FIST and nothing it will reset when you 'rest'

I think you need a media-enabled keyboard for it to work but I may be wrong. 


# Overview

This project provides an interface to communicate with the Thalmic Myo,
providing the ability to scan for and connect to a nearby Myo, and giving access
to data from the EMG sensors and the IMU.

The code is primarily developed on Linux and has been tested on Windows and
MacOS.

Thanks to Jeff Rowberg's example bglib implementations
(https://github.com/jrowberg/bglib/), which helped me get started with
understanding the protocol.


# Requirements

- python >=2.6
- pySerial
- pygame, for the example visualization and classifier program
- numpy, for the classifier program
- sklearn, for a more efficient classifier (and easy access to smarter classifiers)


# Dongle device name

To use these programs, you might need to know the name of the device
corresponding to the Myo dongle. The programs will attempt to detect it
automatically, but if that doesn't work, here's how to find it out manually:

- Linux: Run the command `ls /dev/ttyACM*`. One of the names it prints (there
  will probably only be one) is the device. Try them each if there are multiple,
  or unplug the dongle and see which one disappears if you run the command
  again. If you get a permissions error, running `sudo usermod -aG dialout
  $USER` will probably fix it.

- Windows: Open Device Manager (run `devmgmt.msc`) and look under "Ports (COM &
  LPT)". Find a device whose name includes "Bluegiga". The name you need is in
  parentheses at the end of the line (it will be "COM" followed by a number).

- Mac: Same as Linux, replacing `ttyACM` with `tty.usb`.


# Included files

## myo_raw.py (access to EMG/IMU data)

myo_raw.py contains the MyoRaw class, which implements the communication
protocol with a Myo. If run as a standalone script, it provides a graphical
display of EMG readings as they come in. A command-line argument is interpreted
as the device name for the dongle; no argument means to auto-detect. You can
also press 1, 2, or 3 on the keyboard to make the Myo perform a short, medium,
or long vibration.

## classify_myo.py (example pose classification and training program)

classify_myo.py contains a very basic pose classifier that uses the EMG
readings. You have to train it yourself; make up your own poses and assign
numbers (0-9) to them. As long as a number key is held down, the current EMG
readings will be recorded as belonging to the pose of that number. Any time a
new reading comes in, the program compares it against the stored values to
determine which pose it looks most like. The screen displays the number of
samples currently labeled as belonging to each pose, and a histogram displaying
the classifications of the last 25 inputs. The most common classification among
the last 25 is shown in green and should be taken as the program's best estimate
of the current pose.

This method works fine as long as the Myo isn't moved, but, in my experience, it
takes quite a large amount of training data to handle different positions
well. Of course, the classifier could be made much, much smarter, but I haven't
had the chance to tinker with it yet.

## myo.py (Myo library with built-in classifier and pose event handlers)

After you've done training with classify_myo.py, the Myo class in this file can
be used to notify a program each time a pose starts. If run as a standalone
script, it will simply print out the pose number each time a new pose is
detected.

Tips for classification:

- make sure to only press the number keys while the pose is being held, not
  while your hand is moving to or from the pose
- try moving your hand around a little in the pose while recording data to give
  the program a more flexible idea of what the pose is
- the rest pose needs to be trained as a pose in itself


# Caveats/issues

- on Windows, the readings become more and more delayed as time goes on
- doesn't have access to Thalmic's pose recognition
- may or may not work with a Myo that has never been plugged in and set up with
  Myo Connect
- classify_myo.py segfaults on exit under certain circumstances (probably
  related to Pygame version)
