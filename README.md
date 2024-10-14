# Digital Layout vs. Analog Layout

## Digital Layout
Digital layout refers to the design of digital circuits, which primarily involve logic gates, flip-flops, and other digital components. The process is often automated and involves the following steps:

1. **Schematic Design**: Creating a high-level representation of the circuit.
2. **Synthesis**: Converting the high-level design into a gate-level netlist.
3. **Placement**: Determining the positions of the gates on the chip.
4. **Routing**: Connecting the gates with wires.
5. **Verification**: Ensuring the design meets timing, power, and area constraints.

   **Characteristics**:
- **Automation**: Highly automated, reducing manual effort.
- **Regularity**: Digital circuits are often more regular and repetitive.
- **Scalability**: Easier to scale for larger designs.

## Analog Layout
Analog layout involves designing circuits that deal with continuous signals, such as amplifiers, oscillators, and filters. The process is more manual and includes:

1. **Schematic Design**: Creating a detailed circuit diagram.
2. **Component Placement**: Manually placing components to optimize performance.
3. **Routing**: Manually routing connections to minimize noise and parasitics.
4. **Parasitic Extraction**: Analyzing parasitic elements like capacitance and inductance.
5. **Simulation and Tuning**: Iteratively simulating and tuning the design for optimal performance.

**Characteristics**:
- **Manual Effort**: Requires significant manual intervention.
- **Precision**: High precision is needed to meet performance specifications.
- **Complexity**: More complex due to the need to manage parasitics and noise.

## Which is More Tedious?

**Analog Layout** is generally considered more tedious due to the following reasons:

- **Manual Effort**: Requires a lot of manual placement and routing.
- **Precision**: High precision is needed, making the process more time-consuming.
- **Complexity**: Managing parasitics and noise adds to the complexity.
- **Iterative Process**: Often requires multiple iterations of simulation and tuning.

In contrast, **Digital Layout** benefits from higher levels of automation, making it less tedious and more scalable for larger designs.

This repository explores the fundamentals of **GLayout** engine which is Python based and calls the **GDSFactory** tool for layout manipulation. 

# About the Tools

## GLayout
GLayout helps in doing analog layout as a tradition flow of analog include as shown below in the figure which is a very tedious task as compared to digital layout which doesn't include much of a manual work and this gap is brigded through the this tool. Question is **HOW?**

Circuit blocks are written with the help of Python functions which accept several parameters like a normal Parameterized Cells (PCells) also PDK as a parameter.

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


# GDSFactory

A python library which is being used in entire process of designing integrated circuits layout and save it directly to GDSII format

