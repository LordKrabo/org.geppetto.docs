


Geppetto Simulation File 
=====================

A Simulation file in Geppetto defines the entities, the models and the simulators for a specific simulation.
This aim of this tutorial is to guide you through the process of creating a Simulation file that can be loaded in Geppetto.

Getting Started
---------
Simulations used within Geppetto are specified within an XML file, which follows a specific schema_

.. _schema: https://github.com/openworm/org.geppetto.core/blob/master/src/main/resources/schema/simulation/simulationSchema.xsd

.. code-block:: xml

    <schema xmlns="http://www.w3.org/2001/XMLSchema" targetNamespace="http://www.openworm.org/simulationSchema"
		xmlns:tns="http://www.openworm.org/simulationSchema" elementFormDefault="qualified" 
		xmlns:jxb="http://java.sun.com/xml/ns/jaxb" jxb:version="2.0">
	<element name="simulation">
		<complexType>
			<sequence>
				<element name="entity" type="tns:Entity" maxOccurs="unbounded" minOccurs="1">
					<annotation>
						<appinfo>
							<jxb:property name="entities" />
						</appinfo>
					</annotation>
				</element>
				<element name="script" type="string" maxOccurs="unbounded" minOccurs="0"></element>
			</sequence>
		</complexType>
	</element>
		
	<complexType name="Model">
		<sequence>
			<element name="modelInterpreterId" type="string" maxOccurs="1" minOccurs="1"></element>
			<element name="modelURL" type="string" maxOccurs="1" minOccurs="1"></element>
			<element name="instancePath" type="string" maxOccurs="1" minOccurs="0"></element>
			<element name="parentAspect" type="tns:Aspect" maxOccurs="1" minOccurs="0"></element>
			<element name="recordingURL" type="string" maxOccurs="unbounded" minOccurs="0"></element>
		</sequence>
	</complexType>

	<complexType name="Simulator">
		<sequence>
			<element name="simulatorId" type="string" maxOccurs="1" minOccurs="0"></element>
			<element name="timeStep" type="tns:TimeStep" maxOccurs="1" minOccurs="0"></element>
			<element name="instancePath" type="string" maxOccurs="1"  minOccurs="0"></element>
			<element name="parentAspect" type="tns:Aspect" maxOccurs="1" minOccurs="0"></element>
			<element name="conversionServiceId" type="string" maxOccurs="1"	minOccurs="0"></element>
		</sequence>
	</complexType>

	<complexType name="TimeStep">
		<sequence>
			<element name="type" type="tns:TimeStepType" maxOccurs="1" minOccurs="1"></element>
			<element name="value" type="float" maxOccurs="1" minOccurs="0"></element>
		</sequence>
	</complexType>

	<complexType name="Point3D">
		<sequence>
		   <element name="x" type="float" maxOccurs="1" minOccurs="1"></element>
		   <element name="y" type="float" maxOccurs="1" minOccurs="1"></element>
		   <element name="z" type="float" maxOccurs="1" minOccurs="1"></element>
		</sequence>
	</complexType>

	<simpleType name="TimeStepType">
		<restriction base="string">
			<enumeration value="FIXED" />
			<enumeration value="VARIABLE" />
		</restriction>
	</simpleType>

	<complexType name="Aspect">
		<sequence>
			<element name="id" type="string" maxOccurs="1" minOccurs="1"></element>
			<element name="instancePath" type="string" maxOccurs="1" minOccurs="0"></element>
			<element name="simulator" type="tns:Simulator" maxOccurs="1" minOccurs="0"></element>
			<element name="model" type="tns:Model" maxOccurs="1" minOccurs="1"></element>
			<element name="parentEntity" type="tns:Entity" maxOccurs="1" minOccurs="0"></element>
		</sequence>
	</complexType>

	<complexType name="Entity">
		<sequence>
			<element name="id" type="string" maxOccurs="1" minOccurs="1"></element>
			<element name="instancePath" type="string" maxOccurs="1" minOccurs="0"></element>
			<element name="aspect" type="tns:Aspect" maxOccurs="unbounded" minOccurs="1">
				<annotation>
					<appinfo>
						<jxb:property name="aspects" />
					</appinfo>
				</annotation>
			</element>
			<element name="parentEntity" type="tns:Entity" maxOccurs="1" minOccurs="0"></element>
			<element name="entity" type="tns:Entity" maxOccurs="unbounded" minOccurs="0">
				<annotation>
					<appinfo>
						<jxb:property name="entities" />
					</appinfo>
				</annotation>
			</element>
			<element name="position" type="tns:Point3D" minOccurs="0" maxOccurs="1"></element>
		</sequence>
	</complexType>
    </schema>

