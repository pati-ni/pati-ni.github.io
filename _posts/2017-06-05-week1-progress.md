---
layout: post
---

Time flied during the first week. I was excited to actually work in the isotope pattern algorithm. I sticked to my plan and started with Midas polynomial algorithm.

After planning my moves I noticed that I will need a custom component that is a core feature of this method. For the probability estimation in this polynomial method it is used an arbitrary set of nested counters. Each counter takes values on a predefined range. On top of that, the sum if those counters at any given iteration must have sum of a predefined numbers. Another requirement is that we have to iterate set of values exactly once. That is a crucial component of the algorithm and its proper function will ensure the accuracy of the method. Implementing a solution for this problem while trying to optimize as much as possible (skipping values that do not satisfy the sum restrictions) was not a trivial task and a lot of testing was required.

After finishing I knew that an important part of the implementation was done. The original midas implementation involved static nested counters and contained lots of duplicate code. By having static counters the algorithm is bounded to a maximum number of dimensionality. The implemented countet generator on the other hand contains less than a one third of the original midas code, works in arbitrary dimensions and I hope that it will perform simirarly or better.