It has a generic PDkK framework which helps in describing any PDK in a standardized format with the help of ```MappedPDK``` class along with some optional layout parameters to produce DRC & LVS clean layout which can be used for extraction of pre-PEX SPICE netlist for simulating the circuit


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
1. To remove the container, run the command: ```docker container rm glayoutcontainer```
2. To restart the container, run the command ```docker container restart glayoutcontainer```
3. To execute a running container, first check its status by running
   
   *a.* ```docker container ls -a```
   
   *b.* ```docker exec -it glayoutcontainer bash``` (if glayoutcontainer is running) (-it runs the container in interactive mode)

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
python3.10 -m pip install "numpy<2.0.0"
python3 test_glayout.py
```

This script will test if:
*1.* The python version meets requirements (3.10 or newer)
*2.* The conda packages have been installed
*3.* The PDKs sky130 or gf180 have been installed and are in their expected locations
*4.* Python packages have been properly installed
*5.* Glayout is working as required. The script:
   
   *a.* Places an **nmos** component
   
   *b.* Runs a **Layout-vs-Schematic** check on it

**After a successful you should be able to get these output*

![Terminal_Test](https://github.com/user-attachments/assets/824bd5a5-179a-47a8-9c2d-dc90c083ad34)

![Klayout_Test](https://github.com/user-attachments/assets/76e139a9-fa9f-4379-aa40-40356986deda)


## Installing kLive

1. Run your container using a previously described run command (Step 8 of Install with Docker)
2. Run KLayout by just typing ```klayout &``` in the docker container
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
#pfet = pmos(pdk, dummy_routes=False, with_substrate_tap=False, fingers=1, multipliers=1, with_tie=False, with_dummy=False)
#nfet = nmos(pdk, with_substrate_tap=False, fingers=1, multipliers=1, with_tie=False, with_dummy=False, dummy_routes=False)
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

Below are the different variations that I generated by tweaking different parameters in the code.

![DEFAULT -- NFET(L), PFET(R)](https://github.com/user-attachments/assets/28cd0e44-a14a-4917-b762-8451a4de705e)
            

![NFET(with_substrate_tap=False, fingers=2), PFET(DEFAULT)](https://github.com/user-attachments/assets/bbec529a-9aff-4d85-abc9-a4a27b680226)


![NFET(with_substrate_tap=False, fingers=2, multipliers=2), PFET(DEFAULT)](https://github.com/user-attachments/assets/4c0fe0df-df7c-4e4f-96b6-2d2210a0b24e)


![NFET(with_substrate_tap=False, fingers=2, multipliers=2, with_tie=false), PFET(DEFAULT)](https://github.com/user-attachments/assets/7d1c95f0-682e-477f-a863-08cd8c86e765)


![NFET(with_substrate_tap=False, fingers=2, multipliers=2, with_tie=false, with_dummy=false), PFET(DEFAULT)](https://github.com/user-attachments/assets/ed9a62d0-7cbd-4bd6-97e9-b1fadd70f6e2)


![NFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false, PFET(DEFAULT)](https://github.com/user-attachments/assets/4ae1ae53-530d-4de2-a25c-58849fa09cc8)


![NFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false), PFET(with_substrate_tap=False, fingers=1, multipliers=1, with_tie=false, with_dummy=false)](https://github.com/user-attachments/assets/cc327696-649d-4db7-b2fd-e8bedfc381ce)




# Routing & Ports

Glayout provides with various placement techniques which you can view from [here](https://github.com/idea-fasoc/OpenFASOC/tree/main/openfasoc/generators/glayout/glayout/flow/routing)

## C_Route 

creates a C shaped route between two Ports.
![croute](https://github.com/user-attachments/assets/0765b51d-ed8b-410d-914e-c5985f37c6ed)

## Straight_Route

extends a route from edge1 until perpindicular with edge2, then places a via
This depends on the orientation of edge1 and edge2	if edge1 has the same orientation as edge2, the generator will rotate edge2 180 degrees Will not modify edge1 or edge2
![sroute](https://github.com/user-attachments/assets/d7021b9f-6835-4ddb-9cd7-6412b0c766ad)

## L_Route

creates a L shaped route between two Ports.
![lroute](https://github.com/user-attachments/assets/9884ecc2-1883-42b4-aba7-99ca7b75be0e)


# Placement

Glayout provides with various placement techniques which you can view from [here](https://github.com/idea-fasoc/OpenFASOC/tree/main/openfasoc/generators/glayout/glayout/flow/placement)

## Common Centroid (CC) & Interdigitated Matching Techniques

These are the widely used analog design to make the circuit resistant to variations by matching device characterstics

# Inverter

Let's go through a basic inverter cell using Glayouot

```
#Netlist
from glayout.flow.spice.netlist import Netlist

#Primitives
from glayout.flow.primitives.fet import pmos, nmos
#from glayout.flow.primitives.fet import nmos
from glayout.flow.primitives.guardring import tapring

# Standard
from glayout.flow.pdk.mappedpdk import MappedPDK
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from gdsfactory import Component

# Utility
from glayout.flow.pdk.util.comp_utils import evaluate_bbox, prec_center
from glayout.flow.pdk.util.port_utils import rename_ports_by_orientation

# Routing
from glayout.flow.routing.straight_route import straight_route
from glayout.flow.routing.c_route import c_route