The Simulation file starts with the head tag pointing to the different schema location that will be used to describe the file as seen below. 

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <tns:simulation xmlns:tns="http://www.openworm.org/simulationSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		    xsi:schemaLocation="http://www.openworm.org/simulationSchema ../../src/main/resources/schema/simulationSchema.xsd ">
    </tns:simulation>

The rest of the simulation file defines what entities exist in the project.

An **Entity** is the basic building block of the simulated world and represents an instance of something which needs to be simulated. 
An Entity can aggregate other Entities and can contain one or multiple Aspects. 
Examples of entities are for instance a cell, a tissue, an organ, etc.

An **Aspect** defines a particular characterization of an entity which is specified through a Model and a Simulator.
Aspects can be thought of as domain specific descriptions of an Entity.
A muscle cell for instance can be described by multiple aspects, one defining its electrical properties, one defining its mechanical structure, one for thermodynamics, etc.

An Aspect is defined through a **Model** and a **Simulator**.
A Model contains a URL which points to a specific domain model, e.g. a NeuroML file, and the id of the model interpreter which is capable of loading and visualizing it.
A Simulator specifies the id of the Geppetto simulator which should be used to simulate the model. It may also specify the conversion service to be used between the model interpreter and the simulator if needed.

Entities are logical containers which make possible to describe the hierarchy that describe the system which we want to simulate.
Geppetto is not strongly typed and an entity is solely described by its models and aggregated entities.

A Simulation file must have at least one entity, but there’s no cap on the maximum amount it can have.

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <tns:simulation xmlns:tns="http://www.openworm.org/simulationSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		    xsi:schemaLocation="http://www.openworm.org/simulationSchema ../../src/main/resources/schema/simulationSchema.xsd ">

	<tns:entity>
		<tns:id>hhcell</tns:id>
		<tns:aspect>
			<tns:id>electrical</tns:id>
			<tns:simulator>
				<tns:simulatorId>jLemsSimulator</tns:simulatorId>
			</tns:simulator>
			<tns:model>
    				<tns:modelInterpreterId>lemsModelInterpreter</tns:modelInterpreterId>
    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
			</tns:model>
		</tns:aspect>		
	</tns:entity>
    </tns:simulation>
    
.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <tns:simulation xmlns:tns="http://www.openworm.org/simulationSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		    xsi:schemaLocation="http://www.openworm.org/simulationSchema ../../src/main/resources/schema/simulationSchema.xsd ">

	<tns:entity>
		<tns:id>c302</tns:id>
		<tns:aspect>
			<tns:id>electrical</tns:id>
			<tns:simulator>
				<tns:simulatorId>neuronSimulator</tns:simulatorId>
				<tns:conversionServiceId>NeuroMLConversionService</tns:conversionServiceId>
			</tns:simulator>
			<tns:model>
    				<tns:modelInterpreterId>lemsModelInterpreter</tns:modelInterpreterId>
    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
			</tns:model>
		</tns:aspect>		
	</tns:entity>
    </tns:simulation>


Using Entities
---------------
As explained in previous section, a Simulation can have multiple entities, and each entity can contain multiple entities, forming a hierarchical tree structure. 

