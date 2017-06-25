---
layout: post
---

We have reached the evaluation week. For the most part I am on schedule, meaning I am done with the implementation of the first algorithm. However, some issues bugged me this week that I have not found a solution yet. A quick note reporting this week's progress while I am waiting on the OpenMS library to build.

After applying the steps of the algorithm I ended up with a bunch of tuples representing (mass,abundance) points. These points need to be merged into bins that:
 - avoid losing probability peaks at the mass domain. (setting a large bin size)
 - do not have very dense points in the mass domain with very low probability. (merge them so we can find a peak)
 - produce a uniform contiguous probability distribution on the mass domain without inserting many inbetween points that do not contain any actual information.
 - can have adjustable accuracy.

I was a bit confused which of these was actually important. In fact I still am. I ended up implementing a merge function that aggregated into bins the probabilities. The size of a bin is defined by the user in Daltons. 

It may be needed a gaussian filter to be applied at the final output to be more smooth. The parameters of the filter would be dependended on the resolution of the results. But this filter does not work with non-uniform samples. I expect it to work with my uniform constant-sized bins but I do not know how useful it would be.





