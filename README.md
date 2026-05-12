# Overview
This is the repo for my stepper motor project _steppy_
# Backend
The backend is responsible for executing the majority of the calculation, it is responsible for decompresing the midi file into a format readable to the driver and sends commands to the arduino nanos in real time. Both arduino nanos must be wired to the computer, you can adjust the port number based on your own needs (the serial M0_ser is responsible for substepping - noted as A2 on on the PCB). The Backend will also automatically duplicate the highest not on motor 6 (since higher frequencies are usually softer) and set the thresholds for substepping - all of this can be customized.

The backen needs to be executed in the same directory as a folder named "Songs" which you will store all your .midi files

# Speed Controler 
The speed controler should be uploaded to A1 on the PCB, the speed control takes commands in from the serial port in the form of M{Motor #}:{Note Letter}{Note #}, and sets the corresponding frequency to the correct motor, It will step each stepper motor as soon as the duration of their note frequency has passed.

# Step Controller
The step controlelr should be uploaded to A2 on the PCB. Lower frequencies on the stepper motors tend to be much louder than higher ones. To counteract this, lower frequencies are assigned a finer and finer substepping threshold making the sound smoother and overall quieter than the supposed melody.

# Schematic
This project uses 12V NEMA 17 Stepper motors, 2 Arduino Nanos and 6 DRV8825 Motor drivers with local 100µf capicitors backed up by 4 larger 330µf Capitors at the power line, The Schematic is available to [download](https://github.com/David-Xie-0/Steppy/blob/main/Steppy%20-%20PCB.kicad_pro) in the form of Kicad Files.

<img width="2078" height="1500" alt="image" src="https://github.com/user-attachments/assets/db51a3de-dcf9-423f-ba52-93007a456bdb" />

# PCB
This custom designed PCB is a 4 layer PCB manufactured by JLCPCB, the PCB files are available to [download](https://github.com/David-Xie-0/Steppy/blob/main/Steppy%20-%20PCB.kicad_sch) in the form of Kicad files.

| | |
|---|---|
| <img width="350" height="650" alt="image" src="https://github.com/user-attachments/assets/75027605-4e82-44ac-99fe-882f73ad44d9" /> | <img width="350" height="650" alt="IMG_2329" src="https://github.com/user-attachments/assets/7a90fd75-043b-4498-aaac-df5c1a4d8439" /> |

Expect more information to be added soon.




