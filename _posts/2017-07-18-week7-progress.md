---
layout: post
---

#### Finish prototyping MIDAs-FFT
The prototyping of the algorithm is finished. I am quite happy with the result because it has little room for improvement aside some refactoring. Admittedly I am a little behind schedule because I now I am supposed to have a running implementation and reporting the first results. That is not happening though.

#### Linking KISS-FFT in OpenMS
While I thought my work was done when I build kissfft in the contrib directory, linking the static library is another story. I've never had to add new components in projects of that scale and that meant that I had no solid experience with CMake. Hopefully though I was willing to get my hands dirty and with the of my mentors I've managed to overcome the problems and `make` the OpenMS see the kissfft while linking. As soon as I run the algorithm I got some strange undefined behavior that I was not able to debug. That means that the library that I link has some problems.
