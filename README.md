# TS4NLE: a Template System for Natural Language Explanations
TS4NLE is a tool that converts the explanation of an eXplainable AI (XAI) system into natural language utterances comprehensible by humans. Moreover, the XAI explanation can be enhanced with a domain and a user model that allow the natural language rendering to be tailored to the particular user. The code has been developed, and manteined, by [Radu Loghin](https://github.com/radu1690/Explanations)

TS4NLE has been tested with real users for providing persuasive explanations for the adherance to the Mediterranean diet, see the main paper [here](https://www.sciencedirect.com/science/article/pii/S0933365719310140) and the [SEMEX4ALL tutorial](https://horus-ai.fbk.eu/semex4all/) at [ISWC2020](https://iswc2020.semanticweb.org/program/tutorials/). A detailed Colab tutorial can be found [here](https://colab.research.google.com/drive/1iCVSt7TFMruSzeg5DswLOzOR1n7xATbz?usp=sharing)



## Installation
The source code of TS4NLE can be retrieved [here](https://github.com/ivanDonadello/TS4NLE). It requires Node.js and Python languages and it is mainly based on [RosaeNLG](https://rosaenlg.org/): a Natural Language Generation library for node.js, based on the [Pug template engine](https://pugjs.org/). Once Node.js, Python and the nmp package manager have been installed, TS4NLE can be easily installed by installing, as first step, the [Graphviz](https://graphviz.org/) library for graph visualization.

## Citing TS4NLE
If you use TS4NLE in your research, please use the following BibTeX entry.
```
@article{DragoniDE20,
  author    = {Mauro Dragoni and
               Ivan Donadello and
               Claudio Eccher},
  title     = {Explainable {AI} meets persuasiveness: Translating reasoning results into behavioral change advice},
  journal   = {Artif. Intell. Medicine},
  volume    = {105},
  pages     = {101840},
  year      = {2020}
}
```

## Run
You can run TS4NLE with:  
```
node explanations.js <template> <graph>
```
where `template` is the `.pug` file of the template and `graph` the `.csv` explanation graph file. Here some examples in the repository:
```
node explanations.js templates/english/argument.pug graphs/exp_graph_1_en.csv
``` 
will output "Red meat contains animal protein which can cause the increment of cancer risk!". You can specify the language, English or Italian (the default is English), like in the following example:  
```
node explanations.js templates/italian/feedback.pug graphs/exp_graph_1_it.csv italian
```  
with output "Hai consumato una grande quantità di carne rossa (3 su un massimo di 2)".

You can also generate a visual reppresentation of a graph with [Graphviz](https://graphviz.org/) with:
```
python csv2graphviz.py <graph.csv>
```  

Example:
```
python graphviz.py graphs/exp_graph_1_en.csv
```
will generate a pdf file named exp_graph_1_en.pdf with the reppresentation of the graph.

# Explanation graph format
The graphs are stored in csv file (with \t delimiter) where each row contains a triple in the form of "subject - predicate - object": subject is the parent node, object is the child node and predicate is the arc label.  
In every graph the following must be true:
* the subject of the first row is the head node of the graph
* the graph does not contain cycles
* a child node has only one parent node

# Basic templates and RosaeNLG tutorial
For all the features in Pug templates you can check the [Pug main website](https://pugjs.org/).  
A complete tutorial with advanced features of RosaeNLG can be found [here](https://rosaenlg.org/rosaenlg/3.0.0/tutorials/tutorial_en_US.html).
## Plain text
To insert some plain text you can use the pipe character (`|`) followed by the text you want to write:  
```
|Hello world!
```
will output: ```Hello world!```

Spaces between different plain texts (except only punctuation) are automatically inserted, so:
```
|Hello
|world
|!
```
will still output: ```Hello world!``` 

## Variables  
The object passed to RosaeNLG (after the graph has been processed) is an explanation.  
To print a field, you write ```#[+value(explanation.field)]``` or ```!{explanation.field}```. For example, the value of explanation.entity.enLabel is "red meat":  
```
|You ate #[+value(explanation.entity.enLabel)].
```
will output: ```You ate red meat.```  

```#[+value(explanation.field)]``` will throw an error if the field is undefined while ```!{explanation.field}``` will print an empty string.

## Conditionals
If-else statements are used like in the following example where _entity.enLabel=meat_ and _constraint=less_:
```
if explanation.constraint == 'less'
	|You ate too much,
	|enough 
else 
	|You did not eat enough,
	|more
|#[+value(explanation.entity.enLabel)]!
```  
will output: ```You ate too much, enough meat!```  
Note that to use an explanation field in an if-else statement you must not use the ```#[+value()]``` or the ```!{}``` notation.  

## Javascript code, iteration and functions
You can write javascript code in a template by starting the line with the "```-```" character,for example here i declare two variables and then I use them in the template:
```
- let nutrient = "cheese"
- let food = "pie"

|the !{food} contains a lot of !{nutrient}
```
This will output ```The pie contains a lot of cheese```

## Iteration
You can do loop with the ```eachz``` operator from RosaeNLG:
```
-let data = ["fruits", "vegetables", "meat"]
eachz element in data
    |!{data}
```
will output: ```fruits vegetables meat```

The ```eachz``` operator can accept some [parameters](https://rosaenlg.org/rosaenlg/3.0.0/mixins_ref/eachz_itemz.html) which are useful in Natural Language Generation. Example:
```
-let data = ["fruits", "vegetables", "meat"]
|I bought
eachz element in data with {separator: ",", last_separator: "and", end: "."} 
    |!{element}
```
will output: ```I bought fruits, vegetables and meat.```

## Functions
### Pluralize
You can use ```pluralize.isSingular()``` and ```pluralize.isPlural()``` in a template to check if a word is either singular or plural. Example:  
```
if pluralize.isSingular(explanation.entity)
	|contains
else
	|contain
```  
This will output ```contains``` if the entity field is singular or ```contain``` if the entity field is plural.  

### Random
The random function will return a random element of an array (if the argument is an array) or the argument if it is not an array.  
```
- let nutrient = random(explanation.entity.negative)
- let consequence = random(nutrient.cons_en)
```

## Functions customization
You can also add your own javascript functions to use in pug templates.  
Here I create a function in my JS file which will print the input text two times and then I pass it to RosaeNLG:
### JS file
```
var rosae = require('rosaenlg');

function customFunction(input){
    text = input + " " + input
    return text
}

console.log( rosae.renderFile('testNLG.pug', {
    language: 'en_US',
    double : customFunction
}) );
```  
### PUG template testNLG.pug
```
-let data = ['apples', 'bananas', 'apricots']
|!{double(data[0])}
```  
This will output: ```Apples apples```. 
