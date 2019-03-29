# Competency Questions and corresponding Sparql


---
__01 Is the flow $x$ a determining product for the activity $y$__

Since flow here is a product, I translated it into flow-object. Also, I reread and the flow $x$ and the activity $y$ seems to be a constant. In which case this is a true or false statement.

Is the flow of steel a determining product for steel production?
Here x is "steel" and y is "steel production".

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?z WHERE {
    bind ( exists{ #exists checks if the query returns any result
        ?xFlow b:outputOf ?yActivity; #?xFlow refers to the actual activity since 'x' would be the label used.
               b:determiningFlow ?yActivity; # y is the activity which is easy enough to identify using regular language
               b:objectType ?xObject; #x here should be translated to the flow from above, though I think in terms of questions, x would be a flow object, so it's referenced as one here.
        ?xObject rdfs:label "steel". #last two lines are for the labels which I presume are used in the question
        ?yActivity rdfs:label "steel production" 
    } as ?z) #output of exists returned as ?z
}
```

---
 

__02 Is input flow $x$ required for activity $y$?__
 
This is the same query as above except that the output has been changed to input. To make the query reasonable, I changed the values of x and y.
 
Is the flow of steel a determining product for steel production?
Here x is "iron" and y is "steel production".
```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?z WHERE {
    bind ( exists{ #exists checks if the query returns any result
        ?xFlow b:outputOf ?yActivity; #?xFlow refers to the actual activity since 'x' would be the label used.
               b:determiningFlow ?yActivity; # y is the activity which is easy enough to identify using regular language
               b:objectType ?xObject; #x here should be translated to the flow from above, though I think in terms of questions, x would be a flow object, so it's referenced as one here.
        ?xObject rdfs:label "iron". #last two lines are for the labels which I presume are used in the question
        ?yActivity rdfs:label "steel production" 
    } as ?z) #output of exists returned as ?z
}
```

---
  
__03 What is the amount of flow $x$ emitted as output during the time period $y$ ?__
 
I think $y$ here would be expressed as a lower $low$ bound and an upper $up$ bound. What do we do if we don’t have the actual data but we have to aggregate data in the database?

What is the amount of CO2 emitted as output on 2011?
Here x is CO2 and y is between January 1, 2011 0:00:00 and December 31, 2011 11:59:59

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT ?z ? WHERE {
    ?xFlow a b:flow; 
       b:flowOf ?xObject;
       b:hasTemporalExtent ?y.
    ?xObject a b:flow-object;
       rdfs:label "CO2".
    ?y time:hasBeginning ?low;
       time:hasEnd ?up;
       a time:ProperInterval
}
```

---

__04 What is the location of the agent performing the activity $y$?__

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?a ?loc WHERE {
    ?a b:performs y;
       a agent;
       b:location ?loc
}
```

---

__05 What other agents performing the same type of activity of agent $z$ are present in the same location $w$?__
 
The other agents I called $a$ and here z is a constant instead of a variable in sparql so I’ll leave it as a z. I also removed z from the result set since this is a given.

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?a WHERE {
    z a b:agent;
      b:performs ?w;
      b:location ?l.
    ?y b:performs ?w;
       b:location ?l.
    MINUS { z }
}
```

---
 
__06 How are the flow objects quantified? / Which units of measure are used?__

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>

SELECT ?x ?unit WHERE {
    ?x a b:flowObject.
    ?z a b:flow;
       b:objectType ?x;
       om:hasUnit ?unit
}
```

---

__07 Does the input and output of flow objects follow mass balance?__
 
I’m assuming this flow objects mass balance is from flows. Also, this only works for same unit scales.

```
TBD
```

---

__08 Further, balance checks on ALL flows of a specific flow-object across all activities, requires a more complete database.__

Similar question to above except we're looking into all flows into all activities.

---
 
__09 Is output flow required for activity of type 'stock'?__
 
Are we still handling stocks? Not sure how to query this as stocks was mentioned to be outside the ontology.
 
---

__10 What is the direct input flow of flow-object F to activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?__
 
I think flow-property here is redundant since we already standardized the units. So I’m removing it from the query itself. Still need to consider convertion. Don't know how to ask the about the macro-economic scenario though.

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT sum(?v) WHERE {
    ?i a flow;
       om:hasValue ?v;
       b:objectType ?f;
       b:inputOf ?a;
       om:hasUnit ?u.
    ?a a activity;
       b:location l;
       b:hasTemporalExtent ?t.
    ?t a time:ProperInterval;
       time:hasBeginning low;
       time:hasEnd up
}
GROUP BY ?u
```

