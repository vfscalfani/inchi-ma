# InChI Metal Architecture

Proof of concept to generate InChIs that distinguish structures with different
Square Planar, Trigonal Bipyramidal, and Octahedral configurations.

It adds a '/ma' (metal architecture) layer as proposed by Jonathan Goodman to
the end of an InChI. Currently the following geometries are supported:

* Square Planar: ``/ma<atom>s<order>`` where order is 1,2,3
* Trigonal Bipyramidal: ``/ma<atom>tb<order>`` where order is 1-20
* Octahedral: ``/ma<atom>o<order>`` where order is 1-30

Daylight used the term 'chiral order', I prefer 'order' as these
geometries are not necessarily chiral.

The order specifies a permutation index and uses the same coding
scheme as SMILES (see. [relevant blog post](http://timvdm.blogspot.com/2010/09/smiles-stereochemistry-enigma.html)).
It works because we need to be able to specify any order of the neighbors around
the central (or 'focus') atom. To specify any ordering for octahedral we have 6 
neighbors so there are 720 (6 factorial) possible ways to order them. However 
there are 24 symmetries and so we only need 720/24 = 30 possible orders. For 
trigonal bipyramidal, 120 (i.e. 5 factorial) ways to order but 6 symmetries, 
120/5 = 20 possible orders.

For each of these geometries we use a table to look up the
ordering we have ended up with from the InChI atom numbers (parsed from the 
AuxInfo). When there are symmetries within the neighbors we choose the lowest
possible ordering. Currently such symmetries are broken by re-enumeration but in
practice a backtracking canonical labelling algorithm (such as that used by the
InChI) can take care of this step.

### Examples

#### Square Planar

There are 16 possible orderings of Square Planar neighbors, for platin
when two of each neighbors are symmetric there two possibilities: *cis-platin*
and *trans-platin*. As shown below the ``/ma`` layer divides the possibilities
with ``/ma5sp1`` corresponding to *cis-*, and ``/ma5sp2`` to *trans-*.

Note the numbers here refer to the input atom order in the SMILES (see
[``examples/platin.smi``](https://github.com/johnmay/inchi-ma/blob/master/examples/platin.smi))
and not the InChI canonical numbers.

![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP1%5D%28Cl%29%28%5BNH3%5D%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP2%5D%28Cl%29%28%5BNH3%5D%29%5BNH3%5D+%2Fma5sp2&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP3%5D%28Cl%29%28%5BNH3%5D%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP1%5D%28%5BNH3%5D%29%28Cl%29%5BNH3%5D+%2Fma5sp2&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP2%5D%28%5BNH3%5D%29%28Cl%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP3%5D%28%5BNH3%5D%29%28Cl%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP1%5D%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP2%5D%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BPt%40SP3%5D%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma5sp2&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP1%5D%28Cl%29%28%5BNH3%5D%29Cl+%2Fma5sp2&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP2%5D%28Cl%29%28%5BNH3%5D%29Cl+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP3%5D%28Cl%29%28%5BNH3%5D%29Cl+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP1%5D%28Cl%29%28Cl%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP2%5D%28Cl%29%28Cl%29%5BNH3%5D+%2Fma5sp1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=%5BNH3%5D%5BPt%40SP3%5D%28Cl%29%28Cl%29%5BNH3%5D+%2Fma5sp2&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)

#### Octahedral

Note: The SMILES depiction will make some bonds longer to try fit in the expanded NO<sub>2</sub> group.

cis/trans- examples:

![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH1%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH2%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH3%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH4%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH5%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH6%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH7%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH8%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH9%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH10%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH11%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH12%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH13%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH14%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH15%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH16%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH17%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH18%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH19%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH20%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH21%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH22%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH23%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH24%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH25%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH26%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH27%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH28%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH29%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH30%5D%28Cl%29%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma5o12&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)

fac/mer- examples:

![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH1%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH2%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH3%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH4%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH5%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH6%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH7%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH8%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH9%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH10%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH11%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH12%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH13%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH14%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH15%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH16%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH17%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH18%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH19%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH20%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH21%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH22%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH23%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH24%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH25%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH26%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH27%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH28%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH29%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BCo%40OH30%5D%28Cl%29%28N%28%3DO%29%28%3DO%29%29%28N%28%3DO%29%28%3DO%29%29%28Cl%29N%28%3DO%29%3DO+%2Fma4o8&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)


#### Trigonal Bipyramidal

When we have 2 and 3 groups in TBPY there are 3 possibilities: two as the axis,
two equatorial, or one in the axis and one equatorial.

![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB1%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB2%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB3%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB4%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB5%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB6%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB7%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB8%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB9%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB10%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB11%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb5&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB12%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB13%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB14%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB15%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB16%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb17&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB17%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB18%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB19%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb17&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)
![](https://www.simolecule.com/cdkdepict/depict/bow/svg?smi=Cl%5BAs%40TB20%5D%28Cl%29%28%5BNH3%5D%29%28%5BNH3%5D%29Cl+%2Fma1tb1&abbr=on&hdisp=bridgehead&showtitle=true&zoom=1.6&annotate=number)

### Usage

A command line application is provided (available: [inchi-ma.jar](https://github.com/johnmay/inchi-ma/releases/latest)) that can provide InChIs with an ``/ma`` layer for a SMILES or 3D SDfile:

```
$ java -jar inchi-ma.jar input.smi
$ java -jar inchi-ma.jar input.sdf
```

Some example inputs are provided in the ``examples/`` directory.

### Limitations

Currently only constitutionally different neighbors are handled. The system used
here can also be used to encode geometries such as lambda/delta Fe(ox<sub>3</sub>)
if there was tighter integration within the canonically labelling procedure.

### Building

The project is built with [Apache Maven](https://maven.apache.org/).

```
$ mvn install
```

will generate the file ``target/inchi-ma.jar``.
