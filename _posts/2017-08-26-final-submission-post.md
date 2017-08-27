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
Before starting to implement the methods I had to do an important change at the Isotope Distribution container. OpenMS used nomimal masses for the coarse method 

During the first period I implemented the MIDAs polynomial method. This method modelled the problem using polynomials by aggregating the probabilities of a set of elements. This looks like a dummy method of calculating probabilities and very inefficient, nevertheless the algorithm crops the problem size by searching probabilities at small mass ranges based on the variance of an element.

The method needed to utilize some special components generating sets of numbers that their sum satisfy an equality that was not trivial to implement. Nevertheless I did and I am quite happy with the results.

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


### Conclusion


### Future Work
To get the most of this effort more work has to be done. 

For starters, the methods need to be implemented at the actual tools to make use of the new methods. This will further increase the flexibility of the tools while improving their power in various scenarios. There is a large set of tools that can use these methods so a nice interface for these methods will come handy.

During the course of the project my mentors were contacted and informed about a new method called Isospec that is said to be significantly faster than those that currently exist so it would be nice if OpenMS could be used with this new seemingly superior method. This method utilizes a heuristic function and FFT to find isotopes. None of the methods do that so implementing this method should be a priority.

Finally, during the coding I realized that the distributions produced did non uniform samples, non-constant sample rates. Currently these are handled with two basic methods either by filling with zeros or bin aggregation. It will be a useful task to develop methods that produce uniform and smooth distributions that would not lose any mass peaks at the distribution while being relatively smooth and do not use excessive memory to be stored (e.g. many dense points). Even better if those methods could autoconfigure their sample rate parameters so no info is lost. These tools could be used by other generators or approximations as well.


### Closing thoughts
Participating in Google Summer of Code was an exciting opportunity to contribute to the open source community and I feel proud that I could pull it off.

I feel very strongly about the community of OpenMS, so I will continue to contribute code to the project. My mentors supported me exceptionally and I learned a lot from them. I feel I could do a little bit better on my end at some aspects. Nevertheless, given the circumstances, I believe that I reached most of the goals and strongly believe that my work at the project will eventually be valuable to OpenMS and the opensource community.

Timo, Artem, Julianus and Oliver big thanks for being awesome!
