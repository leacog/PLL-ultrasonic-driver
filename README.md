# PLL-ultrasonic-driver
KiCad project files and arduino code for an in-expensive and simple phase-locked-loop driver for piezo-electric ultrasonic transducers. 

### Principle of operation
The driver exploits the fact that, at resonance, current and voltage is in phase for a piezoelectric transducer. By locking the driving signal to the current feedback signal, i.e. adjust the driving frequency until it is in-phase with the current, resonance of the transducer can be found. 

The driver is based around the CD4046 Phase Locked Loop (PLL) IC(Texas instruments has a good application report: http://www.ti.com/lit/an/scha002a/scha002a.pdf) and an arduino nano. The driver features two modes of operation:

![Finished board next to 25kHz transducer](https://github.com/leacog/PLL-ultrasonic-driver/blob/master/pcb/images/finished-board.JPG)

### Automatic resonance tracing (PLL mode) ###
A current sensing resistor provides a reference signal which is compared to the driving signal at the phase demodulator of the CD4046. Before being fed into the phase-demodulator however, the current signal is put through a comparator which turns the analog signal into a digital signal (positive cycle of the AC-waveform becomes high while the negative cycle becomes 0V). This retains phase information and gives better lock performace than the unaltered current-signal. The phase demodulator output is low-pass filtered and feed as the input to the voltage controlled oscillator(VCO), the output of which is the driving signal.

### Digital control (Arduino mode) ###
In digital mode, instead of feeding the output of the phase-demodulator into the VCO, a low-pass filtered PWM signal from the arduino is feed as an input. The dutycycle determines the voltage at the VCO input and thus the driving frequency. Since the driver can be interfaced via a serial-connection to a PC, this can be programmed to do perform any arbitrary function. One use-case is to do impedance-analysis of the transducer.

This is possible because the arduino monitors the current. The current signal, around 100mV from the current sensing resistor, is amplified by a non-inverting op-amp and sent through a simple peak detection circuit. This voltage is read by the arduino, which can use it to emulate the PLL or send to the PC over serial. 

### Selecting frequency ###
There are two main parameters of concern with regards to tuning the driver for the right frequncy. The **center frequency** and the **frequency lock range**. The latter is the bandwidht of the driver i.e. the difference between the lowest and the highest frequency it can output. A large bandwidth provides a large operating range for the transducers, however there's a tradeoff between large bandwidth and the abillity of the driver to aquire lock and the size of the phase error at lock (the undesired difference in phase). The center frequency is  the frequncy halfwaybetween the maximum and the minumum operating frequncy. In the case of a standard 60W 25KHz transducer a suitiable center frequency is 25 KHz with a lock range of 1 KHz (i.e. the signal can vary between 24.5 to 25.5 KHz).

These parameters are tuned through a frequncy selector network. A switch selects the center frequency, either of 40 or 25 KHz (programmable by resistors), which can be fine-tuned with a variable resistor. The lock range is set by another variable resistor.

![](https://github.com/leacog/PLL-ultrasonic-driver/blob/master/pcb/images/PLL-USL-signalgenerator-schematic-1.png) 
