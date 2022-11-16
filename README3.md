#SETUP of InsituBloodFlow
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. Install Ubuntu on your laptop.
For windows users, follow this guide. If you are using a mac you can install ubuntu using terminal
commands.

For windows users, follow this guide
`https://ubuntu.com/tutorials/install-ubuntu-on-wsl2-on-windows-10#1-overview`

For Mac users follow this guide
`https://www.macupdate.com/how-to/mac-terminal-commands-list`

Follow steps 1-4. Create a username and password for ubuntu. Update the ubuntu terminal
every time the user logs in.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
2. Install latest updates for ubuntu
Type the following code in the terminal:

`sudo apt update`

then,

`sudo apt upgrade`

This will run a series of updates in which the user must select ”Y” to finalize the Ubuntu updates.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
3. Install openmpi
Install openmpi within the Ubuntu terminal. Openmpi is used for parallel computing

`sudo apt-get install libopenmpi-dev openmpi-bin libhdf5-openmpi-dev`

Check to see if openmpi has succesfully installed. Type the following in the command line:

`mpirun -np 2 echo hello`

The output should display:

hello
hello

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
4. Install palabos
Before Installing palabos, the some packages must be installed:

`sudo apt install gcc clang clang-format cmake make`

Press Y if prompted. To copy then paste, press control+C as normal to copy and then right click to paste.

Then palabos can be git cloned:

`git clone https://gitlab.com/unigespc/palabos.git`

Clone the Palabos repository on to the home directory. To go back to the
home directory of Ubuntu type

`cd`

 into the command line. Right click on the blinking cursor
within Ubuntu and hit enter. To see if Palabos has successfully installed and can run, follow the Cavity2d example.

`cd palabos/examples/showcases/cavity2D/build`

`cmake ..`

`make`

`cd ..`

`mpirun -np 2 Cavity2D`

where -np is number of processors used and can be adjusted accordingly based on your computer.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
5. install lammps
In the home directory, clone the Lammps repository into ubuntu. Copy the line of code on the
lammps website that looks like this

`git clone -b release https://github.com/lammps/lammps.git lammps`

then paste it into Ubuntu by right clicking, and pressing enter

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
6. install InSituBloodFlow
Download InSituBloodFlow to your home directory using gitclone by the same process:

`git clone https://github.com/TJFord/InsituBloodFlow.git`

if you are not in the home directory, the command cd will take you there, then do the git clone ...

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
7. Clone the executable files from InsituBloodFlow into Lammps src
Navigate into the InsituBloodFlow directory with the command

`cd InsituBloodFlow`

`ls`

A helpful trick to use is to
type cd and the first letter of the directory. Then hit tab and it will auto fill in the directories
name. This is an easy way to make sure the user does not botch the name of directories or file
locations.
Type the following in the command line:

`cd rbc`

`ls`

The rbc is defined as the red blood cells files and these green
files are their components in solving for various parameters that are used within the Lammps
software.

Open a second ubuntu terminal and use this as a reference for correctly copying the files and
their location. The destination of these executable files will be within Lammps src directory. On
the newly opened terminal, type the following into the command line:

`cd lammps/src`

`ls`

Begin copying the green files that are within the InsituBloodFlow rbc directory into
the Lammps src directory. In order to do this, use the following command while in rbc. Do not copy the
pair platelet morse prob. files.

`cp angle rbc.* bond wlc pow.* diherdral bend.* fix activate platelet.* fix fcm.* fix rls.* ../../lammps/src`

Check within the Lammps src directory to see if each executable was successfully copied over. This step is necessary in order to run the embolism example. Lammps
needed more information in order to properly run the necessary data being asked of it.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
8. Install Lammps packages
For this simulation, there are two packages that need to be installed into lammps that are not
present by default. These are Molecule and MC. To do this, navigate to lammps/src. Once there,
these are the commands to use:

`make yes-MOLECULE`

`make yes-MC`

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
9. Append flags
Once these commands have been entered, it is worth checking that some flags have the right
value. navigate to the lammps/src/MAKE directory from the src directory, and edit the file
Makefile.mpi using vim.

`cd MAKE`

`vim Makefile.mpi`

Once in this file, press the esc key to make sure vim is in viewing mode, and use the command

`:set nu`

This will show line numbers. On line 10, this should be shown

`CCFLAGS =        -g -03 -std=c++11`

If this is the case,

`:q`

can be pressed to quit without saving.
If this is not shown, navigate the cursor to line 10 using the arrow keys or h, j, k, l for left, down,
up, and right respectively. Once there, the i key can be pressed to enter insert mode. Once in
insert mode, correct the line to match line 157.
Once this is done, esc must be pressed to enter viewing mode again. Then the file must be saved
and exited. the command for this is

`(esc) :x`

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
10. Compile lammps as a library
For these packages to take effect, lammps must be complied as a library. This is simple. To
start, be in the lammps/src directory,

`:cd ..`

Then use the command

`make mpi mode=lib`

The changes should now be applied and this is all for the lammps/src.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
11. Create a Build directory within embolism
Navigate to the embolism directory by using this command

`cd InsituBloodFlow/examples/embolism/`

`ls`

Create a directory named “build” within the embolism directory. To do this type the following
into the command line:

`mkdir build`

Go into the build directory and type this following command:

`cd build`

`cmake ..`

`make -j6`

Ignore all warnings as they are just
part of the code. -jX makes running make command faster and depends on how many processors
the users device has. A successful make command will result with the output ”Built target
embolism."

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
12. Run embolism with mpirun
Run the simulation outside of the build directory and type the following commands:

`cd ..`

`mpirun -np 2 embolism in.embolism`

Successfully run the simulation. Note: -np X is the number of processors the user’s computer
owns. Do input the correct number of processors to run the simulation. If not, simulation freezes
and displays that this device does not have the required amount of core processors to run the
simulation.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
