---
layout: post
---

This is a special blog post that presents some ideas that were inspired from my work in Google Summer of Code.

### Motivation

The native OpenMS method that works with nomimal masses can append during runtime other isotope distributions using the `+=` and `+` operator. 
The native method also employs the `*` and `*=` operators that take as input an `unsigned int` convolves the isotope distribution with itself. 
Internally, each time an operator is invoked a convolution takes place and a new isotope distribution is computed.

The new methods of OpenMS in their current state can compute theoretical isotope patterns of compounds with known empirical formulas.
This might be a problem when one needs to combine two distributions of elements or two discrete probability distributions.
It seems that the methods should be related with a more abstract concept than an empirical formula of a compound.
Overview how the methods work so far:
```
foreach element,size in EmpiricalFormula:
  calculate_element_distribution(element.isotope),size)

calculate_final_distribution(id)

```

This is working closely with Empirical formula but in fact we need is the collection of tuples `IsotopeDistribution, number of molecules`.


### "Lazy" isotope distribution evaluation

What really is needed is a transparent method of implementing the `*` and `+` operators transparently without much computational overhead.

Changes to the IsotopeDistribution data model (it just explains the logic):

```
class IsotopeDistribution
{
vector<Peak1D> id;

map<IsotopeDistribution, unsigned int> individual_ids;

void computeID();

operator+(IsotopeDistribution&);
operator*(unsigned int);

};

```

The `id` will remain empty until `id` is accessed or `computeID()` is invoked.

The `operator+` will become:

```
void IsotopeDistribution::operator+(IsotopeDistribution& added_distribution)
{

if individual_ids.has_key(added_distribution)
   individual_ids[added_distribution]++;
else
   individual_ids[added_distribution] = 1;
}

```

The `operator*` will become:

```
void IsotopeDistribution::operator*(unsigned int size)
{
    IsotopeDistribution new_id;
    new_id.set_individual_ids(this->individual_ids);
    individual_ids.clear();
    individual_ids[new_id] = size;
}

```

`computeID()` will work in a tree manner that will compute all the earlier stages and finally compute the final distribution at the top level.


The greatest challenge for this is implementing a hash function for the map data structure that will identify quickly and accurately same isotopedistribution components and group them together.







