---
layout: post
---

Another week reaches its end. the first evaluation seems much closer now. During this week I implemented the rest of the core algorithm and started to test and debug my implementation.

There were two important functions that needed to be implemented to complete the algorithm. one was the polynomial multiplication of the isotope distributions generated from the previous week. the other function is at the end of the pipeline that convert a polynomial to the final isotope distribution. 

These two tasks were carried out smoothly, but the task of making the algorithm return sane results remained.

The algorithm used some a bit cryptic parameters that scaled the mass resolution of the isotopes and its products. This step needs to be undone at the final stage to extract the exact atomic masses. I had to carefully review the existing implementation to get an insight of what is going on. This combined with an unsigned integer overflow caused me some headaches with the debugging. However most of these issues were fixed and now I am very near to compare my implementation's results with the original's. I feel quite optimistic about my implementation's superiority and soon will come back with some fresh results.-

