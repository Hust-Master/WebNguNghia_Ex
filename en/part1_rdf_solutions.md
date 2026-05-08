# Part 1: RDF / RDFS — Solutions (Exercises 1–7)

---

## Exercise 1 — Natural Language Statements as RDF Triples

> Represent each statement using at least 3 triples.

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ex:   <http://example.org/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .

### Statement 1: SOICT is located at No. 1, Dai Co Viet, Hai Ba Trung, Hanoi
ex:SOICT  rdf:type          schema:Organization ;
          schema:name        "SOICT" ;
          schema:address     "No. 1, Dai Co Viet, Hai Ba Trung, Hanoi" .

### Statement 2: Website https://soict.hust.edu.vn/ is the homepage of SOICT
<https://soict.hust.edu.vn/>
          rdf:type           foaf:Document ;
          rdfs:label         "SOICT Homepage" ;
          foaf:homepage      ex:SOICT .
# (Alternatively: ex:SOICT foaf:homepage <https://soict.hust.edu.vn/> .)

### Statement 3: URI1 is a course on Semantic Web, taught by Lecturer Hung in room B1-404
ex:URI1   rdf:type           ex:Course ;
          ex:courseName      "Semantic Web" ;
          ex:taughtBy        ex:Hung ;
          ex:room            "B1-404" .

ex:Hung   rdf:type           ex:Lecturer ;
          foaf:name          "Hung" .
```

---

## Exercise 2 — Dublin Core (Documents, Authors, Reification)

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix dc:   <http://purl.org/dc/elements/1.1/> .
@prefix ex:   <http://example.org/> .

### Document 1 created by Minh
ex:doc1   dc:creator   "Minh" .

### Document 2 and Document 3 created by the same author (unknown) → blank node
_:author  dc:creator   _:author .   # (defining the blank node)
ex:doc2   dc:creator   _:author .
ex:doc3   dc:creator   _:author .

### Document 3 states that Document 1 is published by W3C → Reification
ex:statement1  rdf:type       rdf:Statement ;
               rdf:subject    ex:doc1 ;
               rdf:predicate  dc:publisher ;
               rdf:object     "W3C" .

ex:doc3        ex:states      ex:statement1 .
```

> [!NOTE]
> The third statement requires **RDF Reification** because Document 3 *talks about* another triple (Document 1 is published by W3C). We create an `rdf:Statement` resource to represent the reified triple.

---

## Exercise 3 — Classes, Properties, Domain & Range

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ex:   <http://example.org/> .

### URI1 and URI2 are classes
ex:URI1   rdf:type   rdfs:Class .
ex:URI2   rdf:type   rdfs:Class .

### URI3 is a property
ex:URI3   rdf:type   rdf:Property .

### URI4 is an instance of URI1
ex:URI4   rdf:type   ex:URI1 .

### URI5 and URI6 are instances of URI2
ex:URI5   rdf:type   ex:URI2 .
ex:URI6   rdf:type   ex:URI2 .

### URI3 has domain URI1 and range URI2
ex:URI3   rdfs:domain   ex:URI1 ;
          rdfs:range    ex:URI2 .
```

```mermaid
graph LR
    URI1["ex:URI1"] -->|rdf:type| C1["rdfs:Class"]
    URI2["ex:URI2"] -->|rdf:type| C1
    URI3["ex:URI3"] -->|rdf:type| P1["rdf:Property"]
    URI4["ex:URI4"] -->|rdf:type| URI1
    URI5["ex:URI5"] -->|rdf:type| URI2
    URI6["ex:URI6"] -->|rdf:type| URI2
    URI3 -->|rdfs:domain| URI1
    URI3 -->|rdfs:range| URI2
```

---

## Exercise 4 — Graph to Turtle (Saint, Archangel, Georges, Michael)

**Original graph:**

![Exercise 4 graph from PDF](./../page2_img1.png)

**Turtle representation:**

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex:   <http://example.org/> .

### Georges
ex:Georges  rdf:type     ex:Saint ;
            foaf:name    "Georges" ;
            ex:defeated  [ rdf:type ex:Dragon ] .

### Michael
ex:Michael  rdf:type     ex:Saint ;
            rdf:type     ex:Archangel ;
            foaf:name    "Michael" ;
            ex:defeated  ex:Satan .

### Satan
ex:Satan    foaf:name    "Satan" .
```

> [!TIP]
> - Georges defeated **a dragon** (an anonymous/blank node of type Dragon).
> - Michael is both a `Saint` and an `Archangel`.
> - Satan is a named resource with only a `foaf:name` property.

**Mermaid diagram:**

```mermaid
graph LR
    Georges -->|rdf:type| Saint
    Georges -->|"foaf:name"| G_name["'Georges'"]
    Georges -->|defeated| bDragon["_:dragon"]
    bDragon -->|rdf:type| Dragon

    Michael -->|rdf:type| Saint
    Michael -->|rdf:type| Archangel
    Michael -->|"foaf:name"| M_name["'Michael'"]
    Michael -->|defeated| Satan

    Satan -->|"foaf:name"| S_name["'Satan'"]
```

