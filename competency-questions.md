# Competency questions
The SPARQL queries associated with these questions are visible in the /queries folder, with matching numbers.
These should become part of a testing suite.
If a number is missing from the files in this folder, the query is not yet written.

## 01
Is the flow $x$ a determining product for the activity $y$

### Notes
Since flow here is a product, I translated it into flow-object.
How do we identify if it is a determining product? (query right now is only for product)	

## 02
Is input flow $x$ required for activity $y$? 

## 03
What is the amount of flow $x$ emitted as output during the time period $y$ ?

### Notes
I think $y$ here would be expressed as a lower $low$ bound and an upper $up$ bound. 
What do we do if we don’t have the actual data but we have to aggregate data in the database?


## 04
What is the location of the agent performing the activity $y$?

## 05
What other agents performing the same type of activity of agent $z$ are present in the same location $w$?

### Notes
The other agents I called $a$ and here z is a constant instead of a variable in sparql so I’ll leave it as a z. I also removed z from the result set since this is a given.


## 06
How are the flow objects quantified/ Which units of measure are used?

## 07
Does the input and output of flow objects follow mass balance?

### Notes
This is hard!

## 08
Is output flow required for activity of type 'stock'?

## 09
What is the direct input flow of flow-object F to activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?

## 10
What is the direct output flow of flow-object F from activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?

## 11
What is the determining flow of activity A in location L in the time period T under macro-economic scenario S?

## 12
What is the difference between input flows and output flows of flow-property P for activity A in location L in the time period T under macro-economic scenario S?

## 13
What is the additional input flow of flow-object F to activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?

## 14
What is the additional output flow of flow-object F from activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?

## 15
What is the direct output flow of flow-object F from activity A measured by flow-property P in location L in the time period T under macro-economic scenario S?  

## 16
What is the additional output flow of flow-object F from activity A measured by flow-property P in location M in the time period T resulting from a demand of flow-object G in location M in the time period U, all under macro-economic scenario S?

## 17
...
