def inverter(
           pdk: MappedPDK,
           wid: tuple[float,float] = (3,3),
           len: tuple[float,float] = (None,None),
           finger: tuple[int,int] = (1,1),
           multiplier: tuple[int,int] = (1,1),
           dummy_1: tuple[bool,bool] = (True,False),
           dummy_2: tuple[bool,bool] = (False,True),
           substrate_tap: bool = False,
           with_tie: bool = True,
           tie_layers: tuple[str,str] = ("met2","met1"),
            ) -> Component:
    
    # Create a top level component
    top_level = Component("inverter")
    
    pfet_model = pdk.models['pfet']
    nfet_model = pdk.models['nfet']

    # We want two pfets
    pfet = pmos(pdk, width=wid[0], fingers=finger[0], with_substrate_tap=False, with_dummy=(False, False))
    nfet = nmos(pdk, with_dnwell=False, with_substrate_tap=False, with_dummy=(False, False))
    pfet_ref = top_level << pfet
    nfet_ref = top_level << nfet

    # Relative move
    ref_dimensions = evaluate_bbox(pfet)
    nfet_ref.movey(-ref_dimensions[0] - 10*pdk.util_max_metal_seperation())

    # Routing
    top_level << c_route(pdk, pfet_ref.ports["multiplier_0_drain_E"], nfet_ref.ports["multiplier_0_drain_E"])
    top_level << c_route(pdk, pfet_ref.ports["multiplier_0_gate_W"], nfet_ref.ports["multiplier_0_gate_W"])
    
       #top_level.info['netlist'] = inverter_netlist(sky130, pmos, nmos, circuit_name='INVERTER', models=[pdk.models['pfet'], pdk.models['nfet']])
    
    #top_level.info['netlist'] = inverter_netlist(pfet, nfet)
    #print(top_level.info['netlist'].generate_netlist(only_subcircuits=True))
    
    #print(top_level.info['netlist'].generate_netlist(only_subcircuits=True))
    #print(top_level.info['netlist'])
    return top_level 

myinv=inverter(sky130)
myinv.show()
#sky130.drc_magic(layout= "/home/sudeep/Documents/Glayout/test_inverter.gds", design_name='myinv')
#sky130.drc(layout= "/home/sudeep/Documents/Glayout/test_inverter.gds")
#myinv.write_gds('test_inverter.gds')
```

![Inverter](https://github.com/user-attachments/assets/e8e301ba-7a90-47bd-a6c6-5569190de4ff)


**Netlisting of the Inverter cell**

We can generate a pre-PEX SPICE netlist for the component which can be viewd using

```component.info['netlist'].generate_netlist()```which I have used in the below code to extract netlist.

```
#Netlist
from glayout.flow.spice.netlist import Netlist

#Primitives
from glayout.flow.primitives.fet import pmos, nmos
#from glayout.flow.primitives.fet import nmos
from glayout.flow.primitives.guardring import tapring

# Standard
from glayout.flow.pdk.mappedpdk import MappedPDK
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from gdsfactory import Component

# Utility
from glayout.flow.pdk.util.comp_utils import evaluate_bbox, prec_center
from glayout.flow.pdk.util.port_utils import rename_ports_by_orientation

# Routing
from glayout.flow.routing.straight_route import straight_route
from glayout.flow.routing.c_route import c_route


def inverter_netlist(pfet: Component, nfet: Component) -> Netlist:
        inverter_netlist = Netlist(circuit_name='INVERTER', nodes=['VCC', 'VIN', 'GND', 'VOUT'])
        inverter_netlist.connect_netlist(
                pfet.info['netlist'],
	        [('D', 'VOUT'), ('G', 'VIN'), ('S', 'VCC'), ('B', 'VCC')]
        )
        inverter_netlist.connect_netlist(
                nfet.info['netlist'],
	        [('D', 'VOUT'), ('G', 'VIN'), ('S', 'GND'), ('B', 'GND')]
        )
        return inverter_netlist


