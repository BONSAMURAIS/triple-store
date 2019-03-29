# Competency Questions and corresponding Sparql


---
__01 Is the flow $x$ a determining product for the activity $y$__

Since flow here is a product, I translated it into flow-object. Also, I reread and the flow $x$ and the activity $y$ seems to be a constant. In which case this is a true or false statement.

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?z WHERE {
    bind ( exists{
        x b:outputOf y;
        b:determiningFlow y
    } as ?z)
}
```

---
 

__02 Is input flow $x$ required for activity $y$?__
 
```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?z WHERE {
    bind (exists{
        x b:inputOf y;
        b:determiningFlow y
    } as ?z)
}
```

---
  
__03 What is the amount of flow $x$ emitted as output during the time period $y$ ?__
 
I think $y$ here would be expressed as a lower $low$ bound and an upper $up$ bound. What do we do if we don’t have the actual data but we have to aggregate data in the database?

```sparql
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT ?x WHERE {
    ?x a b:flow;
       b:flowOf ?z;
       b:hasTemporalExtent ?y.
    ?z a b:flow-object.
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

```
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
