1. In an oscilloscope, volts per div refers to how many volts are between each vertical grid division on the screen, controlling the vertical scale of the oscilloscope. It is indicated in the bottom left of the display. The time per div refers to how much time is in one horizontal division, it refers to the the time axis (horizontal scale), and is indicated in the top of the display
2. Explained in diagram
	![[Pasted image 20260201174644.png]]
3. PCB failure question
	1. On a programmable power supply you can enable the OTP (Over temperature protection). Since the faulty components get hot, one could supply voltage to the board without much risk with this setting. Carsten can set the correct voltage with a safe current limit, and briefly power the board, and it should be detectible which components get hot by touching them, or a thermal probe
	2. There are two main things Carsten should verify in his DAC. Firstly, he should use the oscilloscope to verify the DAC pins (clock signal, CS, data change on clock signal). Additionally, he can test by setting the DAC to a certain bit value and measure the output with the digital multimeter from the lab. Then, he can compare the expected value with the output. He can do this on a relevant range of values to ensure that the DAC is working as expected
4. In order to test the hypothesis, the first step would be to simulate the high motor load during maneuvers on a test stand. I would then use the oscilloscope to probe the VDD rail and regulator output, and set a trigger on the oscilloscope to a voltage dip that would cause this brownout. I would then simulate the throttle with increasing intensity and verify weather the dip in power supply happened or not.
5. Low pass filter question
	1. Theoretical cutoff:
		`fc = 1/(2*pi*R*C)`
		`fc = 1/(2*pi*1kΩ*100nF)`
		`fc = 1591.55 Hz`
	2. Bode plot results (python script attached separately):
	   ![[Pasted image 20260201175746.png]]
	3. Measured resistor and capacitor values:
		Capacitor: 98.1nF
		Resistor: 983.1Ω
		New Calculation (Same formula as part a): 1650.26 Hz
		- This result is a much closer approximation to the value from the bode plot. One reason for the slight discrepancy could be the precision of the measuring instruments, the integrity of the cables, and the slight noise that could cause interference.