def inverter(
           pdk: MappedPDK,
           wid: tuple[float,float] = (3,3),
           len: tuple[float,float] = (None,None),
           finger: tuple[int,int] = (1,1),
           multiplier: tuple[int,int] = (1,1),
           dummy_1: tuple[bool,bool] = (True,False),
           dummy_2: tuple[bool,bool] = (False,True),
           substrate_tap: bool = False,
           with_tie: bool = True,
           tie_layers: tuple[str,str] = ("met2","met1"),
            ) -> Component:
    
    # Create a top level component
    top_level = Component("inverter")
    
    pfet_model = pdk.models['pfet']
    nfet_model = pdk.models['nfet']

    # We want two pfets
    pfet = pmos(pdk, width=wid[0], fingers=finger[0], with_substrate_tap=False, with_dummy=(False, False))
    nfet = nmos(pdk, with_dnwell=False, with_substrate_tap=False, with_dummy=(False, False))
    pfet_ref = top_level << pfet
    nfet_ref = top_level << nfet

    # Relative move
    ref_dimensions = evaluate_bbox(pfet)
    nfet_ref.movey(-ref_dimensions[0] - 10*pdk.util_max_metal_seperation())

    # Routing
    top_level << c_route(pdk, pfet_ref.ports["multiplier_0_drain_E"], nfet_ref.ports["multiplier_0_drain_E"])
    top_level << c_route(pdk, pfet_ref.ports["multiplier_0_gate_W"], nfet_ref.ports["multiplier_0_gate_W"])
    
       #top_level.info['netlist'] = inverter_netlist(sky130, pmos, nmos, circuit_name='INVERTER', models=[pdk.models['pfet'], pdk.models['nfet']])
    
    top_level.info['netlist'] = inverter_netlist(pfet, nfet)
    print(top_level.info['netlist'].generate_netlist(only_subcircuits=True))
    
    #print(top_level.info['netlist'].generate_netlist(only_subcircuits=True))
    #print(top_level.info['netlist'])
    return top_level 

myinv=inverter(sky130)
myinv.show()
#sky130.drc_magic(layout= "/home/sudeep/Documents/Glayout/test_inverter.gds", design_name='myinv')
#sky130.drc(layout= "/home/sudeep/Documents/Glayout/test_inverter.gds")
#myinv.write_gds('test_inverter.gds')
```

![Netlist](https://github.com/user-attachments/assets/574a39b7-5b60-4653-80c6-e8f0a8baec81)

# DRC and LVS

## Design Rule Check

 **D**esign **R**ule **C**heck -  Checks the interaction of layout shapes

 1. Spacing
 2. Width
 3. Enclosure & Overlap
 4. Antenna
 5. Density
 6. Required layers

   ![DRC](https://github.com/user-attachments/assets/adb78201-3a29-4f2a-813d-de7e08df874b)

   # 


We can check for DRC to ensure layout does not violate the constraints imposed by the PDK. 
Design rules for the *Skywater 130 PDK* are described in a ```.lydrc``` [file](https://github.com/idea-fasoc/OpenFASOC/blob/8835b6a0f4d18e355b713a7b9efe7e64a95433a3/openfasoc/generators/glayout/glayout/flow/pdk/sky130_mapped/sky130.lydrc).

```
!klayout out.gds -zz -r glayout/pdk/sky130_mapped/sky130.lydrc
!echo -e "\n$(grep -c "" sky130_drc.txt) DRC Errors Found"
```

This is a basic script which decodes into to following

 * !klayout: Executes the KLayout command. 

 * out.gds: Specifies the GDS file to be checked. 

 * -zz: Runs KLayout in no-GUI mode. 

 * -r glayout/pdk/sky130_mapped/sky130.lydrc: Specifies the DRC rule file to be used. 

 * !echo -e: Executes the echo command with the -e option to enable interpretation of backslash escapes. 

 * "\n$(grep -c "" sky130_drc.txt) DRC Errors Found": 

 * \n: Adds a new line. 

 * $(grep -c "" sky130_drc.txt): Uses grep to count the number of lines in the file sky130_drc.txt, which presumably contains the DRC errors. 

 * DRC Errors Found: Adds this text after the number of errors found. 

 We can use other DRC related functions to do this check like **drc_magic()** or **drc()***, you can find there description [here](https://github.com/idea-fasoc/OpenFASOC/blob/8835b6a0f4d18e355b713a7b9efe7e64a95433a3/openfasoc/generators/glayout/glayout/flow/pdk/mappedpdk.py#L344)

## Antenna

Damages to gate caused by charge buildup while etching
--> Damage is cumulative
--> Mitigated by mosfet drain connections, "antenna" diodes, or routing changes

![Screenshot from 2024-10-13 09-49-40](https://github.com/user-attachments/assets/752ecc0f-28fe-4a31-bb96-feda3b4d828e)


![Screenshot from 2024-10-13 09-50-57](https://github.com/user-attachments/assets/0602e824-9437-4ae0-8336-dd779795e468)

## Density
Ensures the uniformity and avoid issues related to manufacturing variability
--> maximum and minimum density
--> overlapping grid based check: for sky130 700um x 700um window at 70um step
--> minimum density generally not a problem because of fill
--> minimum clear density = 1 - maximum density (77% maximum density = 23% minimum clear density)
	EX: (77% maximum density = 23% minimum clear density)

## Layout versus Schematic

LVS consist of

1. Extraction
* Layout patterns -> hierarchical text "netlist" of connected parameterized devices

  ![Screenshot from 2024-10-13 09-54-12](https://github.com/user-attachments/assets/425a7107-9a5f-4edf-81b6-ca87d007ee23)


2. Comparison
* Netlists converted to graphs with devices/subcircuits as nodes and nets as edges
* Paraller/Series reduction 
* Topology matching
* Parameter check
* Port check

  ![Screenshot from 2024-10-13 09-58-12](https://github.com/user-attachments/assets/55725718-e0af-4815-a567-806ac808307f)

  ![Screenshot from 2024-10-13 10-01-13](https://github.com/user-attachments/assets/11cfe74f-ae9b-4bed-848f-b71f7a9049eb)

  ![Screenshot from 2024-10-13 10-02-51](https://github.com/user-attachments/assets/c42f5e5b-67d9-4097-ab93-6c561ea4679e)

  * Magic can  be used for (DRC/Extraction) and Netgen (LVS)
    	* 


  


Resistor reduction
Mosfet reduction

# Parasitic Extraction (PEX)

We can calculate the parasitic capacitance, resistance and inductive effect of an layout that we created for simulation. The script shown below is needed to run the PEX or you can find it [here](https://github.com/idea-fasoc/OpenFASOC/blob/main/openfasoc/generators/glayout/tapeout/tapeout_and_RL/extract.bash.template)

```
#!/bin/bash

