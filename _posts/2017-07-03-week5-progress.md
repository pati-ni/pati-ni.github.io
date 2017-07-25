---
layout: post
---

#### MIDAs Interface


I passed the first evaluation successfully and I am really happy about it. Now I have to move on and try to keep up with the timeline's pace. It is time to start working at another flavor of MIDAs that utilizes fast fourier transform algorithm to compute high resolution isotope distributions.

Before moving to the algorithm I have to do some refactorings to avoid writing duplicate code. Once the isotope distribution is computed from the algorithm we have a bunch of points that we have to manipulate to get a consistent uniform result. This defines an interface that handles high resolution algorithms in our case we will call it `MIDAs`. This interface is used from `MIDAsPolynomialID` and `MIDAsFFTID` respectively. So after refactoring I came up with this structure.

```c++

  class MIDAs : public IsotopeDistribution
  {
 public:
    
    struct PMember
    {
      double power;
      double probability;
      PMember():power(0), probability(0) {}
    };
    typedef std::deque<struct PMember> Polynomial;
  
    MIDAs(EmpiricalFormula&, double, UInt);
    virtual void run() = 0;
    void merge(Polynomial&, double);
    void dumpIDToFile(String file);
 protected:
    EmpiricalFormula& formula_;
    double resolution_;
    UInt N;
  };


```

The polynomial serves as a `(mass, abundance)` tuple where the final samples are exported. As a future plan is to perform further refactoring and convert this to High resolution class or even merge this interface with the `IsotopeDistribution` class which is the current coarse grain class that OpenMS uses (my enemy :P).




#### Existing fast fourier algorithm testing

Original MIDAs implementation uses its own fourier transform. The fft was the only black box in the algorithm and has a critical role in its function. So I need to compare it with the kissfft method. So I tested the two algorithms in the same data separately and it did not yield the same results. After some frustation I started looking into the issue more. 

First I realized that the algorithm works when the sample size is in the power of two. So  the |sample| can be {2,4,8,16.., 2^n}. Otherwise it will segfault. For kissfft the only limitation is that the |sample| it cannot be odd. I believe this limitation it makes the current implementation algorithm a memory hog and I guess there is remove for improvement.

After managing the algorithm to run correctly the results were not the same. After some crawling in the web [I found this page](http://www.embedded.com/print/4017495) which is the same algorithm with the one I was investigating. This fft worked fine with the kissfft implementation. However I do not know the reason, but they changed some bits

Original algorithm:
```c

  void four1(double* data, unsigned long nn)
  {
          unsigned long n, mmax, m, j, istep, i;
          double wtemp, wr, wpr, wpi, wi, theta;
          double tempr, tempi;
// reverse-binary reindexing
          n = nn<<1;
          j=1;
          for (i=1; i<n; i+=2) {
                  if (j>i) {
                          swap(data[j-1], data[i-1]);
                          swap(data[j], data[i]);
                  }
                  m = nn;
                  while (m>=2 && j>m) {
                          j -= m;
                          m >>= 1;
                  }
                  j += m;
          };

// here begins the Danielson-Lanczos section
          mmax=2;
          while (n>mmax) {
                  istep = mmax<<1;
                  theta = -(2*M_PI/mmax);
                  wtemp = sin(0.5*theta);
                  wpr = -2.0*wtemp*wtemp;
                  wpi = sin(theta);
                  wr = 1.0;
                  wi = 0.0;
                  for (m=1; m < mmax; m += 2) {
                          for (i=m; i <= n; i += istep) {
                                  j=i+mmax;
                                  tempr = wr*data[j-1] - wi*data[j];
                                  tempi = wr * data[j] + wi*data[j-1];

                                  data[j-1] = data[i-1] - tempr;
                                  data[j] = data[i] - tempi;
                                  data[i-1] += tempr;
                                  data[i] += tempi;
                          }
                          wtemp=wr;
                          wr += wr*wpr - wi*wpi;
                          wi += wi*wpr + wtemp*wpi;
                  }
                  mmax=istep;
          }
  }


```

MIDAs implementation of four1:

```c

void  MIDAs:: four1(double *Data, int nn, int isign)
{
   unsigned long i, j, m, n, mmax, istep;
   double wr, wpr, wpi, wi, theta;
   double wtemp, tempr, tempi;
   double one_pi = acos(-1);
   double two_pi= 2*one_pi;
  
   /* Perform bit reversal of Data[] */
   n = nn << 1;
   j=1;
   for (i=1; i<n; i+=2)
     {
       if (j > i)
	 {
	   wtemp = Data[i];
	   Data[i] = Data[j];
	   Data[j] = wtemp;
	   wtemp = Data[i+1];
	   Data[i+1] = Data[j+1];
	   Data[j+1] = wtemp;
	 }
       m = n >> 1;
       while (m >= 2 && j > m)
	 {
	   j -= m;
	   m >>= 1;
	 }
       j += m;
     }
 
   /* Perform Danielson-Lanczos section of FFT */
    n = nn << 1;
    mmax = 2;
    while (n > mmax)  /* Loop executed log(2)nn times */
      {
	istep = mmax << 1;
	theta = isign * (two_pi/mmax);  
	wtemp = sin(0.5*theta);
	wpr = -2.0*wtemp*wtemp;
	wpi = sin(theta);
	wr = 1.0;
	wi = 0.0;
	for (m=1; m<mmax; m+=2)
	  {
	    for (i=m; i<=n; i+=istep)
	      {
		j = i+mmax;                      

		tempr = wr*Data[j]-wi*Data[j+1];
		tempi = wr*Data[j+1]+wi*Data[j];
		Data[j] = Data[i]-tempr;
		Data[j+1] = Data[i+1]-tempi;
		Data[i] += tempr;
		Data[i+1] += tempi;
	      }
	    wr = (wtemp=wr)*wpr-wi*wpi+wr;
	    wi = wi*wpr+wtemp*wpi+wi;
	  }
	mmax = istep;
      }

   
}


```

Essentially it is the same except the algorithm output is in `i+1` instead of `i` . (maybe they did it for copyright issues? who knows). Will the kissfft work seamlessly with the algorithm? This questions will be answered down the road.

