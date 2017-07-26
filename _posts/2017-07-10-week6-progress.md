---
layout: post
---

#### Integrating MIDAs into OpenMS

A quiet week with not much to report. This week I implemented part of the MIDAs core functionality while trying to understand its underlying function. 

I have too say that some parts of the source code is a bit convoluted. For example the snippet below occurs during the initialization of the algorithm parameters:


```c++
  used_resolution = resolution;
  rho_resolution = 1.0;
  delta=1.0;
  flag=N=k=0;
  while(flag==0)
    {
      resolution = used_resolution/pow(2.0,k);
      mass_range  =  ft_variance_molecular_mass(element_composition);
      mass_range  =  int(15.0*sqrt(1+mass_range) + 1);
      mass_range=int(log(mass_range)/log(2.0)+1.0);
      mass_range=int(pow(2.0,mass_range));
      N= int(mass_range/resolution);
      N=int(log(N)/log(2.0)+1.0);    
      N=int(pow(2.0,N));
      delta = 1.0/N;  
      resolution = mass_range/N;     
      rho_resolution = resolution;
      k=k+1;
      if(rho_resolution <  used_resolution)
       {
          MASS_FREQUENCY_DOMAIN = (double*)malloc((2*N+1)*sizeof(double));  
	  flag=1;
       }
    }

```

The `resolution` parameter is specified as an algorithm parameter and defines the fine grain of the algorithm. Other than that some steps are redundant (but it works right? so no problem). Hopefully I will be able to cut some corners in the OpenMS version of the algorithm that I will implement.