# Actual
export PDK_ROOT=@@PDK_ROOT

# args:
# first arg = gds file to read
# second arg = name of top cell in gds file to read
# third arg (optional) = noparasitics (basically an LVS extraction)

paropt="@@@PAROPT"

if [ "$paropt" = "noparasitics" ]; then

magic -rcfile ./sky130A/sky130A.magicrc -noconsole -dnull << EOF
gds read $1
flatten $2
load $2
select top cell
extract do local
extract all
ext2sim labels on
ext2sim
ext2spice lvs
ext2spice cthresh 0
ext2spice -o $2_pex.spice
exit
EOF

else

magic -rcfile ./sky130A/sky130A.magicrc -noconsole -dnull << EOF
gds read $1
flatten $2
load $2
select top cell
extract do local
extract all
ext2sim labels on
ext2sim
extresist tolerance 10
extresist
ext2spice lvs
ext2spice cthresh 0
ext2spice extresist on
ext2spice -o $2_pex.spice
exit
EOF

fi

rm -f $2.nodes
rm -f $2.ext
rm -f $2.res.ext
rm -f $2.sim
```


# Sizing of transistors


### Important Links to Refer

**1.***[sscs-ose/sscs-ose-chipathon.github.io](https://github.com/sscs-ose/sscs-ose-chipathon.github.io)*

**2.***[GLayout quick Start Guide Notebook](https://colab.research.google.com/drive/1mU7-zmAWgDRJqaZGP-RkSXCSgRkHOb--?usp=sharing#scrollTo=-Xp4cEjkeHIx)*

**3.***[Human Language to Analog Layout Using GLayout Layout Automation Framework](https://dl.acm.org/doi/10.1145/3670474.3685971)*

**4.***[readthedocs](https://openfasoc.readthedocs.io/en/latest/index.html)*

