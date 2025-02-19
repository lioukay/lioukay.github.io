---
layout: post
title: Segregation energy at grain boundary
subtitle: Learn from literature
tags: [GB,SOAP,MD]
comments: true
mathjax: true
---

In alloys, atoms of type A could be replaced by another type of atoms, let's say, B. The replacement would cause a difference in energy. For sites in bulk, the $\Delta E$ is a constant in dilute alloy. However, for sites at grain boundaries (GBs), $\Delta E$ is no longer a single value for the complexity of local atomic environments. Malik Wagih (supervised by Christopher A. Schuh), showed the spectrum of $\Delta E$ with molecular dynamic.

The procedures are:
1. build polycrystal models for a given alloy system and run energy minimization, the energy of the system is recorded as $E_{c}^{solute}$.
2. identify "GB atoms" with OVITO, then label them.
3. replace each of the labelled atoms with the alloying atom, and re-calculate the relaxed energy as $E_{GB,i}^{solute}$.
4. Then $\Delta E = E_{GB,i}^{solute} - E_{c}^{solute}$

Beyond calculating the energy spectrum, the researcher managed to build a connection between local atomic environment and the segregation energy by linear  regression. The researchers are so generous that they provided the details of the work with a Jupyter notebook file. There are several inspiring techniques.

1. describe local atomic environments with SOAP
the code looks like these:
```python
import ase.io 
from quippy import descriptors
model = ase.io.read(filename,format="lammps-data",style="atomic",Z_of_type={1:13})
QUIP = descriptors.Descriptor("soap cutoff=8 l_max=10 n_max=10 atom_sigma=1.0 n_Z=1 Z={29}")
Soap = QUIP.calc(model)
```
2. Do machine learning with sklearn package. There are tremendous tutorials about that.

It should be noted there are one (or two) problem(s) in the document they provided.
1. the number of samples in file GB_SOAP_Al_Mg.npy is not consistent with that in file seg_energies_Al_Mg.txt. So when making use of the notebook, the SOAP should be recalculated by yourself.
2. in the latest version of OVITO package, some of the methods are greatly changed. So modifications on function get_gb_ids_and_indices(polycrystal) are needed, as is shown below.


```python
node.modifiers.append(ov.modifiers.ExpressionSelectionModifier(expression="StructureType == %i"%(t)))
select = data.particles['Selection']   
pid = data.particles['Particle Identifier']
```

All in all, that is excellent work! 
The title of the thesis is Learning Grain Boundary Segregation Energy Spectra in Polycrystals.

