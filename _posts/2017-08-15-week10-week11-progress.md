---
layout: post
---

### MIDAs FFTID

The mystery of fft transform method is somewhat solved although I have not successfully hacked the kiss fft algorithm to work in the same manner.

I had to dig a little deeper to see that midas fft modified the samples during the butterfly stage where the samples are are going through a bit-reversal permutation.

For 8 sample a normal fftid bit-reversal permutation goes like this:
`0 4 2 6 1 5 3 7`

For 8 sample a midas fftid bit-reversal permutation goes like this:
`1 5 3 7 0 4 2 6`


Kissfft uses a mixed radix bit-reversal permutation so transforming the algorithm to work like this is not a trivial task and might produce several bugs.


After discussing the issue with my mentors we agreed to include the native MIDAs method 



### Finishing Ecipex Integration


Ecipex is now done and works fine. Props to my mentor for pointing me to his team code where the method was nicely implemented.

### Refactoring the code

#### Data structure changes
After discussion with my mentors we thought a good idea was to refactor the data structure of `IsotopeDistribution` from `std::vector< std::pair<double, double> >` to `std::vector< OpenMS::Peak1D >`.

This way the code is more easily readable and maintainable.


#### Class data model

OpenMS used its native low resolution method integrated in the Isotopedistribution container. That served well until now but now with the variety of methods that are going to be integrated we have to separate containers from algorithms.

I went through the codebase of OpenMS and made this change. ( I have an idea for further improvements but I will present them in another blog post). So now the `IsotopeDistribution` is essentially a container. The native method is now refactored to a new class named `CoarseID`. 

What remains though is if the operators of the native method that CoarseID currently uses are required and how will be implemented in that scenario.

OpenMS uses addition and multiplication. Addition operator appends other distribution to convolute to the `IsotopeDistribution` while multiplication scales the convolution by an integer number.



