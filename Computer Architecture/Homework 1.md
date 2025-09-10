Thomas Canro

1. 
	a) Speedup obtained: approximately 5.5:
	![[IMG_0795.jpg]]
	b) As calculated above approximately, 90.9%
2. 
	a) Following Ahmdal's law:
		`s(enhanced) = 2`
		`f(enhanced) = 0.2`
		$S(overall) =\frac{1}{(1-0.2)+\frac{0.2}{2}}$
		`S(overall) = 1.11`
	b) This affects the original execution time:
		New time calculations: `T(old) = (1 - f - 0.1) = 0.7
		`T(cache_new) = 0.1(1.5) = 0.15`
		`T(fpu) = 0.2/2 = 0.1`
		Speedup become:
		`T(new) = T(fpu) + T(cache_new) + T(old)`
		`T(new) = 0.95`
		Speedup is 1/0.95, which is 1.05
	c) Accounting for the slowdown, FPU operations account for 10.5% of the new time. Cache access accounts for 15.8% of the new time.  
	
3. 
   a) We can map parlallelizable percentage as the fraction(enhanced), and the cores as the s(enhanced)
		$S(overall) = \frac{1}{0.5 + \frac{0.5}{22}}$
		$S(overall) = 1.91$
	b) Use same mapping, but with process D
		$S(overall) = \frac{1}{0.1 + \frac{0.9}{22}}$
		$S(overall) = 7.10$
	c) new enhanced speedup is 22 X 41% = 9
		$S(9 cores) = \frac{1}{0.5 + \frac{0.5}{9}} = 1.8$
		Use result in final calculation:
		$S(overall) = \frac{1}{(1 - 0.41) + \frac{0.41}{1.8}} = 1.222$
	d) 
		Already Calculated for application A: 1.8
		For Application B:
		$S(B enhanced) = \frac{1}{0.2 + \frac{0.8}{6}} = 3$
		For Application C:
		$S(C enhanced) = \frac{1}{0.4 + \frac{0.6}{4}} = 1.82$
		For Application D
		$S(D enhanced) = \frac{1}{0.1 + \frac{0.9}{3}} = 2.5$
		Total speedup:
		$S = \frac{1}{(0.41/1.8) + (0.27/3) + (0.18/1.82) + (0.14/2.5)} = 2.12$
	e) Get execution time (base on resources needed by enhancement):
		Process A:
			$0.41*\frac{1}{1.8} = 0.228$
		Process B:
			$0.27 * \frac{1}{3} = 0.09$
		Process C:
			$0.18 * \frac{1}{1.82} = 0.099$
		Process D:
			$0.14 * \frac{1}{2.5} =  0.056$	
		Now add all together and calculate how much they each contribute
		$A + B + C + D = 0.473$
		**A consumes 48.2%, B consumes 19%, C consumes 20.9% and C consumes 11.8%**