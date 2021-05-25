# Simple Json Parser Demonstration

![](https://img.shields.io/github/stars/renewrr/JsonParser) ![](https://img.shields.io/github/forks/renewrr/JsonParser) ![](https://img.shields.io/github/tag/renewrr/JsonParser) ![](https://img.shields.io/github/release/renewrr/JsonParser) ![](https://img.shields.io/github/issues/renewrr/JsonParser)

# What is this project?
This project is a demonstation of a self written JSON parser for the 2021 Spring SM404 Compiler course, written entirely in HTML and JavaScript.

The [JSON syntax diagram](https://www.json.org/json-en.html) provides a clear view of how a parser should work.

Visualization is done with the [jsonTreeViewer](https://github.com/summerstyle/jsonTreeViewer) library.

To verify the correctness of our parsed data, we use the native json parsing method in parallel with our parser, then we visualize both results to see if there are any discrepancy.

# Some explanation for the arcane parts of this project

### Mapping for important symbols

	let typeCat = new Map([
	  ['[', 'L_Brack'],
	  [']', 'R_Brack'],
	  ['{', 'L_Curl'],
	  ['}', 'R_Curl'],
	  ['"', 'Quote'],
	  [':', 'Colon'],
	  ['digit', 'Digit'],
	  ['other', 'Character'],
	  [',', 'Comma'],
	  ['ws', 'WS'],
	  ['.', 'Dec_Point']
	])
This is a map for converting symbols into strings that are easier to read, which makes later parts of the code easier to understand.

### Character evaluation
	let codeZero = "0".charCodeAt();
	let codeNine = "9".charCodeAt();

	function charEval(ch){
		type = typeCat.get(ch)
		if(digitCheck(ch)){
			type = 'Digit'
		}
		if(wsCheck(ch)){
			type = 'WS'
		}
		if(type === undefined){
			type = 'Character'
		}
		return new Object([ch,type])
	}
	function digitCheck(ch){
		return(ch.charCodeAt() >= codeZero && ch.charCodeAt() <= codeNine)
	}
	function wsCheck(ch){
		code = ch.charCodeAt() 
		return(code == 32 || code == 13 || code == 10 || code == 9)
	}
The charEval function is the most critical of this part of code, it reads one character then returns the type that character should be assigned to, according to the map in the previous section.

Since there are multiple characters designated as a digit or white space, we need two more functions to correctly identify these characters.(The digit check function was shamelessly "borrowed" from stackoverflow)

If a character is not identified as any of the types, we designate it as "character" type, since I think anymore types would be counterproductive.

### Recursive object and array evaluation

	function getObject(text,currPos)
	function getArray(text,currPos)

These are the bread and butter of the parser, with each character type identified, I implement the entire Json syntax rule as a bunch of if else statement, which I think is the quick and dirty way to do this. <sub><sup>Still true, but much more is added</sup></sub>

The main challenge is that each value of an Object or element of an Array could also be an Object or Array, when we encounter this scenario, the function recursively calls itself or calls the other function to evaluate the nested item.

This implementation makes it really easy to solve the nested array/object problem without major code modification, although some readability is sacrificed since I just made everything up as I go, so nothing really was planned. <sub><sup>Not entirely true after V3</sup></sub>

### How do I keep the position of the current character?

After each action, I always left the position at the last evaluated character, which we increments after we are ready to move to the next character.

We pass the current position to the getObject or getArray function when we encounters a nested item, these functions return the last position of the item with the evaluated item, we advance the position after everything is done and we are ready to progess to the next character. <sub><sup>Some operation now requires one look ahead symbol</sup></sub>

# To do list

Better UI : Currenty the UI consist of one textarea, three labels with one button.

Readme update : Update readme to V3.

# Real problems to be solved

Currently none that I am aware of, yay.