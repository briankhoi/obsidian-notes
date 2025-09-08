Object technology is a set of principles guiding software construction together with languages, databases, and other tools that support those principles.

An object is an entity with:
- unique entity
- state (attributes)
	- condition in which the object exists, which may change over time
	- defined by the value of its attributes
- behavior (operations)
	- how an object acts and reacts to requests from other objects
	- determined by the operations the object can perform

A class is a description of a set of objects that share the same structure and behavior. It serves as a template for creating objects, and thus an object is an instance of a class. It includes:
- class name
- structure (attributes)
- behavior (operations)

Four Principles of Object Orientation
- abstraction: description of an entity that highlights essential characteristics of an entity relative to a given perspective, and manages complexity by eliminating unnecessary details
- encapsulation: grouping of related elements (attributes & operations) into a single entity. it facilitates information hiding (limits access to fields and hides implementation details) by providing interfaces and private fields. this protects an object's internal state from being corrupted by its clients, and protects the client's code from changes in the object's implementation
- hierarchy: ranking or ordering of entities into a tree like structure, with the top of the tree being highest abstraction layer
	- aggregation hierarchy: hierarchy in which an entity is made up of other entities (e.g. robot first layer connected to second layer of body part and joint)
	- inheritance hierarchy: hierarchy in which a subclass (child) inherits from one (or more) superclass (parent) (e.g. car level 1, connected to racecar and sedan level 2)
		- a subclass inherits its parent's attributes, operations, and relationships and can add its own and override parent methods
		- supports code re-use and ability to create new classes by abstracting out common attributes, behaviors, and relationships
	- polymorphism: ability for an object to take many forms. allows program to be extensible to add subclasses without changing rest of code
- modularity: breaking a large and complex system into smaller manageable modules. the goal is to achieve low coupling and high cohesion
	- low coupling: modules are independent, you can change one without affecting the others
	- high cohesion: each module has a unique responsibility

Unified Modeling Language (UML)
A language for modelling different views of a system
- class and component diagrams used to describe structure of system
- activity and state diagrams used to describe behavior of system
- pick a subset of diagrams that help you reach your specific goal