The elements used to describe an entity are the following:

- **ID** : Entity identifier

- **Aspect**: An Aspect specifies the Model and Simulator of an entity, multiples aspects can be specified within an entity. 

- **Simulator** : Defines the simulator used to execute the Model associated to this aspect via `<simulatorid>` inside the <simulator> tag. If the object model generated by the model interpreter is not compatible with the simulator Geppetto will automatically look for a conversion service. However a certain conversion service can be specified and it will be used to perform the conversion process. The tag to specify the conversion service is `<conversionServiceId>` and it will be placed inside the <simulator> tag.

- **Model** : The Model that defines the specific aspect for a given entity. Two tags are used for the model, `<modelURL>` which points to the URL and `<modelInterpreterId>` which specifies the Geppetto module that will be used to load and visualize it.

Below an example of nested entities where a Simulator is specified in the parent Entity, resulting in only one instance of the simulator responsible for executing all the models associated to the same aspect of the contained children entities.

.. code-block:: xml

	<?xml version="1.0" encoding="UTF-8"?>
	<tns:simulation xmlns:tns="http://www.openworm.org/simulationSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="../../main/resources/schema/simulationSchema.xsd">
	
		<tns:entity>
			<tns:id>network</tns:id>
			<tns:aspect>
				<tns:id>electrical</tns:id>
				<tns:simulator>
					<tns:simulatorId>jLemsSimulator</tns:simulatorId>
				</tns:simulator>
			</tns:aspect>
			<tns:aspect>
				<tns:id>mechanical</tns:id>
				<tns:simulator>
					<tns:simulatorId>sphSimulator</tns:simulatorId>
				</tns:simulator>
			</tns:aspect>
			<tns:entity>
				<tns:id>neuron1</tns:id>
				<tns:aspect>
					<tns:id>electrical</tns:id>
					<tns:model>
		    				<tns:modelInterpreter>lemsModelInterpreter</tns:modelInterpreter>
		    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
					</tns:model>
				</tns:aspect>
				<tns:aspect>
					<tns:id>mechanical</tns:id>
					<tns:model>
		    				<tns:modelInterpreter>sphModelInterpreter</tns:modelInterpreter>
		    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
					</tns:model>
				</tns:aspect>
			</tns:entity>
			<tns:entity>
				<tns:id>neuron2</tns:id>
				<tns:aspect>
					<tns:id>mechanical</tns:id>
					<tns:model>
		    				<tns:modelInterpreter>sphModelInterpreter</tns:modelInterpreter>
		    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
					</tns:model>
				</tns:aspect>
				<tns:aspect>
					<tns:id>electrical</tns:id>
					<tns:model>
		    				<tns:modelInterpreter>lemsModelInterpreter</tns:modelInterpreter>
		    				<tns:modelURL>https://raw.github.com/openworm/org.geppetto.samples/master/LEMS/SingleComponentHH/LEMS_NML2_Ex5_DetCell.xml</tns:modelURL>
					</tns:model>
				</tns:aspect>
	
			</tns:entity>
		</tns:entity>
	</tns:simulation>

Scripts
---------------
You can specify a `<script>` element within the root `<simulation>` element which allows to specify an external URL containing a javascript file with a set of Geppetto Commands_. 
The script will be executed right after the simulation is loaded, and the commands within the script executed in order one after another, see a Sample_ here.

.. _Sample: https://github.com/openworm/org.geppetto.samples/blob/master/LEMS/SingleComponentHH/HH_Geppetto_Script.js
.. _Commands: http://docs.geppetto.org/en/latest/intro.html#g-object-commands
  
.. code-block:: xml

    <tns:scripts>
      <tns:script>
        <tns:URL>https://dl.dropboxusercontent.com/u/7538688/electrofluid.py</tns:scriptURL>
       </tns:script>
    </tns:scripts>
    
