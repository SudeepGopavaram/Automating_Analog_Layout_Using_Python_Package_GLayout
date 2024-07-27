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

--------------------------------




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

#Printing tuple values
print(sky130_met1)
print(gf180_met1)
print(sky130_poly)
print(gf180_poly)
```
## Using grule

```python
#Minimum Width
#Importing the mappedPDK class for sky130 & gf180 process
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

#Using the get_grule() method
min_width_via1 = sky130.get_grule('via1')['min_width']
//it returns a dictonary of layout rules 

#Printing the minimum width
print(f"minimmum width for via1 {min_width_via1}")
```

```python
#Minimum Enclosure
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

min_enc_met1 = sky130.get_grule('via1','met1')['min_enclosure']
min_enc_met2 = sky130.get_grule('via1','met2')['min_enclosure']


print("sky130")
print(f"min enclosure met1 and via1: {min_enc_met1}")
print(f"min enclosure met2 and via1: {min_enc_met2}")


min_enc_met1 = gf180.get_grule('via1','met1')['min_enclosure']
min_enc_met2 = gf180.get_grule('via1','met2')['min_enclosure']


print("gf180")
print(f"min enclosure met1 and via1: {min_enc_met1}")
print(f"min enclosure met2 and via1: {min_enc_met2}")
```

```python
#Minimum Spacing
from glayout.flow.pdk.sky130_mapped import sky130_mapped_pdk as sky130
from glayout.flow.pdk.gf180_mapped import gf180_mapped_pdk as gf180

min_met1_spacing = sky130.get_grule('met1','met1')["min_separation"]

print(f"min spacing between metal: {min_met1_spacing}")
```
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


### Important Links to Refer

[sscs-ose/sscs-ose-chipathon.github.io](https://github.com/sscs-ose/sscs-ose-chipathon.github.io)

[GLayout uick Start Guide Notebook](https://colab.research.google.com/drive/1mU7-zmAWgDRJqaZGP-RkSXCSgRkHOb--?usp=sharing#scrollTo=-Xp4cEjkeHIx)

