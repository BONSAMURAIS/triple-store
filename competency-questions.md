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

    # exists checks if the query returns any result
    bind ( exists{ 
    
    # ?xFlow refers to the actual activity since 'x' would be the label used.
    # y is the activity which is easy enough to identify using regular language
    # x here should be translated to the flow from above, though I think in terms
    # of questions, x would be a flow object, so it's referenced as one here.
        ?xFlow b:outputOf ?yActivity; 
               ^b:determiningFlow ?yActivity; #made a mistake in domain and ranges so put an inverse ^ here.
               b:objectType ?xObject;
               
    #last two lines are for the labels which are presumed to used in the question
        ?xObject rdfs:label "steel". 
        ?yActivity rdfs:label "steel production"
        
    #output of exists returned as ?z
    } as ?z) 
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

    # exists checks if the query returns any result
    bind ( exists{ 
    
    # ?xFlow refers to the actual activity since 'x' would be the label used.
    # y is the activity which is easy enough to identify using regular language
    # x here should be translated to the flow from above, though I think in terms
    # of questions, x would be a flow object, so it's referenced as one here.
        ?xFlow b:outputOf ?yActivity; 
               ^b:determiningFlow ?yActivity; #made a mistake in domain and ranges so put an inverse ^ here.
               b:objectType ?xObject;
               
    #last two lines are for the labels which are presumed to used in the question
        ?xObject rdfs:label "iron". 
        ?yActivity rdfs:label "steel production"
        
    #output of exists returned as ?z
    } as ?z) 
}
```

---
  
__03 What is the amount of flow $x$ emitted as output during the time period $y$ ?__
 
I think $y$ here would be expressed as a lower $low$ bound and an upper $up$ bound. What do we do if we don’t have the actual data but we have to aggregate data in the database?

What is the amount of CO2 emitted as output on 2011?

Here x is CO2 and y is between January 1, 2011 0:00:00 and December 31, 2011 11:59:59, but we did mention that we have 2011 in our data. So in this case, I assumed that this 2011 is a properly defined time:ProperInterval that is bound as I mentioned earlier. Also, I'll put in a version wherein the proper interval isn't defined as another query.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT SUM(?n) ?u WHERE {

    # This set of statements identifies the flow-object that is used for CO2.
    ?xObject a b:flow-object;
       rdfs:label "CO2".

    # ?xFlow identifies all the flows that have the ?xObject above, "CO2," as an
    # b:objectType. Since we want an amount, we want to add any units, ?u, that we
    # can as well as the value of the flow, ?n. Those that have the same units are
    # then added together.
    ?xFlow a b:flow; 
       b:objectType ?xObject;
       b:hasUnit ?u;
       b:hasNumericValue ?n;
       b:hasTemporalExtent <http://rdf.bonsai.uno/time/2011>;
       b:outputOf ?a
}

# group by is to ensure only those that have the same units are added.
GROUP BY ?u
```

limits defined

What is the amount of CO2 emitted as output on June 2011?

This should probably be modified to include a range of dates instead of something that fits the interval exactly. So if I'm looking for data on June 2011. I can still pull data that was generated from June 15 - June 20, 2011. Not sure though if the ranges intersect.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT SUM(?n) ?u WHERE {
    
    ?xObject a b:FlowObject;
       rdfs:label "CO2".
       
    ?xFlow a b:Flow; 
       b:objectType ?xObject;
       b:hasUnit ?u;
       b:hasNumericValue ?n;
       b:hasTemporalExtent ?y;
       b:outputOf ?a.
    
    # the only difference between this query and the previous is the use of
    # the range in time instead of a predefined time as before.
    ?y time:hasBeginning ?beg;
       time:hasEnd ?end.
       
    ?beg time:inXSDDate "2011-06-01"^^xsd:date.
    
    ?end time:inXSDDate "2011-06-30"^^xsd:date
}
GROUP BY ?u
```

---

__04 What is the location of the agent performing the activity $y$?__

What is the location of "Coal Mining Company" performing the Coal Mining.


```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?loc WHERE {

    # here we look for an agent which we know the name of and it performs a certain activity.
    # we then search for where the location of this agent.
    ?a b:performs ?y;
       a agent;
       b:location ?loc;
       rdfs:label "Coal Mining Company".
    ?y rdfs:label "Coal Mining"
}
```

What is the location of the companies performing the Coal Mining?

Here the query is more generic question when we only know the activity.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?alabel ?loc WHERE {

    # here we look for all agents that performs Coal Mining and output they're
    # names, ?alabel, and locations, ?loc.
    ?a b:performs ?y;
       a agent;
       b:location ?loc;
       rdfs:label ?alabel.
    ?y rdfs:label "Coal Mining"
}
```