---

__11 What is the direct output flow of flow-object F from activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?__
 
Same comments as above. Also similar query with only the input changed to the output.

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT sum(?v) WHERE {
    ?i a flow;
       om:hasValue ?v;
       b:objectType ?f;
       b:outputOf ?a;
       om:hasUnit ?u.
    ?a a activity;
       b:location l;
       b:hasTemporalExtent ?t.
    ?t a time:ProperInterval;
       time:hasBeginning low;
       time:hasEnd up
}
GROUP BY ?u
```

---

__12 What is the determining flow of activity A in location L in the time period T under macro-economic scenario S?__

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT ?f WHERE {
    ?f b:determiningFlow ?a;
       a b:flow.
    ?a a b:activity;
       b:location l;
       b:hasTemporalExtent ?t.
    ?t a time:ProperInterval;
       time:hasBeginning low;
       time:hasEnd up
}
```

---

__13 What is the difference between input flows and output flows of flow-property P for activity A in location L in the time period T under macro-economic scenario S?__

---
 
__14 What is the additional input flow of flow-object F to activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?__

---
 
__15 What is the additional output flow of flow-object F from activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?__
 
---

__16 What is the direct output flow of flow-object F from activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?__

---
 
__17 What is the additional output flow of flow-object F from activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?__

--- 

Other queries
==========

Conversion (simple multiplication)
-----

Converts simple units of measure to their SI counterparts. (Only works for length, time (not 1/time), mass, and equivalent basic units - though it might work on energy. Doesn't work for velocity, rates, power density and similar with complicated conversions).

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX om2: <http://www.ontology-of-units-of-measure.org/resource/om-2/>

SELECT ((xsd:float(?v)*xsd:float(?f)) as ?result)
WHERE {
  ?s a om2:Measure;
     om2:hasUnit ?u;
     om2:hasNumericalValue ?v.
  ?u om2:hasFactor ?f
}
LIMIT 1000
```

Property Path -> Activity Lists
----

Property path assumes the same pathway across recursive properties. This is the example for tracing "Coal" and works with the "simple_steel_transport" in the <https://github.com/BONSAMURAIS/BONSAI-ontology-RDF-framework/blob/master/examples/simple_steel_transport.rdf>.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
prefix bont: <http://ontology.bonsai.uno/core#>

SELECT ?alabel
WHERE {
  ?a1 (^bont:outputOf/bont:inputOf)* ?a2.
  #from activity ?a1, it checks all flows that are the output of an activity which is also an 
  #input to another activity, the other activity is stored in ?a2. It then checks ?a2 for
  #outputs that are also inputs of other activities. This works by the * operating on both the
  #properties ^bont:outputOf and bont:inputOf in order.
  #^bont:outputOf is the inverse property of bont:outputOf (inversion of domain and range)
  ?flow2 a bont:Flow; #This set of lines is to get the flow-object of the output of ?a1
         bont:objectType ?outputObject;
         bont:outputOf ?a1.
  #The previous set was used to restrict the flow-object being monitored to the flow-object
  #that has a label "Coal". This is done in the next set of lines.
  ?outputObject a bont:FlowObject;
                rdfs:label "Coal".
  ?agent bont:performs ?a2; #Checks the labels of the agents that perform the activity ?a2, this includes ?a1
         rdfs:label ?alabel.
}
LIMIT 500
```

