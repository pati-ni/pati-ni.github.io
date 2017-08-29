---
layout: post
---

### Pull Requests 

 - [#2864](https://github.com/OpenMS/OpenMS/pull/2864) Isotope Distribution Generator methods
 - [#2798](https://github.com/OpenMS/OpenMS/pull/2798) (Merged) Changes in the data model. Nomimal mass to double in Isotope Distribution Container.
 - [#2523](https://github.com/OpenMS/OpenMS/pull/2523) (Merged) Digestion Filter, community bonding period.


### Project log
A blog has been created in which I posted weekly updates of the projects. Can be found [here](http://pati-ni.github.io)


### Program Timeline
The goal of the project was to implement and integrate three High Resolution Isotope Distribution methods during the code period.

Overall each coding period was dedicated to make a Fine Grained Isotope Distrubution Generation method to work. During the end third period some days were allocated to integrate the methods at the OpenMS codebase and make use of the tool. 

#### 1st coding period
Before starting to implement the methods I had to do an important change at the Isotope Distribution container. OpenMS used nomimal masses for the coarse method and the new methods introduced non nomimal masses. So the Isotope Distribution container needed to hold `double` masses while maintaining backwards compatibility with the current coarse grained method. 

During the first period I implemented the MIDAs polynomial method. This method modelled the problem using polynomials by aggregating the probabilities of a set of elements. This looks like a dummy method of calculating probabilities and very inefficient, nevertheless the algorithm crops the problem size by searching probabilities at small mass ranges based on the variance of an element.

The method needed to utilize some special components generating sets of numbers that their sum satisfy an equality that was not trivial to implement. Nevertheless I did it and I am quite happy with the results.

#### 2nd coding period
The second period did not roll so smoothly as the first one. The second MIDAs method gave me a very hard time with its fft components. 
Aside that I had some headaches to make the kissfft methods to be integrated as well giving me some very weird runtime errors.

The kissfft error was due to the build parameters of the library. Kissfft is build on a specific datatype (int, float, double etc.). I ended up not defining the data type in the header file in OpenMS and this resulted in some nasty runtime errors.

The fft method that midas involved some black magic so we ended up giving up the kissfft method for this and using the MIDAs native method. That was a bummer because I had some ideas making for improving the method but all of this required kissfft working with the implementation.

#### 3rd coding period
During the third period I directly integrated C++ code in OpenMS and adapted to the data structures afterwards. This method was integrated quite easily compared to the others.

The rest of the period was alloted to the modification and the refactoring of the current data model to allow use of the new methods in the OpenMS codebase.

Finally some benchmarking reports were exported from the results.

### Results

#### Evaluation

A set of formulas were benchmarked, namely:
- C

- CHNO

- C10H10
- C100H100
- C1000H1000
- C10000H10000

- C10H10N10
- C100H100N100
- C1000H1000N1000

- C10H10N10O10
- C100H100N100O100
- C1000H1000N1000O1000

The resolution of exporting is in 0.01 Da.


#### Runtime
![alt text](/assets/img/runtime.png "Runtime")
From the this first graph can be seen a deviation on the way algorithms work.

 - Ecipex has a spike in its runtime performance during `C10000H10000` . It is obvious that this algorithm would not scale at large molecules that well.
 - MIDAs FFT has consistently larger runtimes. It is remarkable though that despite the algorithm's speed spees this algorithm demontrates good scalability.
 - MIDAs Polynomial takes the prize on speed. It is fast and it shows excellent scalability.
 

#### Distribution Quality
+![alt text](/assets/img/error.png "Mean error skew")

This error metric is a quick and dirty way to measure the quality of the distribution. Given the known average mass of the formula the error is calculated by the difference of the weighted mean of distribution and the average mass of the formula.

 - We see similar errors in all the algorithms but MIDAsFFT has consistently better accuracy.
 - During the calculation of the `C1000H1000N100O1000` we see a big spike at the error of Ecipex and MIDAs Polynomial. That reveal a good quality on MIDAsFFT despite its significant bigger runtime.

+![alt text](/assets/img/isotopes.png "Number of isotopes at 0.01 Da")

This graph plots the number of isotopes each method found for a set of formulas. The extra isotope explains the improved accuracy of MIDAs FFT above.

#### Distributions
##### 0.01 Daltons resolution

For the demonstration the formula `C1000H1000N100O1000` is tested.

+![alt text](/assets/img/ecipex.png "Ecipex at 0.01 Da")
+![alt text](/assets/img/midas_fft.png "MIDAs FFT at 0.01 Da")
+![alt text](/assets/img/midas_pol.png "MIDAs Polynomial at 0.01 Da")

At this resolution we see ecipex and MIDAs polynomial identifying two peaks. What is interesting though is that MIDAs Polynomial has one peak at the first half of its distribution and ecipex at the second. MIDAs FFT on the other has been able to identify more peaks throughout the distribution.

##### 0.1 Daltons resolution

![alt text](/assets/img/ecipex_10x.png "Ecipex at 0.1 Da")
![alt text](/assets/img/midas_fft_10x.png "MIDAs FFT at 0.1 Da")
![alt text](/assets/img/midas_pol_10x.png "MIDAs Polynomial at 0.1 Da")

We see that the MIDAs fft, still contain much richer peak information than the others.

The distributions of MIDAs polynomial and ecipex looks identical! Despite the first time different peaks where identified.

Let's plot the difference of the two distributions:

![alt text](/assets/img/midas_ecipex_diff.png "MIDAs polynomial - Ecipex difference")

Small differences but impressive! Can't help but blame my merging function.

### Conclusions

We see that there is a place for each algorithm. 
 - MIDAs FFT is slow, but is scalable and produce the best results. 
 - Ecipex and MIDAs are fast but may lack the extra accuracy. Ecipex has some scalability issues but the tweaking of its parameters may reduce the run time during large molecules.

Another issue is the manipulation of the raw results. As it is noticed above the distributions need smoothing without losing peaks.


### Future Work
To get the most of this effort more work has to be done. 

For starters, the methods need to be implemented at the actual tools to make use of the new methods. This will further increase the flexibility of the tools while improving their power in various scenarios. There is a large set of tools that can use these methods so a nice interface for these methods will come handy.

During the course of the project my mentors were contacted and informed about a new method called Isospec that is said to be significantly faster than those that currently exist so it would be nice if OpenMS could be used with this new seemingly superior method. This method utilizes a heuristic function and FFT to find isotopes. None of the methods do that so implementing this method should be a priority.

Finally, during the coding I realized that the distributions produced did non uniform samples, non-constant sample rates. Currently these are handled with two basic methods either by filling with zeros or bin aggregation. It will be a useful task to develop methods that produce uniform and smooth distributions that would not lose any mass peaks while not using excessive memory to be stored (e.g. many dense points). Even better if those methods could autoconfigure their sample rate parameters so no info is lost. These tools could be used by other generators or approximations as well.


### Closing thoughts
Participating in Google Summer of Code was an exciting opportunity to contribute to the open source community and I feel proud that I could pull it off.

I feel very strongly about the community of OpenMS, so I will continue to contribute code to the project. My mentors supported me exceptionally and I learned a lot from them. I feel I could do a little bit better on my end at some aspects. Nevertheless, given the circumstances, I think that I reached most of the goals and strongly believe that my work at the project will eventually be valuable to OpenMS and the open-source community.

**Timo, Artem, Julianus and Oliver big thanks for being awesome!**
