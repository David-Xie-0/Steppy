# Overview
This is the repo for my stepper motor project _steppy_
# Backend
The backend is responsible for executing the majority of the calculation, it is responsible for decompresing the midi file into a format readable to the driver and sends commands to the arduino nanos in real time. Both arduino nanos must be wired to the computer, you can adjust the port number based on your own needs (the serial M0_ser is responsible for substepping - noted as A2 on on the PCB). The Backend will also automatically duplicate the highest not on motor 6 (since higher frequencies are usually softer) and set the thresholds for substepping - all of this can be customized.

The backen needs to be executed in the same directory as a folder named "Songs" which you will store all your .midi files

# Speed Controler 
The speed controler should be uploaded to A1 on the PCB, the speed control takes commands in from the serial port in the form of M{Motor #}:{Note Letter}{Note #}, and sets the corresponding frequency to the correct motor, It will step each stepper motor as soon as the duration of their note frequency has passed.

# Step Controller
The step controlelr should be uploaded to A2 on the PCB. Lower frequencies on the stepper motors tend to be much louder than higher ones. To counteract this, lower frequencies are assigned a finer and finer substepping threshold making the sound smoother and overall quieter than the supposed melody.
