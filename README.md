# Pcell_Generation_Using_Python

how tedious is it to do manual layouts

# About the Tools

## GLayout
GLayout helps in doing analog layout as a tradition flow of analog include as shown below in the figure which is a very tedious task as compared to digital layout which doesn't include much of a manual work and this gap is brigded through the this tool. Question is HOW?

![Analog vs Digital Flow](https://github.com/user-attachments/assets/f7ce852b-8af6-44f9-a99c-d94f4e61a6e6)


1. What is we can generalize the design rules & layers.

2. What if we can generate the layout by tweaking the different parameters. 

3. What if we can do hierarchical component design along with routing & placement.

This is where GLayout comes into play........

GLayout provides us with the ```MappedPDK``` class which stores the technology specific rules/layers in python dictionaries providing the designers with the ```necessary layers``` & ```layout rules```.

![MappedPDK class](https://github.com/user-attachments/assets/a556060e-d46b-4a74-be38-b7dc0ae616ed)


# GLayout Utilities

## GLayers

1. Each process has a number of layers which will be called as generic layer and these generic layers are mapped to actual process layers using the MappedPDK class.

2. As we can see from below table that a polysilicon being a generic layer is mapped to actual process layer using the tuple data type in python for different technologies.

![glayers](https://github.com/user-attachments/assets/8dec5c81-2434-4b81-8cc8-826eb5cff08d)

***pdk.get_glayers()*** is a method within a MappedPDK class which stores the association of different layer specific to different process which can be used to select the layers to draw the geometries.

Each foundary uses different GDS layer number for different process steps for example you can refer the below table

![Screenshot 2024-07-26 102432](https://github.com/user-attachments/assets/1759f91d-e9f8-4747-86f6-421a42302ead)



## GRules

![Grules](https://github.com/user-attachments/assets/49033121-b808-49db-97c7-e68edbb3ac3a)


Each technology has a certain design rules which needs to be followed for our design to be tape-out ready and MappedPDK stores these rules for all the stack layers.

1. ***pdk.get_grules()*** is a method which helps us in accessing the specific rule related to each different layers be it a common dimensional checks like spacing, extension, minimum_width etc, arguments for this method include the generic layers that we are working on and the rule we want to access.

## Ports

Ports describe an edges of polygon, they include different attributes like width, center etc which will be used for the routing purposes.

![Ports](https://github.com/user-attachments/assets/ee4eb9a2-90d7-4ae4-9187-503165b42a95)

## Routing

To generate a specif macro with number of devices instantiated and connected with each other GLayout provides us with the three types of routes known as C route, Straight route & L route.



We will be using a python library called GDSFactory for handeling generic layout components & generating the layouts for integrated circuit and we will be saving it in GDSII format.

GLayout will be generating the DRC clean layout & SPICE netlist for any PDK, composed of two parts:

1. Generic PDK framework: Allows to describe any PDK in a standardize format defined by ```MappedPDK``` class.

2. Circuit generators: A python function which takes ```MappedPDK``` as an argument allowing us to set different parameters to produce a DRC clean layout 
 which can be written to a ```.gds``` file & SPICE netlist for a circuit component.

![GLayout Workflow](https://github.com/user-attachments/assets/edb7e5af-4546-40c9-b5a0-fa396d41c485)



.....gdsfactory content


# Installation

Run the following command to uninstall all conflicting packages:

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

1. Set up Docker's apt repository.
   
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. To install the latest version, run:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verify that the Docker Engine installation is successful by running the hello-world image.

```bash
sudo docker run hello-world
```

4. clone the OpenFASOC repository

```bash
git clone https://github.com/idea-fasoc/OpenFASOC.git
```

5. Navigate to the docker folder

```bash
cd OpenFASOC/docker/conda
```

6. Build and run the Docker container

```bash
#For Linux/WSL
sudo docker build -t openfasoc:glayout .
cd ../../
sudo docker run -v $(pwd):$(pwd) -w $(pwd) --name glayoutcontainer -it openfasoc:glayout
```

```python
pip install -r requirements.txt

pip install gdstk prettyprint
```

***for running graphical applications (such as klayout) in the docker container Open a terminal and run***

```bash
#For Linux/WSL
xhost +Local:*
```

7. Open the Docker Container (the following commands create a new container from the image)

```bash
#For Linux
sudo docker run -v $(pwd):$(pwd) -w $(pwd) -e DISPLAY -v /temp/.X11-unix:/tmp/.X11-unix --net=host -it --name glayoutcontainer openfasoc:glayout

#For WSL
docker run -it -v /tmp/.X11-unix:/tmp/.X11-unix -v /mnt/wslg:/mnt/wslg -v $(pwd):$(pwd) -w $(pwd)  -e DISPLAY -e WAYLAND_DISPLAY -e XDG_RUNTIME_DIR -e PULSE_SERVER --name glayoutcontainer openfasoc:glayout
```

**Miscellaneous Docker Commands -**
1. To remove the container, run the command: docker container rm glayoutcontainer
2. To restart the container, run the command docker container restart glayoutcontainer
3. To execute a running container, first check its status by running
   
   a. docker container ls -a
   
   b. docker exec -it glayoutcontainer bash (if glayoutcontainer is running) (-it runs the container in interactive mode)

***Note: ‘exit’ in a running docker container halts all running processes and stops. A graceful shutdown can be achieved by using ```docker stop glayoutcontainer```***

## Checking your Installation
Once the install is complete, you can check your install by running the following command

```bash
# assuming you are in the OpenFASOC directory
cd openfasoc/generators/glayout
python3 test_glayout.py
```

If you get the following error then try the below mentioned commands

![Numpy_error](https://github.com/user-attachments/assets/86e25f1b-f8bc-4f7e-ba59-107218d89f3e)

```
cd openfasoc/generators/glayout
python3.10 -m pip install "numpy2.0.0"
python3 test_glayout.py
```
This script will test if:
1. The python version meets requirements (3.10 or newer)
2. The conda packages have been installed
3. The PDKs sky130 or gf180 have been installed and are in their expected locations
4. Python packages have been properly installed
5. glayout is working as required. The script:
   
   a. places an nmos component
   
   b. runs a Layout-vs-Schematic check on it

## Installing kLive

1. Run your container using a previously described run command (Step 8 of Install with Docker)
2. Run KLayout by just typing ```klayout``` in the docker container
3. Install KLive
   
   a. Go to Tools -> Manage Packages -> Install New Packages -> Search
   
   b. Search for KLive
   
   c. Install the package that looks like this:
   
   ![kLive](https://github.com/user-attachments/assets/e9da2473-e47c-47d3-890f-d34ae94fb6f1)
   
5. Once it's installed, on your host system. Type in ```docker ps -a```. This will list all of your running containers. Copy the container ID of the most recently started one or the one you installed klive
6. Enter the command ```docker commit <container id> openfasoc:glayout```

you can follow the following playlist to view practical implementation of installation [here](https://www.youtube.com/watch?v=omjUs0bUGHo&list=PLlSXtIpWKQOBaUgQ6gQqfz39XhUoLEWgj) or the following document [here](https://docs.google.com/document/d/e/2PACX-1vRL8ksIvB-fHaqWgkgBPDUznOcDmmFhNrvzPNx9GSSkZyfhJYexEI9gBZCJ0SNNnHdUrAf1EBOeU182/pub) for all the different platforms.

--------------------------------


```
python <filename>
```
If you get the following error then try the below mentioned commands

![Numpy_error](https://github.com/user-attachments/assets/86e25f1b-f8bc-4f7e-ba59-107218d89f3e)


```python
python3.10 -m pip install "numpy2.0.0"
python <filename>
```


## Using glayer

```python
#Importing the mappedPDK class for sky130 & gf180 process
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

#Using get_glayer() method
sky130_met1 = sky130.get_glayer("met1")
gf180_met1  = gf180.get_glayer("met1")
sky130_poly = sky130.get_glayer("poly")
gf180_poly  = gf180.get_glayer("poly")

#printing tuple for met1 in respective process
print(f"sky130_met1:{sky130_met1}")
print(f"gf180_met1: {gf180_met1}")
print(f"sky130_poly: {sky130_poly}")
print(f"gf180_poly: {gf180_poly}")
```
![tuple.py](https://github.com/user-attachments/assets/ae0a6a72-5cfe-4abd-9c1b-8fb5d4472fcf)


## Using grule

```python
#Minimum Width
#Importing the mappedPDK class for sky130 & gf180 process
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

#Using the get_grule() method
min_width_via1 = sky130.get_grule('via1')["min_width"]
//it returns a dictonary of layout rules 

#Printing the minimum width
print(f"minimmum width for via1 {min_width_via1}")
```
![min_width](https://github.com/user-attachments/assets/48db3654-e64e-41b5-86b6-6a8ed944d949)


```python
#Minimum Enclosure
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

min_enc_met1 = sky130.get_grule('via1','met1')["min_enclosure"]  //get_grule -- Returns a dictionary describing the relationship between two layers
        If one layer is specified, returns a dictionary with all intra layer rules
min_enc_met2 = sky130.get_grule('via1','met2')["min_enclosure"]


print("sky130")
print(f"min enclosure met1 and via1: {min_enc_met1}")
print(f"min enclosure met2 and via1: {min_enc_met2}")


min_enc_met1 = gf180.get_grule('via1','met1')["min_enclosure"]
min_enc_met2 = gf180.get_grule('via1','met2')["min_enclosure"]


print("gf180")
print(f"min enclosure met1 and via1: {min_enc_met1}")
print(f"min enclosure met2 and via1: {min_enc_met2}")
```

![min_enc](https://github.com/user-attachments/assets/56e67e1a-ff91-4094-99ae-ab68e269bad1)


```python
#Minimum Spacing
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

min_met1_spacing = sky130.get_grule('met1', 'met1')["min_separation"]
min_met1_spacing = gf180.get_grule('met1', 'met1')["min_separation"]

print("sky130")
print(f"min spacing between metal1: {min_met1_spacing}")

print("gf180")
print(f"min spacing between metal1: {min_met1_spacing}")
```
![min_spacing](https://github.com/user-attachments/assets/d170937b-a9f7-4025-af0e-70526a0ba44c)


## Drawing Actual Layout

```python
from glayout.flow.pdk.mappedpdk import MappedPDK
from gdsfactory import component
from gdsfactory.components import rectangle

//defining a top level component using the glayout "component" function which which includes the geometries that we want to generate in out layout.

def met1track(pdk: MappedPDK, lenght):
met1 = pdk.get_glayer("met1")
met1_width = pdk.get_grule('met1')["min_width"]
top_level  = component(name="metal_track")
top_level << rectangle(size=(length, met1_width), layer=met1) //using stream insertion operator helps to add the object into the component --> look into rectangle function once syntax
return top_level

metalTrack = met1track(sky130,3)
metalTrack.write_gds('met1track.gds')

#To view the layout in klayout
metalTrack.show()
```
![met1_track](https://github.com/user-attachments/assets/5fb52fff-864b-45fd-a0d2-4a472fb9bbb6)


## Using and manipulating the component

```python
from glayout.flow.pdk.mappedpdk import MappedPDK
from gdsfactory import component
from gdsfactory.components import rectangle
from glayout.flow.pdk.util.comp_utils import evaluate_bbox

def adjacentmet1track(pdk: MappedPDK, length)
track1 = met1track(pdk, length)
track2 = met1track(pdk, length)
top_level = component(name="adjacent_metal_track")
track1_ref = top_level << track1
track2_ref = top_level << track2
width = evaluate_bbox(track1)[1] #getting dimension on the y-axis
track2_ref.movey(width + pdk.get_grule('met1','met1')['min_separation']
return top_level

adjmettrack = adjacentmet1track(sky130,3)
adjmettrack.show()
adjmettrack.write_gds('adjmettrack.gds')
```

![adj_met1_tracks](https://github.com/user-attachments/assets/fd5f62b4-2ba6-404c-ae0c-1868b0ada8db)


## Using Primitives
 
 You can find all the primitives that glayout provides with their source code [here](https://github.com/idea-fasoc/OpenFASOC/tree/main/openfasoc/generators/glayout/glayout/flow/primitives)

```python
from glayout.flow.primitive.fet import pmos
from glayout.flow.primitive.fet import nmos

def twotransistor(pdk)
nfet = nmos(pdk)
pfet = pmos(pdk)
top_level = component()
pfet_ref = top_level << pfet
nfet_ref = top_level << nfet
compsep = pdk.util_max_metal_separation()  //returns the maximum of the min_seperation rule for all layers specfied
        although the name of this function is util_max_metal_seperation, layers do not have to be metals
        you can specify non metals by using metal_levels=list of glayers
        if metal_levels is list of int, integers are converted to metal levels
        if a single int is provided, all metals below and including that int level are considerd
        by default this function returns the maximum metal seperation of metals1-5
nfet_ref.movex(compsep + evaluate_bbox(pfet)[0])
return top_level

twoadjtransistor = twotransisitor(sky130)
twoadjtransistor.write_gds(twatransisitor.gds)
twoadjtransistor.show()
```

![DEFAULT -- NFET(L), PFET(R)](https://github.com/user-attachments/assets/28cd0e44-a14a-4917-b762-8451a4de705e)

![NFET(with_substrate_tap=False, fingers=2), PFET(DEFAULT)](https://github.com/user-attachments/assets/bbec529a-9aff-4d85-abc9-a4a27b680226)

![NFET(with_substrate_tap=False, fingers=2, multipliers=2), PFET(DEFAULT)](https://github.com/user-attachments/assets/4c0fe0df-df7c-4e4f-96b6-2d2210a0b24e)

![NFET(with_substrate_tap=False, fingers=2, multipliers=2, with_tie=false), PFET(DEFAULT)](https://github.com/user-attachments/assets/7d1c95f0-682e-477f-a863-08cd8c86e765)

![NFET(with_substrate_tap=False, fingers=2, multipliers=2, with_tie=false, with_dummy=false), PFET(DEFAULT)](https://github.com/user-attachments/assets/ed9a62d0-7cbd-4bd6-97e9-b1fadd70f6e2)

![NFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false, PFET(DEFAULT)](https://github.com/user-attachments/assets/4ae1ae53-530d-4de2-a25c-58849fa09cc8)

![NFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false), PFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false)](https://github.com/user-attachments/assets/cc327696-649d-4db7-b2fd-e8bedfc381ce)




## Routing & Ports




### Important Links to Refer

[sscs-ose/sscs-ose-chipathon.github.io](https://github.com/sscs-ose/sscs-ose-chipathon.github.io)

[GLayout uick Start Guide Notebook](https://colab.research.google.com/drive/1mU7-zmAWgDRJqaZGP-RkSXCSgRkHOb--?usp=sharing#scrollTo=-Xp4cEjkeHIx)


