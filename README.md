# TS4NLE: a Template System for Natural Language Explanations
TS4NLE is converts the explanation of an eXplainable AI (XAI) system into natural language utterances comprehensible by humans. Moreover, the XAI explanation can be enhanced with a domain and a user model that allow the natural language rendering to be tailored to the particular user. The code has been developed, and manteined, by [Radu Loghin](https://github.com/radu1690/Explanations)

TS4NLE has been tested with real users for providing persuasive explanations for the adherance to the Mediterranean diet, see the main paper [here](https://www.sciencedirect.com/science/article/pii/S0933365719310140) and the [SEMEX4ALL tutorial](https://horus-ai.fbk.eu/semex4all/) at [ISWC2020](https://iswc2020.semanticweb.org/program/tutorials/). A detailed Colab tutorial can be found [here](https://colab.research.google.com/drive/1iCVSt7TFMruSzeg5DswLOzOR1n7xATbz?usp=sharing)


## Installation
TS4NLE requires Node.js and Python languages and it is mainly based on [RosaeNLG](https://rosaenlg.org/): a Natural Language Generation library for node.js, based on the [Pug template engine](https://pugjs.org/). Once Node.js, Python, the [Graphviz](https://graphviz.org/) library and the nmp package manager have been installed, TS4NLE can be easily installed with:
```
git clone https://github.com/radu1690/Explanations.git
cd Explanations
npm install
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
This example
```
python csv2graphviz.py graphs/exp_graph_1_en.csv
```
will generate a pdf file named exp_graph_1_en.pdf with the reppresentation of the explanation graph.


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
