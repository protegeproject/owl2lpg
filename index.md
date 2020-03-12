![W3C Logo](http://www.w3.org/Icons/w3c_home)

# Mapping of OWL 2 Web Ontology Language to Labeled Property Graphs (OWL2LPG)

Working Draft, Updated: 20 March 2020



**Feedback**:

​	[rafael.goncalves@stanford.edu](mailto:rafael.goncalves@stanford.edu)

​	[josef.hardi@stanford.edu](mailto:josef.hardi@stanford.edu)

**Document Source Control**:

​	[GitHub](https://github.com/protegeproject/owl2lpg/) (in branch: [gh-pages-markdown](https://github.com/protegeproject/owl2lpg/tree/gh-pages-markdown))

**Issue Tracking**:

​	[GitHub](https://github.com/protegeproject/owl2lpg/issues/)

**Editors**:

​	[Rafael Gonçalves](https://rsgoncalves.com) (Stanford University)

​	[Josef Hardi](https://profiles.stanford.edu/josef-hardi) (Stanford University)

​	[Matthew Horridge](https://web.stanford.edu/~horridge) (Stanford University)

**Contributors**:

​	[Alexander García Castro](mailto:alexander.garcia-castro@basf.com) (BASF)

​	[José Antonio Bernabé Díaz](mailto:jose-antonio.bernabe-diaz@basf.com) (BASF)

​	[Juergen Mueller](mailto:juergen.a.mueller@basf.com) (BASF)



---



## Abstract

This document specificies a mapping of OWL 2 Web Ontology Language to Labeled Property Graphs (LPGs), and vice versa.



## Status of this document

This is a public copy of the editors’ draft. It is provided for discussion only and may change at any moment. Do not cite this document other than as work in progress.



---

**Table of Content**

* [1 Introduction](#1-introduction)
  
   * [1.1 Document conventions](#11-document-conventions)
   * [1.2 Main Requirements](#12-main-requirements)
      * [1.2.1 Tooling Requirements](#121-tooling-requirements)
      * [1.2.2 BASF Use-Case Requirements](#122-basf-use-case-requirements)
   * [1.3 Design Choices](#13-design-choices)
   * [1.4 Similar Mappings](#14-similar-mappings)
      * [1.4.1 SciGraph](#141-scigraph)
      * [1.4.2 VirtualFlyBrain](#142-virtualflybrain)
      * [1.4.3 OWL2Neo4J](#143-owl2neo4j)
   
* [2 Class Expressions](#2-class-expressions)
  
   * [2.1 Propositional Connectives and Enumeration of Individuals](#21-propositional-connectives-and-enumeration-of-individuals)
      * [2.1.1 Intersection and Union of Class Expressions](#211-intersection-and-union-of-class-expressions)
      * [2.1.2 Complement of Class Expressions](#212-complement-of-class-expressions)
      * [2.1.3 Enumeration of Individuals](#213-enumeration-of-individuals)
   * [2.2 Object Property Restrictions](#22-object-property-restrictions)
      * [2.2.1 Existential and Universal Quantification](#221-existential-and-universal-quantification)
      * [2.2.2 Individual Value Restriction](#222-individual-value-restriction)
      * [2.2.3 Self-Restriction](#223-self-restriction)
   * [2.3 Object Property Cardinality Restrictions](#23-object-property-cardinality-restrictions)
   * [2.4 Data Property Restrictions](#24-data-property-restrictions)
      * [2.4.1 Existential and Universal Quantification](#241-existential-and-universal-quantification)
      * [2.4.2 Literal Value Restriction](#242-literal-value-restriction)
   * [2.5 Data Property Cardinality Restrictions](#25-data-property-cardinality-restrictions)
   
* [3 Axioms](#3-axioms)
   * [3.1 Class Expression Axioms](#31-class-expression-axioms)
      * [3.1.1 Subclass](#311-subclass)
      * [3.1.2 Equivalent Classes](#312-equivalent-classes)
      * [3.1.3 Disjoint Classes](#313-disjoint-classes)
   * [3.2 Object Property Axioms](#32-object-property-axioms)
      * [3.2.1 Object Subproperty](#321-object-subproperty)
      * [3.2.2 Equivalent Object Properties](#322-equivalent-object-properties)
      * [3.2.3 Disjoint Object Properties](#323-disjoint-object-properties)
      * [3.2.4 Inverse Object Properties](#324-inverse-object-properties)
      * [3.2.5 Object Property Domain and Range](#325-object-property-domain-and-range)
      * [3.2.6 Object Property Characteristics](#326-object-property-characteristics)
   * [3.3 Data Property Axioms](#33-data-property-axioms)
      * [3.3.1 Data Subproperty](#331-data-subproperty)
      * [3.3.2 Equivalent Data Properties](#332-equivalent-data-properties)
      * [3.3.3 Disjoint Data Properties](#333-disjoint-data-properties)
      * [3.3.4 Data Property Domain](#334-data-property-domain)
      * [3.3.5 Data Property Range](#335-data-property-range)
      * [3.3.6 Data Property Characteristics](#336-data-property-characteristics)
   * [3.4 Assertions](#34-assertions)
      * [3.4.1 Class Assertions](#341-class-assertions)
      * [3.4.2 Object Property Assertions](#342-object-property-assertions)
      * [3.4.3 Data Property Assertions](#343-data-property-assertions)
      * [3.4.4 Individual Equality](#344-individual-equality)
      * [3.4.5 Individual Inequality](#345-individual-inequality)
   
* [4 Annotations](#4-annotations)
   * [4.1 Annotation of Ontologies, Axioms, and other Annotations](#41-annotation-of-ontologies-axioms-and-other-annotations)
   * [4.2 Annotation Axioms](#42-annotation-axioms)
      * [4.2.1 Annotation Assertions](#421-annotation-assertions)
      * [4.2.2 Annotation Subproperty](#422-annotation-subproperty)
   
* [5 Change History](#5-change-history)   

   

---



## 1 Introduction

This document specifies a mapping of OWL 2 EL axioms to a Labeled Property Graph (LPG) representation, and vice-versa. The mapping we specify here can be used to transform any OWL 2 EL ontology into a Labeled Property Graph. While initially targeted to transform OWL 2 EL ontologies, the design of this mapping is intended to be extensible to OWL 2 axioms of arbitrary expressivity.



### 1.1 Document conventions

OWL axioms are denoted using [OWL Functional-Style syntax](https://www.w3.org/TR/owl2-syntax).

For succintness, the base prefix of OWL entities' IRIs is assumed to be `http://protege.stanford.edu/basf/`, and only denoted by the empty prefix ':' when we write out axioms.

> For example, the entity with IRI `http://protege.stanford.edu/basf/A `is shortened to `:A `in axioms.



### 1.2 Main Requirements

In this section we lay out important requirements that drive the design of the OWL to LPG mapping.

#### 1.2.1 Tooling Requirements

Here we enumerate illustrative examples of the kinds of queries that should be both easily expressible (in [Cypher](https://neo4j.com/developer/cypher-query-language)) and well performant for large knowledge bases. We use the [WebProtégé cloud-based ontology editor](https://webprotege.stanford.edu) as the baseline for performance. The goal is to achieve a query performance that is superior to the performance of executing the same queries in WebProtégé 4.0 (non-LPG).

Example queries:
1. Get the axioms in a frame for class `A`.
2. Get the axioms that mention `A`.
3. Get the revisions for an ontology `O`.
4. Get the revisions that alter the frame for `A`.
5. Get the authors of changes to `A`.
6. Get the last changes for `A`.
7. Get the axioms in the latest revision of `O`.

#### 1.2.2 BASF Use-Case Requirements

TBA



### 1.3 Design Choices

Our overarching design principle is to prioritize representational **consistency over convenience** (of query writing, of query response time, etc.). Below we describe in detail some key design choices, which serve to allow us to fulfil the requirements laid out in [Section 1.2](#requirements).

- **OWL axiom types are represented as nodes in a LPG**. Each OWL axiom is represented with a single node that denotes its type. For example, `rdfs:SubClassOf` and `owl:EquivalentClasses` axiom types will each be represented by its own node. This decision allows two things to happen:

  1. We can encode OWL axiom annotations in a manner that is consistent with our overall representation of OWL entity annotations (as nodes attached to the annotated-entity node).
  2. We can attach versioning information about the axiom, such as the ontology that contains the axiom, and details about the author, the date, and the number of the revision that last altered the axiom (see [Section 4](#change-history) for full details).

- **In general, every resource that has an IRI is represented as a node in a LPG**. All OWL named entities are represented as nodes.

- **OWL class expression types are represented as nodes in a LPG**. This follows from the previous design choice. For example, `owl:someValuesFrom` and `owl:intersectionOf` class expression types will each be represented by its own node.

- **Nodes denoting OWL Classes, Individuals, and Datatypes are reused when possible.  Other nodes are duplicated to guarantee unambiguous round-tripping between OWL and LPG.** 

  When mapping OWL to LPG, we generally reuse existing LPG nodes that denote OWL classes, individuals, and datatypes. The exception is when in the presence of General Concept Inclusion (GCI) axioms with a complex class expression as the subclass (e.g., things that have pets are kinds of `PetOwner`, i.e, `:hasPet some :Pet SubClassOf :PetOwner`). In such cases, the nodes to represent the complex class expression in the subclass position of the axiom will not reuse existing nodes for the same entities in the LPG; they will be duplicated. We will duplicate all other nodes (e.g., OWL axiom types, OWL properties) as necessary to disambiguate how OWL axioms can be parsed from the LPG.

  For example, consider the axioms: `A SubClassOf p some B. A SubClassOf p some C. B SubClassOf D.` Here, the nodes for `A` and `B` would be reused, but the nodes for `SubClassOf, p,` and `some` would be duplicated. We duplicate the `SubClassOf` node (and generally, axiom type nodes) to be able to attach axiom annotations and versioning information on individual axioms. We duplicate the other nodes to prevent ambiguity about the structure of each axiom when parsing the LPG.

  The ability to parse back OWL axioms from a LPG converted as specified here is guaranteed by the conditions that:
  
  1. every LPG node denoting an OWL class, individual, or datatype is followed by a node denoting an OWL axiom type; and
  2. nodes involved in a complex class expression in the subclass position of a GCI are duplicated (rather than reused) in the mapping.
  
  The result of this design choice is illustrated in [Figure 24](#multiple-axioms-lpg).
  
  An alternative strategy to encode sufficient information about axiom structure to allow round-tripping is by: Maintaining one node for each unique OWL resource (one node per IRI); Assigning globally unique identifiers to axioms; and then either:
  
  1. naming edges with the axiom identifier, e.g., edge between `A` and `SubClassOf`, and so on; or
  2. storing the axiom identifier in a key-value string property on the edges. As a consequence, we would potentially need to encode lists of axiom identifiers as values in string properties of LPGs.



### 1.4 Similar Mappings

In this section we compare and contrast the mapping described in this document with existing approaches to transform OWL ontologies to LPGs.

#### 1.4.1 SciGraph

The [SciGraph Neo4j Mapping](https://github.com/SciGraph/SciGraph/wiki/Neo4jMapping) aims to support representing multiple ontologies as a Labeled Property Graph. SciGraph reads ontologies with the OWL API and converts them to a LPG. *SciGraph does not aim to support creating ontologies based on the LPG.* Below we outline and briefly describe the main differences between ours and the SciGraph mapping.

- **OWL axiom types are represented as edges in a LPG.** In the SciGraph mapping, axiom types such as `rdfs:subClassOf` are encoded as edges between nodes that represent the left and the right hand side of the axiom. Axiom annotations are represented as key-value string properties on the edge that denotes the axiom type. In our mapping, axiom types are represented as nodes, and therefore axiom annotations are represented uniformly like other annotation assertions.
- **Annotation assertions are represented as key-value string properties in a LPG.** For example, `i:Individual {'rdfs:label' = 'Ruth'}`. As a consequence, annotations on annotations are not straightforwardly representable in SciGraph.
- **Representation of source ontology in SciGraph is done both at the node and edge level of a LPG.** In the SciGraph mapping, nodes have an outgoing edge named `isDefinedBy` to a node of type `Ontology` that denotes the source ontology, and edges have a key-value string property to denote the source ontology IRI. In our mapping, the source ontology of axioms will be encoded as a node connected to the nodes denoting the axiom types (e.g., `rdfs:SubClassOf, owl:EquivalentClasses`).
- **SciGraph represents OWL properties as both nodes and edges in a LPG.** It represents OWL property types (functional, symmetric, etc.) as key-value properties on LPG **nodes** that represent the OWL properties (e.g., `hasSibling:ObjectProperty {'symmetric' = true}`). And then SciGraph uses OWL properties as **edges** in axioms such as object property assertions (e.g., `rafael:Individual --hasSibling:ObjectProperty --> ruth:Individual`. In our mapping, we consistently represent OWL properties as nodes. More, we represent OWL property characteristics similarly to the OWL/XML and Functional-style syntaxes of OWL. E.g., `hasSibling:ObjectProperty --> SymmetricObjectProperty:Axiom`.

#### 1.4.2 VirtualFlyBrain
The [VirtualFlyBrain OWL 2 EL to Neo4J Mapping](https://github.com/VirtualFlyBrain/neo4j2owl) allows importing "a well defined subset of OWL 2 EL ontologies into and export them from Neo4J, in such a way that entailments and annotations are preserved (not however the syntactic structure) in the ontology after the round-trip." The VirtualFlyBrain (VFB) mapping is explicit about its design goal of facilitating intuitive writing of Cypher queries. In that sense, VFB does not create "anonymous" nodes to represent axiom types or class expression types, and instead encodes these details on edges (relying on both edge names and key-value string properties on edges).

- **Object property names and axiom types are encoded as edge names.** For example, the axioms: `A SubClassOf B. A SubClassOf r some C` would be encoded in LPG as: `A--SubClassOf-->B. A--r-->B`. The restriction qualifiers (or quantifiers) are encoded as key-value properties on edges.
- **The VFB mapping represents OWL properties as both nodes and edges.** Similar to SciGraph, OWL object properties are represented as edges, and then they are also represented as nodes to be able to express axioms such as annotations on those properties.
- **Data and Annotation property assertions are represented as key-value string properties.** Also similar to SciGraph, both data and annotation property assertions are represented as key-value string properties on nodes. As a consequence, annotations on annotations are not straightforwardly representable in VFB.

#### 1.4.3 OWL2Neo4J

The [OWL2Neo4J tool](https://github.com/flekschas/owl2neo4j) allows converting OWL ontologies to Labeled Property Graphs. The tool documentation states that it "only converts the class hierarchy; instances are ignored for now." Overall it is unclear what subset of the OWL language is supported, and whether round-tripping is feasible. This project looks defunct—it was last updated on September 2018.



## 2 Class Expressions

### 2.1 Propositional Connectives and Enumeration of Individuals

#### 2.1.1 Intersection and Union of Class Expressions

An *intersection* class expression `ObjectIntersectionOf( CE1 ... CEn )` contains all individuals that are instances of all class expressions `CEi` for 1 ≤ i ≤ n.

A *union* class expression `ObjectUnionOf( CE1 ... CEn )` contains all individuals that are instances of at least one class expression `CEi` for 1 ≤ i ≤ n.

<u>OWL 2 Notation</u>:

​	**ObjectIntersectionOf** := 'ObjectIntersectionOf' '(' **ClassExpression** **ClassExpression** ')'

​	**ObjectUnionOf** := 'ObjectUnionOf' '(' **ClassExpression** **ClassExpression** ')'

<u>LPG Diagram</u>:

![class-expression-object-set-operations](images/class-expression-object-set-operations.png)



#### 2.1.2 Complement of Class Expressions

A complement class expression `ObjectComplementOf( CE )` contains all individuals that are not instances of the class expression `CE`.

<u>OWL 2 Notation</u>:

​	**ObjectComplementOf** := 'ObjectComplementOf' '(' **ClassExpression** ')'

<u>LPG Diagram</u>:

<img src="images/class-expression-object-complement.png" alt="class-expression-object-complement" width="500px"/>



#### 2.1.3 Enumeration of Individuals

An enumeration of individuals `ObjectOneOf( a1 ... an )` contains exactly the individuals `ai` with 1 ≤ i ≤ n.

<u>OWL 2 Notation</u>:

​	**ObjectOneOf** := 'ObjectOneOf' '(' **Individual** { **Individual** }')'

<u>LPG Diagram</u>:

<img src="images/class-expression-object-one-of.png" alt="class-expression-object-one-of" width="500px" />



### 2.2 Object Property Restrictions

#### 2.2.1 Existential and Universal Quantification

An *existential* class expression `ObjectSomeValuesFrom( OPE CE )` consists of an object property expression `OPE` and a class expression `CE`, and it contains all those individuals that are connected by `OPE` to an individual that is an instance of `CE`. 

A *universal* class expression `ObjectAllValuesFrom( OPE CE )` consists of an object property expression `OPE` and a class expression `CE`, and it contains all those individuals that are connected by `OPE` only to individuals that are instances of `CE`. 

<u>OWL 2 Notation</u>:

​	**ObjectSomeValuesFrom** := 'ObjectSomeValuesFrom' '(' **ObjectPropertyExpression** **ClassExpression** ')'

​	**ObjectAllValuesFrom** := 'ObjectAllValuesFrom' '(' **ObjectPropertyExpression** **ClassExpression** ')'

<u>LPG Diagram</u>:

![class-expression-object-restrictions](images/class-expression-object-restrictions.png)



#### 2.2.2 Individual Value Restriction

A has-value class expression `ObjectHasValue( OPE a )` consists of an object property expression `OPE` and an individual a, and it contains all those individuals that are connected by `OPE` to `a`. 

<u>OWL 2 Notation</u>:

​	**ObjectHasValue** := 'ObjectHasValue' '(' **ObjectPropertyExpression** **Individual** ')'

<u>LPG Diagram</u>:

![class-expression-object-has-value](images/class-expression-object-has-value.png)



#### 2.2.3 Self-Restriction

A self-restriction `ObjectHasSelf( OPE )` consists of an object property expression `OPE`, and it contains all those individuals that are connected by `OPE` to themselves.

<u>OWL 2 Notation</u>:

​	**ObjectHasSelf** := 'ObjectHasSelf' '(' **ObjectPropertyExpression** ')'

<u>LPG Diagram</u>:

<img src="images/class-expression-object-has-self.png" alt="class-expression-object-has-self" width="500px" />



### 2.3 Object Property Cardinality Restrictions

A *minimum cardinality* expression `ObjectMinCardinality( n OPE CE )` consists of a nonnegative integer `n`, an object property expression `OPE`, and a class expression `CE`, and it contains all those individuals that are connected by `OPE` to at least `n` different individuals that are instances of `CE`.

A *maximum cardinality* expression `ObjectMaxCardinality( n OPE CE )` consists of a nonnegative integer `n`, an object property expression `OPE`, and a class expression `CE`, and it contains all those individuals that are connected by `OPE` to at most `n` different individuals that are instances of `CE`.

An *exact cardinality* expression `ObjectExactCardinality( n OPE CE )` consists of a nonnegative integer `n`, an object property expression `OPE`, and a class expression `CE`, and it contains all those individuals that are connected by `OPE` to exactly `n` different individuals that are instances of `CE`.

In all cases, if `CE` is missing, it is taken to be *owl:Thing*.

<u>OWL 2 Notation</u>:

​	**ObjectMinCardinality** := 'ObjectMinCardinality' '(' **nonNegativeInteger** **ObjectPropertyExpression** [ **ClassExpression** ] ')'

​	**ObjectMaxCardinality** := 'ObjectMaxCardinality' '(' **nonNegativeInteger** **ObjectPropertyExpression** [ **ClassExpression** ] ')'

​	**ObjectExactCardinality** := 'ObjectExactCardinality' '(' **nonNegativeInteger** **ObjectPropertyExpression** [ **ClassExpression** ] ')'

<u>LPG Diagram</u>:

![class-expression-object-cardinality](images/class-expression-object-cardinality.png)



### 2.4 Data Property Restrictions

#### 2.4.1 Existential and Universal Quantification

An *existential* class expression `DataSomeValuesFrom( DP1 ... DPn DR )` consists of `n` data properties `DPi`, 1 ≤ i ≤ n, and a data range `DR` whose arity *must* be `n`. Such a class expression contains all those individuals that are connected by `DPi` to literals `lti`, 1 ≤ i ≤ n, such that the tuple `( lt1 , ..., ltn )` is in `DR`.

A *universal* class expression `DataAllValuesFrom( DP1 ... DPn DR )` consists of `n` data properties `DPi`, 1 ≤ i ≤ n, and a data range `DR` whose arity *must* be `n`. Such a class expression contains all those individuals that are connected by `DPi` only to literals `lti`, 1 ≤ i ≤ n, such that each tuple `( lt1 , ..., ltn )` is in `DR`.

<u>OWL 2 Notation</u>:

​	**DataSomeValuesFrom** := 'DataSomeValuesFrom' '(' **DataProperty** { **DataProperty** } **DataRange** ')'

​	**DataAllValuesFrom** := 'DataAllValuesFrom' '(' **DataProperty** { **DataProperty** } **DataRange** ')'

<u>LPG Diagram</u>:

![class-expression-data-restrictions](images/class-expression-data-restrictions.png)



#### 2.4.2 Literal Value Restriction

A has-value class expression `DataHasValue( DP lt )` consists of a data property `DP` and a literal `lt`, and it contains all those individuals that are connected by `DP` to `lt`.

<u>OWL 2 Notation</u>:

​	**DataHasValue** := 'DataHasValue' '(' **DataProperty** **Literal** ')'

<u>LPG Diagram</u>:

![axiom-hasvalue-dataproperties](images/axiom-hasvalue-dataproperties.png)



### 2.5 Data Property Cardinality Restrictions

A *minimum cardinality* expression `DataMinCardinality( n DP DR )` consists of a nonnegative integer `n`, a data property `DP`, and a unary data range `DR`, and it contains all those individuals that are connected by `DP` to at least `n` different literals in `DR`.

A *maximum cardinality* expression `DataMaxCardinality( n DP DR )` consists of a nonnegative integer `n`, a data property `DP`, and a unary data range `DR`, and it contains all those individuals that are connected by `DP` to at most `n` different literals in `DR`.

An *exact cardinality* expression `DataExactCardinality( n DP DR )` consists of a nonnegative integer `n`, a data property `DP`, and a unary data range `DR`, and it contains all those individuals that are connected by `DP` to exactly `n` different literals in `DR`.

In all cases, if `DR` is not present, it is taken to be *rdfs:Literal*.

<u>OWL 2 Notation</u>:

​	**DataMinCardinality** := 'DataMinCardinality' '(' **nonNegativeInteger** **DataProperty** [ **DataRange** ] ')'

​	**DataMaxCardinality** := 'DataMaxCardinality' '(' **nonNegativeInteger** **DataProperty** [ **DataRange** ] ')'

​	**DataExactCardinality** := 'DataExactCardinality' '(' **nonNegativeInteger** **DataProperty** [ **DataRange** ] ')'

<u>LPG Diagram</u>:

![class-expression-data-cardinalities](images/class-expression-data-cardinalities.png)



## 3 Axioms

### 3.1 Class Expression Axioms

#### 3.1.1 Subclass

A subclass axiom `SubClassOf( CE1 CE2 )` states that the class expression `CE1` is a subclass of the class expression `CE2`. Roughly speaking, this states that `CE1` is more specific than `CE2`.

<u>OWL 2 Notation</u>:

​	**SubClassOf** := 'SubClassOf' '(' { **Annotation** } **subClassExpression** **superClassExpression** ')'
​	**subClassExpression** := **ClassExpression**
​	**superClassExpression** := **ClassExpression**

<u>LPG Diagram</u>:

![axiom-subclass-of](images/axiom-subclass-of.png)



#### 3.1.2 Equivalent Classes

An equivalent classes axiom `EquivalentClasses( CE1 ... CEn )` states that all of the class expressions `CEi`, 1 ≤ i ≤ n, are semantically equivalent to each other. This axiom allows one to use each `CEi` as a synonym for each `CEj` — that is, in any expression in the ontology containing such an axiom, `CEi` can be replaced with `CEj` without affecting the meaning of the ontology.

<u>OWL 2 Notation</u>:

​	**EquivalentClasses** := 'EquivalentClasses' '(' { **Annotation** } **ClassExpression** **ClassExpression** { **ClassExpression** } ')'

<u>LPG Diagram</u>:

![axiom-equivalent-classes](images/axiom-equivalent-classes.png)



#### 3.1.3 Disjoint Classes

A disjoint classes axiom `DisjointClasses( CE1 ... CEn )` states that all of the class expressions `CEi`, 1 ≤ i ≤ n, are pairwise disjoint; that is, no individual can be at the same time an instance of both `CEi` and `CEj` for i ≠ j. 

<u>OWL 2 Notation</u>:

​	**DisjointClasses** := 'DisjointClasses' '(' { **Annotation** } **ClassExpression** **ClassExpression** { **ClassExpression** } ')'

<u>LPG Diagram</u>:

![axiom-disjoint-classes](images/axiom-disjoint-classes.png)



### 3.2 Object Property Axioms

#### 3.2.1 Object Subproperty

An object sub property axiom `SubObjectPropertyOf( OPE1 OPE2 )`. This axiom states that the object property expression `OPE1` is a subproperty of the object property expression `OPE2` — that is, if an individual `x` is connected by `OPE1` to an individual `y`, then`x` is also connected by `OPE2` to `y`.

<u>OWL 2 Notation</u>:

​	**SubObjectPropertyOf** := 'SubObjectPropertyOf' '(' { **Annotation** } **subObjectPropertyExpression** **superObjectPropertyExpression** ')'
​	**subObjectPropertyExpression** := **ObjectPropertyExpression**
​	**superObjectPropertyExpression** := **ObjectPropertyExpression**

<u>LPG Diagram</u>:

![axiom-subobjectproperty-of](images/axiom-subobjectproperty-of.png)



#### 3.2.2 Equivalent Object Properties

An equivalent object properties axiom `EquivalentObjectProperties( OPE1 ... OPEn )` states that all of the object property expressions `OPEi`, 1 ≤ i ≤ n, are semantically equivalent to each other. This axiom allows one to use each `OPEi` as a synonym for each `OPEj` — that is, in any expression in the ontology containing such an axiom, `OPEi` can be replaced with `OPEj` without affecting the meaning of the ontology.

<u>OWL 2 Notation</u>:

​	**EquivalentObjectProperties** := 'EquivalentObjectProperties' '(' { **Annotation** } **ObjectPropertyExpression** **ObjectPropertyExpression** { **ObjectPropertyExpression** } ')'

<u>LPG Diagram</u>:

![axiom-equivalent-objectproperties](images/axiom-equivalent-objectproperties.png)



#### 3.2.3 Disjoint Object Properties

A disjoint object properties axiom `DisjointObjectProperties( OPE1 ... OPEn )` states that all of the object property expressions `OPEi`, 1 ≤ i ≤ n, are pairwise disjoint; that is, no individual `x` can be connected to an individual `y` by both `OPEi` and `OPEj` for i ≠ j.

<u>OWL 2 Notation</u>:

​	**DisjointObjectProperties** := 'DisjointObjectProperties' '(' { **Annotation** } **ObjectPropertyExpression** **ObjectPropertyExpression** { **ObjectPropertyExpression** } ')'

<u>LPG Diagram</u>:

![axiom-disjoint-objectproperties](images/axiom-disjoint-objectproperties.png)



#### 3.2.4 Inverse Object Properties

An inverse object properties axiom `InverseObjectProperties( OPE1 OPE2 )` states that the object property expression `OPE1` is an inverse of the object property expression `OPE2`. Thus, if an individual `x` is connected by `OPE1` to an individual `y`, then `y` is also connected by `OPE2` to `x`, and vice versa.

<u>OWL 2 Notation</u>:

​	**InverseObjectProperties** := 'InverseObjectProperties' '(' { **Annotation** } **ObjectPropertyExpression** **ObjectPropertyExpression** ')'

<u>LPG Diagram</u>:

![axiom-inverse-objectproperties](images/axiom-inverse-objectproperties.png)



#### 3.2.5 Object Property Domain and Range

An object property *domain* axiom `ObjectPropertyDomain( OPE CE )` states that the domain of the object property expression `OPE` is the class expression `CE` — that is, if an individual `x` is connected by `OPE` with some other individual, then `x` is an instance of `CE`.

An object property *range* axiom `ObjectPropertyRange( OPE CE )` states that the range of the object property expression `OPE` is the class expression `CE` — that is, if some individual is connected by `OPE` with an individual `x`, then `x` is an instance of `CE`. 

<u>OWL 2 Notation</u>:

​	**ObjectPropertyDomain** := 'ObjectPropertyDomain' '(' { **Annotation** } **ObjectPropertyExpression** **ClassExpression** ')'

​	**ObjectPropertyRange** := 'ObjectPropertyRange' '(' { **Annotation** } **ObjectPropertyExpression** **ClassExpression** ')'

<u>LPG Diagram</u>:

![axiom-objectproperty-domainrange](images/axiom-objectproperty-domainrange.png)



#### 3.2.6 Object Property Characteristics

An object property *functionality* axiom `FunctionalObjectProperty( OPE )` states that the object property expression `OPE` is functional — that is, for each individual `x`, there can be at most one distinct individual `y` such that `x` is connected by `OPE` to `y`.

An object property *inverse functionality* axiom `InverseFunctionalObjectProperty( OPE )` states that the object property expression `OPE` is inverse-functional — that is, for each individual `x`, there can be at most one individual `y` such that `y` is connected by `OPE` with `x`.

An object property *reflexivity* axiom `ReflexiveObjectProperty( OPE )` states that the object property expression `OPE` is reflexive — that is, each individual is connected by `OPE` to itself.

An object property *irreflexivity* axiom `IrreflexiveObjectProperty( OPE )` states that the object property expression `OPE` is irreflexive — that is, no individual is connected by `OPE` to itself.

An object property *symmetry* axiom `SymmetricObjectProperty( OPE )` states that the object property expression `OPE` is symmetric — that is, if an individual `x` is connected by `OPE` to an individual `y`, then `y` is also connected by `OPE` to `x`.

An object property *asymmetry* axiom `AsymmetricObjectProperty( OPE )` states that the object property expression `OPE` is asymmetric — that is, if an individual `x` is connected by `OPE` to an individual `y`, then `y` cannot be connected by `OPE` to `x`.

An object property *transitivity* axiom `TransitiveObjectProperty( OPE )` states that the object property expression `OPE` is transitive — that is, if an individual `x` is connected by `OPE` to an individual `y` that is connected by `OPE` to an individual `z`, then `x` is also connected by `OPE` to `z`.

<u>OWL 2 Notation</u>:

​	**FunctionalObjectProperty** := 'FunctionalObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

​	**InverseFunctionalObjectProperty** := 'InverseFunctionalObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

​	**ReflexiveObjectProperty** := 'ReflexiveObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'	

​	**IrreflexiveObjectProperty** := 'IrreflexiveObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

​	**SymmetricObjectProperty** := 'SymmetricObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

​	**AsymmetricObjectProperty** := 'AsymmetricObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

​	**TransitiveObjectProperty** := 'TransitiveObjectProperty' '(' { **Annotation** } **ObjectPropertyExpression** ')'

<u>LPG Diagram</u>:

<img src="images/axiom-objectproperty-characteristics.png" alt="axiom-objectproperty-characteristics" width="500px" />



### 3.3 Data Property Axioms

#### 3.3.1 Data Subproperty

A data subproperty axiom `SubDataPropertyOf( DP1 DP2 )` states that the data property `DP1` is a subproperty of the data property `DP2` — that is, if an individual `x` is connected by `DP1` to a literal `y`, then `x` is connected by `DP2` to `y` as well.

<u>OWL 2 Notation</u>:

​	**SubDataPropertyOf** := 'SubDataPropertyOf' '(' { **Annotation** } **subDataPropertyExpression** **superDataPropertyExpression** ')'
​	**subDataPropertyExpression** := **DataProperty**
​	**superDataPropertyExpression** := **DataProperty**

<u>LPG Diagram</u>:

![axiom-subdataproperty-of](images/axiom-subdataproperty-of.png)



#### 3.3.2 Equivalent Data Properties

An equivalent data properties axiom `EquivalentDataProperties( DP1 ... DPn )` states that all the data property `DPi`, 1 ≤ i ≤ n, are semantically equivalent to each other. This axiom allows one to use each `DPi` as a synonym for each `DPj` — that is, in any expression in the ontology containing such an axiom, `DPi` can be replaced with `DPj` without affecting the meaning of the ontology. 

<u>OWL 2 Notation</u>:

​	**EquivalentDataProperties** := 'EquivalentDataProperties' '(' { **Annotation** } **DataProperty** **DataProperty** { **DataProperty** } ')'

<u>LPG Diagram</u>:

![axiom-equivalent-dataproperties](images/axiom-equivalent-dataproperties.png)



#### 3.3.3 Disjoint Data Properties

A disjoint data properties axiom `DisjointDataProperties( DP1 ... DPn )` states that all of the data property `DPi`, 1 ≤ i ≤ n, are pairwise disjoint; that is, no individual `x` can be connected to a literal `y` by both `DPi` and `DPj` for i ≠ j.

<u>OWL 2 Notation</u>:

​	**DisjointDataProperties** := 'DisjointDataProperties' '(' { **Annotation** } **DataProperty** **DataProperty** { **DataProperty** } ')'

<u>LPG Diagram</u>:

![axiom-disjoint-dataproperties](images/axiom-disjoint-dataproperties.png)



#### 3.3.4 Data Property Domain

A data property domain axiom `DataPropertyDomain( DP CE )` states that the domain of the data property  `DP` is the class expression `CE` — that is, if an individual `x` is connected by `DP` with some literal, then `x` is an instance of `CE`.

<u>OWL 2 Notation</u>:

​	**DataPropertyDomain** := 'DataPropertyDomain' '(' { **Annotation** } **DataProperty** **ClassExpression** ')'

<u>LPG Diagram</u>:

![axiom-dataproperty-domain](images/axiom-dataproperty-domain.png)



#### 3.3.5 Data Property Range

A data property range axiom `DataPropertyRange( DP DR )` states that the range of the data property `DP` is the data range `DR` — that is, if some individual is connected by `DP` with a literal `x`, then `x` is in `DR`. The arity of `DR` *must* be one. 

<u>OWL 2 Notation</u>:

​	**DataPropertyRange** := 'DataPropertyRange' '(' { **Annotation** } **DataProperty** **DataRange** ')'

<u>LPG Diagram</u>:

![axiom-dataproperty-range](images/axiom-dataproperty-range.png)



#### 3.3.6 Data Property Characteristics

A data property functionality axiom `FunctionalDataProperty( DP )` states that the data property `DP` is functional — that is, for each individual `x`, there can be at most one distinct literal `y` such that `x` is connected by `DP` with `y`.

<u>OWL 2 Notation</u>:

​	**FunctionalDataProperty** := 'FunctionalDataProperty' '(' { **Annotation** } **DataProperty** ')'

<u>LPG Diagram</u>:

<img src="images/axiom-dataproperty-characteristics.png" alt="axiom-dataproperty-characteristics" width="500px" />



### 3.4 Assertions

#### 3.4.1 Class Assertions

A class assertion `ClassAssertion( CE a )` states that the individual `a` is an instance of the class expression `CE`.

<u>OWL 2 Notation</u>:

​	**ClassAssertion** := 'ClassAssertion' '(' { **Annotation** } **ClassExpression** **Individual** ')'

<u>LPG Diagram</u>:

![axiom-class-assertion](images/axiom-class-assertion.png)



#### 3.4.2 Object Property Assertions

A positive object property assertion `ObjectPropertyAssertion( OPE a1 a2 )` states that the individual `a1` is connected by the object property expression `OPE` to the individual `a2`.

A negative object property assertion `NegativeObjectPropertyAssertion( OPE a1 a2 )` states that the individual `a1` is not connected by the object property expression `OPE` to the individual `a2`.

<u>OWL 2 Notation</u>:

​	**ObjectPropertyAssertion** := 'ObjectPropertyAssertion' '(' { **Annotation** } **ObjectPropertyExpression** **sourceIndividual** **targetIndividual** ')'

​	**NegativeObjectPropertyAssertion** := 'NegativeObjectPropertyAssertion' '(' { **Annotation** } **ObjectPropertyExpression** **sourceIndividual** **targetIndividual** ')'

<u>LPG Diagram</u>:

![axiom-objectproperty-assertion](images/axiom-objectproperty-assertion.png)



#### 3.4.3 Data Property Assertions

A positive data property assertion `DataPropertyAssertion( DP a lt )` states that the individual a is connected by the data property `DP` to the literal `lt`.

A negative data property assertion `NegativeDataPropertyAssertion( DP a lt )` states that the individual a is not connected by the data property `DP` to the literal `lt`.

<u>OWL 2 Notation</u>:

​	**DataPropertyAssertion** := 'DataPropertyAssertion' '(' { **Annotation** } **DataPropertyExpression** **sourceIndividual** **targetValue** ')'

​	**NegativeDataPropertyAssertion** := 'NegativeDataPropertyAssertion' '(' { **Annotation** } **DataPropertyExpression** **sourceIndividual** **targetValue** ')'	

<u>LPG Diagram</u>:

![axiom-dataproperty-assertion](images/axiom-dataproperty-assertion.png)



#### 3.4.4 Individual Equality

An individual equality axiom `SameIndividual( a1 ... an )` states that all of the individuals `ai`, 1 ≤ i ≤ n, are equal to each other. This axiom allows one to use each `ai` as a synonym for each `aj` — that is, in any expression in the ontology containing such an axiom, `ai` can be replaced with `aj` without affecting the meaning of the ontology.

<u>OWL 2 Notation</u>:

​	**SameIndividual** := 'SameIndividual' '(' { **Annotation** } **Individual** **Individual** { **Individual** } ')'

<u>LPG Diagram</u>:

![axiom-same-individual](images/axiom-same-individual.png)

#### 3.4.5 Individual Inequality

An individual inequality axiom `DifferentIndividuals( a1 ... an )` states that all of the individuals `ai`, 1 ≤ i ≤ n, are different from each other; that is, no individuals `ai` and `aj` with i ≠ j can be derived to be equal. This axiom can be used to axiomatize the *unique name assumption* — the assumption that all different individual names denote different individuals.

<u>OWL 2 Notation</u>:

​	**DifferentIndividuals** := 'DifferentIndividuals' '(' { **Annotation** } **Individual** **Individual** { **Individual** } ')'

<u>LPG Diagram</u>:

![axiom-different-individuals](images/axiom-different-individuals.png)



## 4 Annotations

### 4.1 Annotation of Ontologies, Axioms, and other Annotations

Ontologies, axioms, and annotations themselves can be annotated using annotations. Such annotations consist of an annotation property and an annotation value, where the latter can be IRIs or literals.

<u>OWL 2 Notation</u>:

​	**Annotation** := 'Annotation' '(' { **Annotation** } **AnnotationProperty** **AnnotationValue** ')'
​	**AnnotationValue** :=  **Literal** | **IRI**

<u>LPG Diagram</u>:

![annotation](images/annotation.png)

### 4.2 Annotation Axioms

#### 4.2.1 Annotation Assertions

An annotation assertion `AnnotationAssertion( AP as av )` states that the annotation subject as — an IRI  — is annotated with the annotation property `AP` and the annotation value `av`.

<u>OWL 2 Notation</u>:

​	**AnnotationAssertion** := 'AnnotationAssertion' '(' { **Annotation** } **AnnotationProperty** **AnnotationSubject** **AnnotationValue** ')'
​	**AnnotationValue** :=  **Literal** | **IRI**

<u>LPG Diagram</u>:

![axiom-annotation-assertion](images/axiom-annotation-assertion.png)

#### 4.2.2 Annotation Subproperty

An annotation subproperty axiom `SubAnnotationPropertyOf( AP1 AP2 )` states that the annotation property `AP1` is a subproperty of the annotation property `AP2`.

<u>OWL 2 Notation</u>:

​	**SubAnnotationPropertyOf** := 'SubAnnotationPropertyOf' '(' { **Annotation** } **subAnnotationProperty** **superAnnotationProperty** ')'
​	**subAnnotationProperty** := **AnnotationProperty**
​	**superAnnotationProperty** := **AnnotationProperty**

<u>LPG Diagram</u>:

![axiom-subannotationproperty-of](images/axiom-subannotationproperty-of.png)





## 5 Change History

![change-history](images/change-history.png)



