**rpitx** is a radio transmitter for Raspberry Pi (B,B+ and PI2) which output directly to GPIO. 
*(Created by Evariste Courjaud F5OEO. Code is GPL)*
*
<h1> Installation </h1>
git clone the repository
cd rpitx
make sure to have access to Internet to download packages : libsndfile1-dev and imagemagick otherwise install them manualy
./install.sh

<h1>Hardware</h1>
Plug a wire on GPIO 18, means Pin 12 of the GPIO header ([header P1](http://elinux.org/RPi_Low-level_peripherals#General_Purpose_Input.2FOutput_.28GPIO.29)): this act as the antenna. Length depend on transmit frequency, but with few centimeters it works for local testing.

<h1>Short manual</h1>

<h2> General </h2>
**rpitx** is the main software to transmit. It allows to transmit 
	- from **IQ** files *.iq (could be generated by external software like gnuradio or by some few examples of modulations).
	- from **Frequency/Time** files *.ft (generally used implement easily digital modes)  

Usage:rpitx [-i File Input][-m ModeInput] [-f frequency output] [-s Samplerate] [-l] [-p ppm] [-h] 
-m            {IQ(FileInput is a Stereo Wav contains I on left Channel, Q on right channel)}
              {IQFLOAT(FileInput is a Raw float interlaced I,Q)}
              {RF(FileInput is a (double)Frequency,Time in nanoseconds}
       	      {RFA(FileInput is a (double)Frequency,(int)Time in nanoseconds,(float)Amplitude}
	      {VFO (constant frequency)}
-i            path to File Input 
-f float      frequency to output on GPIO_18 pin 12 in khz : (130 kHz to 750 MHz),
-l            loop mode for file input
-p float      frequency correction in parts per million (ppm), positive or negative, for calibration, default 0.
-d int 	      DMABurstSize (default 1000) but for very short message, could be decrease
-h            help (this help).

<h2> Modulation samples </h2>
Some modulations are included in the software and can be easily extended. They create files which could be ingest by rpitx.
Some output in IQ (like ssb) other in FT(like sstv)
<h3> SSB modulation </h3>
**pissb** convert an audio file (Wav 48KHZ mono only!) to SSB (USB right now) and output it on a IQ file.
Assuming your audio file is in the folder
```
./pissb audio48mono.wav ssbIQ.wav
```
You could then transmit it on 50MHZ (please set a correct frequency to be legal)
```
sudo ./rpitx -m IQ -i ssbIQ.wav -f 50000 -l
```
A sample script : **testssb.sh** is included.
<h3> FM modulation </h3>
**pifm** convert an audio file (Wav 48KHZ mono only!) to Narrow band FM (12.5khz excursion) and output it on a .ft file.
Assuming your audio file is in the folder
```
./pifm audio48mono.wav fm.ft
```
You could then transmit it on 100MHZ (please set a correct frequency to be legal)
```
sudo ./rpitx -m RF -i fm.ft -f 100000 -l
```
A sample script : **testfm.sh** is included.
<h3> SSTV </h3>
**pisstv** convert a picture(rgb) to a SSTV .ft file
if you have a jpeg picture 320*256 you could transform it to rgb with imagemagick
convert -depth 8 picture.jpg picture.rgb
Then convert to .ft file :
```
./pisstv picture.rgb picture.ft
```
And then transmit it to 100MHZ (please set a correct frequency to be legal)
```
sudo ./rpitx -m RF -i picture.ft -f 100000
```
A sample script : **snapsstv.sh** allows to grab a picture from the Picamera and transmit it
<h3> FSQ </h3>
**pifsq** allows to send a text with the new FSQ modulation
It is still in test.
A sample script : **testfsq.sh** allows to send a text with FSQ
<h3> VFO </h3>
A **VFO** mode is provided to allows precise frequency resolution.
Example for generate a carrier on 100MHZ(please set a correct frequency to be legal)
```
sudo ./rpitx -m VFO -f 100000
```
<h1> Notes </h1>
All rights of the original authors reserved.
Special thanks to Sylvain Azarian F4GKR for improving SSB modulation
Inspired by 
* http://pe1nnz.nl.eu.org/2013/05/direct-ssb-generation-on-pll.html
* http://www.icrobotics.co.uk/wiki/index.php/Turning_the_Raspberry_Pi_Into_an_FM_Transmitter
* https://github.com/richardghirst/PiBits/pull/18
* http://www.bellard.org/dvbt/



