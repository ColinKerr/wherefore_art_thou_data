# What is an iModel?

An iModel is a Sqlite file with a fixed table structure designed for performance and scalability and an extensible metadata layer on top providing friendly queries.

The extensible metadata layer is called [BIS (Base Infrastructure Schema)](https://www.itwinjs.org/bis/) and you query it using [ECSQL](https://www.itwinjs.org/learning/ecsql/)

## What is BIS?

BIS is a set of object oriented relational schemas built on a few core concepts.  

## Core Schema Concepts

_________________

### Entity Class

Like a table in a standard relational database adds support for single inheritance and polymorphism.

### Relationship Class

Like a link table or foreign key in a standard relational database adds support for single inheritance and polymorphism.  Each relationship lists two Entity Classes as endpoints.

### Property Definition

Added to a class defining what would be a column in a standard database.

### Navigation Property

A special kind of property which represents a Foreign Key for a Relationship Class.

### Mixin

A special type of class definition that can be a relationship endpoint and can add classes to an entity class.  An Entity class may implement more than one mixin.

### Instance

Like a row in a standard relational database it is the instantiation of an Entity or Relationship class.

## Core BIS Concepts

 > To understand how these core concepts are used to model data checkout [Modeling with BIS](https://www.itwinjs.org/bis/intro/modeling-with-bis/)

_________________

### Elements

An [Element](https://www.itwinjs.org/bis/intro/element-fundamentals/) is an Entity class that serves as the base class for all data stored in an iModel.

Each Element may have one parent and any number of children, this parent/child hierarchy is used to build [assemblies](https://www.itwinjs.org/bis/intro/element-fundamentals/#assemblies).  This is defined using the Relationship Class [ElementOwnsChildElements](https://www.itwinjs.org/bis/domains/biscore.ecschema/#elementownschildelements) persisted using the navigation property `Element.Parent`.

### Element Aspects

An [Element Aspect Class](https://www.itwinjs.org/bis/intro/elementaspect-fundamentals/) is an Entity class that adds properties to a Instance of an Element.  

There are two types of Aspect class, [Unique Aspect and Multi Aspect](https://www.itwinjs.org/bis/intro/elementaspect-fundamentals/#core-elementaspect-types).  Only one instance of a Unique Aspect class may apply to an Element instance, any number of instances of an Multi Aspect may apply to a single Element instance.

Each Element Aspect Instance belongs to one and only one Element Instance and uses a navigation property `ElementAspect.Element` to point back to the Element it belongs to.  Unique Aspects use the [ElementOwnsUniqueAspect](https://www.itwinjs.org/bis/domains/biscore.ecschema/#elementownsuniqueaspect) relationship class and Multi Aspects use the [ElementOwnsMultiAspects](https://www.itwinjs.org/bis/domains/biscore.ecschema/#elementownsmultiaspects) relationship

### Models

A [Model](https://www.itwinjs.org/bis/intro/model-fundamentals/) is an Entity class that serves as a container for Elements.  A Model and the Elements it contains ['break down'](https://www.itwinjs.org/bis/intro/information-hierarchy/) an Element providing a more detailed representation of that element.

## Secondary BIS Concepts

 > This concepts help search for and understand data in an iModel

_________________

### PhysicalElement Class

The [Bis.PhysicalElement class](https://www.itwinjs.org/bis/intro/physical-models-and-elements/) is the base class for all elements that model real physical things.  

### FunctionalElement Class

The [Functional.FunctionalElement class](https://www.itwinjs.org/bis/intro/functional-models-and-elements/) is the base class for all elements that define requirements of a system

### Codes

[Codes](https://www.itwinjs.org/bis/intro/codes/) are unique, human readable identifiers for an Element.  Not every Element Instance is required to have a code.  Plant Tags are a good example of a use for code.

### TypeDefinition Classes

[Type Definitions](https://www.itwinjs.org/bis/intro/type-definitions/) are special Element classes whose instances are used to represent catalog items (Like a Pump, Door or Beam) that have fixed values.  The Type Definition is referenced by a normal Element instance using the [TypeDefinition](https://www.itwinjs.org/bis/domains/biscore.ecschema/#geometricelement3d) navigation property.

### Categories

[Categories](https://www.itwinjs.org/bis/intro/categories/) group Element instances and is similar to levels in MicroStation, layers in AutoCAD and categories in Revit.  Elements that can be categorized have a `Category` navigation property

### ElementRefersToElements Relationship Class

The [ElementRefersToElements](https://www.itwinjs.org/bis/domains/biscore.ecschema/#elementreferstoelements) relationship class defines the primary link table used to relate two Elements together.  It is used for things like grouping, linking drawings to the elements the represent, linking the functional and physical representations of an object.

### Subject Class

Subject Elements define high level grouping of the data within an iModel.  For example the Model Tree in Design Review is built using the subject hierarchy.  See this [Example Information Hierarchy](https://www.itwinjs.org/bis/intro/information-hierarchy/#example-information-hierarchy) for an idea of what they look like.