---

__05 What other agents performing the same type of activity of agent $z$ are present in the same location $w$?__
 
The other agents I called $a$ and here z is a constant instead of a variable in sparql so I’ll leave it as a z. I also removed z from the result set since this is a given.

Who/What else are performing "Electricity Generation" that is in the same location as "Generic Power Corporation".

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>

SELECT ?ylabel WHERE {

    # this set of lines look for the agent that is named Generic Power Corporation. It
    # also gets the location, ?l, of the agent, as well as the activity, ?w. These are
    # used to get similar agents.
    ?z a b:agent;
      b:performs ?w;
      b:location ?l;
      rdfs:label "Generic Power Corporation".
    
    # Here, we ensure that the activity that the agent ?z is involved in is "Electricity
    # generation.
    ?w rdfs:label "Electricity Generation".
    
    # Then we get the labels, ?ylabel, of all agents that are similar to agent ?z.
    ?y b:performs ?w;
       b:location ?l;
       rdfs:label ?ylabel;
    
    # We have to remove the label of the Generic Power Corporation since it also satisfies
    # the conditions for ?y.
    MINUS { "Generic Power Corporation" }
}
```

---
 
__06 How are the flow objects quantified? / Which units of measure are used?__

How are all the products quantified?

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>

SELECT ?xlabel ?unitlabel WHERE {

    # We first get labels of all flow-objects.
    ?x a b:FlowObject;
       rdfs:label ?xlabel.
       
    # We then get all the flows that are of the flow-objects we generated.
    # We then check what units are used in these flows
    ?z a b:Flow;
       b:objectType ?x;
       om:hasUnit ?unit.
       
    # We check the labels of these units as these make more sense semantically.
    ?unit rdfs:label ?unitlabel
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
 
I think flow-property here is redundant since we already standardized the units. So I’m removing it from the query itself. Still need to consider convertion. Don't know how to ask the about the macro-economic scenario though. Since we're looking for direct input flow here, I'm assuming it's a rate. I may be wrong. I'm also seeing a problem with the current ontology with regards to activities. But perhaps similar activities can be labeled the same just performed by different agents.

What is the direct input flow of "Natural Gas" to "Heat Production" in "Location A" on 2011?

I used 2011 since we already have that. I put Location A as I don't want to offend anyone by being biased on location. :P // bad documentation.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT sum(?v) ?u WHERE {

    # We look for the flow-object, ?f, labeled "Natural Gas"
    ?f rdfs:label "Natural Gas"

    # The look for the flows that use the flow-object above.
    # This includes checking which activities, ?a it is an input of.
    ?i a flow;
       om:hasValue ?v;
       b:objectType ?f;
       b:inputOf ?a;
       om:hasUnit ?u.
       
    # We check that the activity is "Heat Production", what its location, ?l, is,
    # and when it happened - 2011 in this case.
    ?a a activity;
       b:location ?l;
       b:hasTemporalExtent <http://rdf.bonsai.uno/time/2011>;
       rdfs:label "Heat Production"
    
    # We ensure the activity happens in "Location A"
    ?l rdfs:label "Location A"
}

# We sum all flows depending on the units
GROUP BY ?u
```

---

__11 What is the direct output flow of flow-object F from activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?__
 
Same comments as above. Also similar query with only the input changed to the output.

What is the direct output flow of "CO2" to "Heat Production" in "Location A" on 2011?

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/resource/om-2/>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT sum(?v) ?u WHERE {

    # We look for the flow-object, ?f, labeled "CO2"
    ?f rdfs:label "CO2"

    # The look for the flows that use the flow-object above.
    # This includes checking which activities, ?a it is an input of.
    ?i a flow;
       om:hasValue ?v;
       b:objectType ?f;
       b:outputOf ?a;
       om:hasUnit ?u.
       
    # We check that the activity is "Heat Production", what its location, ?l, is,
    # and when it happened - 2011 in this case.
    ?a a activity;
       b:location ?l;
       b:hasTemporalExtent <http://rdf.bonsai.uno/time/2011>;
       rdfs:label "Heat Production"
    
    # We ensure the activity happens in "Location A"
    ?l rdfs:label "Location A"
}