---

## Exercise 5 — Professor, Assistant, Teacher, Course

> Create a graph and represent as triples.

```turtle
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ex:   <http://example.org/> .

### Classes
ex:Teacher     rdf:type   rdfs:Class .
ex:Professor   rdf:type   rdfs:Class ;
               rdfs:subClassOf  ex:Teacher .
ex:Assistant   rdf:type   rdfs:Class ;
               rdfs:subClassOf  ex:Teacher .
ex:Course      rdf:type   rdfs:Class .

### Properties
ex:name        rdf:type      rdf:Property ;
               rdfs:domain   ex:Teacher ;
               rdfs:range    rdfs:Literal .

ex:courseID     rdf:type      rdf:Property ;
               rdfs:domain   ex:Course ;
               rdfs:range    rdfs:Literal .

ex:teaches     rdf:type      rdf:Property ;
               rdfs:domain   ex:Professor ;
               rdfs:range    ex:Course .

ex:supports    rdf:type      rdf:Property ;
               rdfs:domain   ex:Assistant ;
               rdfs:range    ex:Course .
```

```mermaid
graph BT
    Professor -->|rdfs:subClassOf| Teacher
    Assistant -->|rdfs:subClassOf| Teacher
    name_prop["ex:name"] -->|rdfs:domain| Teacher
    courseID_prop["ex:courseID"] -->|rdfs:domain| Course
    teaches_prop["ex:teaches"] -->|rdfs:domain| Professor
    teaches_prop -->|rdfs:range| Course
    supports_prop["ex:supports"] -->|rdfs:domain| Assistant
    supports_prop -->|rdfs:range| Course
```

---

## Exercise 6 — Build a Graph from Given Triples

**Given Turtle:**
```turtle
PREFIX :     <http://example.org/>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

:Minh a :Teacher ;
      :teaches "Semantic Web" , "Data Integration" ;
      foaf:member :SOICT .

[] foaf:knows :Minh ; foaf:member [ a :Company ] .
```

**Analysis:**
- `:Minh` is a `:Teacher`, teaches two courses (literals), and is a `foaf:member` of `:SOICT`.
- An **anonymous person** (`[]` = blank node `_:b1`) knows `:Minh` and is a member of an **anonymous company** (`[]` = blank node `_:b2`, which is of type `:Company`).

```mermaid
graph LR
    Minh[":Minh"] -->|rdf:type| Teacher[":Teacher"]
    Minh -->|":teaches"| SW["'Semantic Web'"]
    Minh -->|":teaches"| DI["'Data Integration'"]
    Minh -->|"foaf:member"| SOICT[":SOICT"]

    B1["_:b1"] -->|"foaf:knows"| Minh
    B1 -->|"foaf:member"| B2["_:b2"]
    B2 -->|rdf:type| Company[":Company"]
```

---

## Exercise 7 — Library Description

> Describe a library with book, author, publisher, year, etc.

```turtle
@prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs:  <http://www.w3.org/2000/01/rdf-schema#> .
@prefix dc:    <http://purl.org/dc/elements/1.1/> .
@prefix foaf:  <http://xmlns.com/foaf/0.1/> .
@prefix xsd:   <http://www.w3.org/2001/XMLSchema#> .
@prefix ex:    <http://example.org/library/> .

### Classes
ex:Book        rdf:type   rdfs:Class .
ex:Author      rdf:type   rdfs:Class .
ex:Publisher    rdf:type   rdfs:Class .
ex:Library     rdf:type   rdfs:Class .

### Properties
ex:hasBook     rdf:type      rdf:Property ;
               rdfs:domain   ex:Library ;
               rdfs:range    ex:Book .

ex:publishedBy rdf:type      rdf:Property ;
               rdfs:domain   ex:Book ;
               rdfs:range    ex:Publisher .

ex:year        rdf:type      rdf:Property ;
               rdfs:domain   ex:Book ;
               rdfs:range    xsd:integer .

### Instance data
ex:CentralLibrary  rdf:type     ex:Library ;
                   rdfs:label   "Central Library" ;
                   ex:hasBook   ex:Book1 , ex:Book2 .

ex:Book1   rdf:type        ex:Book ;
           dc:title        "Semantic Web Primer" ;
           dc:creator      ex:Author1 ;
           ex:publishedBy  ex:Springer ;
           ex:year         2004 .

ex:Book2   rdf:type        ex:Book ;
           dc:title        "Learning SPARQL" ;
           dc:creator      ex:Author2 ;
           ex:publishedBy  ex:OReilly ;
           ex:year         2013 .

ex:Author1    rdf:type   ex:Author ;
              foaf:name  "Grigoris Antoniou" .

ex:Author2    rdf:type   ex:Author ;
              foaf:name  "Bob DuCharme" .

ex:Springer   rdf:type   ex:Publisher ;
              foaf:name  "Springer" .

ex:OReilly    rdf:type   ex:Publisher ;
              foaf:name  "O'Reilly Media" .
```
