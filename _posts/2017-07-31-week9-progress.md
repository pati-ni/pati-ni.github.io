---
layout: post
---

### Tuning MIDAs FFTID

Despite I finished developing MIDAs FFTID algorithm easily, making it work is a frustating task. 

#### More FFT problems
As I mentioned week 5 MIDAs was using an strange flavor of a fast fourier transform that was not aligned with the kissfft fft I was using. 
It seems that this is a culprit that right now keeps from getting nice results like MIDAs'. 
My implementation detects less peaks in the mass domain than MIDAs' and this issue requires further investigation.
Maybe I have to test with MIDAs's native implementation to get more insight on the issue.

#### Tuning the input
One other issue is that the algorithm generated a lot of noise in the isotope distribution that did not allow generating peaks by the cutoff threshold of the algorithm.
In MIDAs's paper this noise is called probability leakages.
This was resolved by carefully inspecting and tuning the input.
In fact the issue was that I was not rounding the average isotope mass before scaling it to the used resolution.
This average mass parameter was used heavily during the sampling of the isotope and it was the main source of noise in the distribution.
I was quite surprised to see that something that seems of such minor significance could impact the output that much.


