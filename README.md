# LAMMPS files | DIADEM

## Download LAMMPS-GUI

Alternatively, LAMMPS-GUI can also be downloaded from these links:

- [LAMMPS-GUI (.exe)](https://github.com/akohlmey/lammps-gui/releases/download/v3.0.7/LAMMPS-GUI-Win10-x86_64-v3.0.7.exe)
  for Windows
- [LAMMPS-GUI (.tar.gz)](https://github.com/akohlmey/lammps-gui/releases/download/v3.0.7/LAMMPS-GUI-Linux-x86_64-v3.0.7.tar.gz)
  for Linux

Alternatively, LAMMPS-GUI can be downloaded from
[https://github.com/akohlmey/lammps-gui/releases/tag/v3.0.7](https://github.com/akohlmey/lammps-gui/releases/tag/v3.0.7).


## Problem with opening LAMMPS-GUI ?

See [this page](https://lammps-gui.lammps.org/installation.html)

## First input

```bash
# Simple NVE argon simulation

units           lj # Use Lennard-Jones reduced units
dimension       3 # Perform the simulation in 3 spatial dimensions
atom_style      atomic # Atoms are treated as point particles without bonds or molecular topology
boundary        p p p # Use periodic boundary conditions

region          simbox block -6 6 -6 6 -6 6 # Define the simulation box from -6 to 6 in x, y, and z
create_box      1 simbox # Create a simulation box containing one atom type
create_atoms    1 random 864 34134 simbox overlap 0.7 # Randomly create N atoms inside the simulation box

mass            1    1.0 # Assign a mass to atom type 1 (mass = 1.0)
pair_style      lj/cut 4.0 # Use a Lennard-Jones pair potential with a cutoff distance of 4.0
pair_coeff      1    1    1.0 1.0 # Set Lennard-Jones parameters for atom type 1 (epsilon = 1.0 sigma = 1.0)

fix             mynve    all      nve # Integrate the equations of motion
timestep        0.0025 # Set the integration timestep

thermo          100 # Print thermodynamic information in log
thermo_style    custom step temp etotal ke pe density # Choose what information is printed

dump            viz      all      image 500 myimage-*.ppm type type size 800 800 zoom 1.452 shiny 0.5 fsaa yes view 0 0 box yes 0.005 axes no 0.0 0.0 center s 0.483725 0.510373 0.510373
dump_modify     viz pad 9 boxcolor white backcolor black adiam 1 1.2 acolor 1 cyan

run             25000
```

## Second input

```bash
# Simple NVT simulation of a fcc crystal with impurities

units           lj # Use Lennard-Jones reduced units
dimension       3 # Perform the simulation in 3 spatial dimensions
atom_style      atomic # Atoms are treated as point particles without bonds or molecular topology
boundary        p p p # Use periodic boundary conditions

lattice         fcc 1.55 origin 0.25 0.25 0.25
region          simbox prism -3 3 -3 3 -3 3 0.0 0.0 0.0 # Define the simulation box from -6 to 6 in x, y, and z
create_box      2 simbox # Create a simulation box containing two atom type
create_atoms    1 region simbox # Create atoms along the predefined lattice
lattice         none 1

mass            1    1.0 # Assign a mass to type one
mass            2    1.7 # Assign a mass to type one
pair_style      lj/cut 4.0 # Use a Lennard-Jones pair potential with a cutoff distance of 4.0
pair_coeff      1    1    1.0 1.0 # Set Lennard-Jones parameters for atom type 1
pair_coeff      2    2    1.0 1.2 # Set Lennard-Jones parameters for atom type 2 (atoms of type are slightly bigger)

set             group all type/fraction 2 0.0 46415 # Replace a fraction X of atoms with type 2

velocity        all create 0.1 4928459 rot yes mom yes dist gaussian
fix             mynve    all      nve # Integrate the equations of motion
timestep        0.0025 # Set the integration timestep to 0.0025 in LJ reduced time units
fix             myber1   all      temp/berendsen 0.1 0.1 0.1 # Set the temperature (unitless)

thermo          100 # Print thermodynamic information in log
thermo_style    custom step temp etotal ke pe density pxy pyz pxz # Choose what information is printed

dump            viz      all      image 100 myimage-*.ppm type type size 800 800 zoom 1.00 shiny 0.5 fsaa yes view 0 90 box yes 0.005 axes no 0.0 0.0 center s 0.483725 0.510373 0.510373
dump_modify     viz pad 9 boxcolor white backcolor black adiam 1 1 adiam 2 1.2 acolor 1 cyan acolor 2 purple

run             1000 # System equilibration
```