# We sum all flows depending on the units
GROUP BY ?u
```

---

__12 What is the determining flow of activity A in location L in the time period T under macro-economic scenario S?__

What is the determining flow of "Water Desalination" in "Location B" in 2011?

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX b: <http://ontology.bonsai.uno/core#>
PREFIX time: <http://www.w3.org/2006/time#>

SELECT ?flabel WHERE {

    # Ensure that the flow, ?f, is a determining flow of an activity, ?a.
    # Get the flow-object, ?fobject, of the flow.
    ?f b:determiningFlow ?a;
       a b:flow;
       b:objectType ?fobject.
       
    # Get the label of flow object to identify what the object is called.
    ?fobject rdfs:label ?flabel.
    
    # Ensure that the activity happens on location, ?l on 2011.
    ?a a b:activity;
       b:location ?l;
       b:hasTemporalExtent <http://rdf.bonsai.uno/time/2011>
       
    # Ensure that the location is "Location B".
    ?l rdfs:label "Location B"
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

Converts simple units of measure to their SI counterparts. (Only works for length, time (not 1/time), mass, and equivalent basic units - though it might work on energy. Doesn't work for velocity, rates, power density and similar with complicated conversions). This works with the property factor in the om ontology.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX om2: <http://www.ontology-of-units-of-measure.org/resource/om-2/>


SELECT ((xsd:float(?v)*xsd:float(?f)) as ?result)
WHERE {

  # What is needed is both the value, ?v, of the flow that is being calculated.
  # Here ?s is a measure since b:Flow is a subclass of om2:Measure.
  # We also need the unit so we know if we need to convert.
  ?s a om2:Measure;
     om2:hasUnit ?u;
     om2:hasNumericalValue ?v.
     
  # In the om ontology, the units have a conversion factor to SI if necessary.
  # This is referenced by the data-property om2:hasFactor and is stored in ?f.
  ?u om2:hasFactor ?f
}
LIMIT 1000
```

Property Path -> Activity Lists
----

Property path assumes the same pathway across recursive properties. This is the example for tracing "Coal" and works with the "simple_steel_transport" in the <https://github.com/BONSAMURAIS/BONSAI-ontology-RDF-framework/blob/master/examples/simple_steel_transport.rdf>.

Property paths are defined in <https://www.w3.org/2009/sparql/wiki/Feature:PropertyPaths>.

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
prefix bont: <http://ontology.bonsai.uno/core#>

# Output is a series of labels of agents that is in the activity train of "Coal".
SELECT ?alabel WHERE {

  # from activity ?a1, it checks all flows that are the output of an activity which is also an 
  # input to another activity, the other activity is stored in ?a2. It then checks ?a2 for
  # outputs that are also inputs of other activities. This works by the * operating on both the
  # properties ^bont:outputOf and bont:inputOf in order.
  # ^bont:outputOf is the inverse property of bont:outputOf (inversion of domain and range)
  ?a1 (^bont:outputOf/bont:inputOf)* ?a2.
  
  # This set of lines is to get the flow-object of the output of ?a1 by passing first identifying
  # the flow of ?a1 and then the ?outputObject.
  ?flow2 a bont:Flow; 
         bont:objectType ?outputObject;
         bont:outputOf ?a1.
         
  # The flow-object being monitored that has a label "Coal" is then isolated. This is done in the
  # next set of lines.
  ?outputObject a bont:FlowObject;
                rdfs:label "Coal".
                
  #Checks the labels of the agents that perform the activity ?a2, this includes ?a1              
  ?agent bont:performs ?a2; 
         rdfs:label ?alabel.
}
LIMIT 500
```

# Queries from Python
SparQLWrapper can be used to that effect.

For example, if one wishes to obtain a dictionnary with activities' uri as keys and labels as values:

        from SPARQLWrapper import SPARQLWrapper, JSON

        sparql = SPARQLWrapper("https://db.bonsai.uno/bonsai/query")
        sparql.setQuery("""
                PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
                SELECT DISTINCT ?s  ?lbl WHERE {  ?s a <http://ontology.bonsai.uno/core#ActivityType>; rdfs:label ?lbl }
        """)
        sparql.setReturnFormat(JSON)
        results = sparql.query().convert()

        response_dict={}
        for result in results["results"]["bindings"]:
            response_dict[result["s"]["value"]] = result["lbl"]["value"]
