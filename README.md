gulp-i18n-extract
===
[![NPM version][npm-image]][npm-url] 

This gulp tasks extracts i18n keys and texts from HTML and JSON files.

This task ist part of the i18n toolchain:
1. [Tag](to be implemented) text nodes in HTML with an i18n key attribute
2. Extract i18n keys and values (This task)
3. Translate
4. [Compile](https://github.com/Netatwork-de/gulp-i18n-compile2) into language files for i18n like [Aurelia-i18n](https://github.com/aurelia/i18n)  

## Installation

Install `gulp-i18n-extract` using npm into your local repository.

```bash
npm install gulp-i18n-extract --save-dev
```
## Usage

Setup a gulp task `i18n-extract`. By default, this job will only extract data from html files.

```js
var gulp = require('gulp');
var i18n = require('gulp-i18n-extract');

var options = {};

gulp.task('i18n-extract', function() {
  return gulp.src("src/**/*.html")
             .pipe(i18n.extract("./lang/language.json",options))
             .pipe(gulp.dest("."));
});
```

## Options

- `outFile` : string = `'i18nextract.json'`

	Output file name. 

- `plugIns` : object[] = `[ new i18n.html("t") ]`

	Specify, which plug-in should be used on extraction.
	
- `markUpdates` : boolean = `true`

	If an extracted value has been changed or added, it will be marked with `"needsUpdate":true` .
	
- `defaultLanguages` : string[] = `["de"]`

	Each default language will be added as translation, if missing.
	
- `warnOnDuplicates` : boolean = `true`

	Created warnings if i18n keys are used twice.

- `keepObsoleteTranslations` : boolean = 'false'

	All translations from obsolete or deleted key will remain in the extract file as `obsoleteTranslations`. These texts will be grouped by the extracted content and contain all found translations.

##Output structure
```json
{
	"<filename w/o ext>": {
		"src":"<relative file path>",
		"content": {
			"<i18n key>": {
				"content":"<extracted content>",
				"lastModified":"<date of extraction>",
				"translations": {
					"<language, 2letter iso>" : {
						"content":"<translated content>",
						"lastModified":"<date of translation>"
					}
				},
				"needsUpdate":"<boolean>"
			}
		}
	},
	"obsoleteTranslations": [
		{
			"key":"<original text>",
			"translations":{
				"<language, 2letter iso>": [
					"<found translation for original text>",
					"<found translation for original text>",
					...
				]
			}
		}
	]
}
```

# PlugIns
There are two predefined plugins available to handle html and json files.

## HTML
You can specify the attribute that is used to identify localizable strings. (Default: "t")
```js
var options = { plugIns:[ new i18n.html("t") ] };  
```
### Examples:
+ Extract the inner text
```html
<h1 t="index.title">Welcome</h1>
```
+ Extract attributes
```html
<input type="email" placeholder="Email" t="[placeholder]index.email">
```
+ Inner text and attributes
```html
<span data-toggle="tooltip" data-placement="right" title="Tooltip" 
      t="[text]index.example;[title]index.exampleTooltip">
	  Example text
</span>
```
+ Skip extraction of certain keys by providing ignore function in the second argument. In the following example, `index.${variable}` is not extracted.
```js
var options = { plugIns:[ new i18n.html("t", (key) => key.indexOf("${") >= 0) ] };  
```   
```html
<input type="email" placeholder="Ignored for extraction" t="[placeholder]index.${variable}">
```

## JSON
You can specify the file extension for your json files. (Default: ".r.json")
```js
var options = { plugIns:[ new i18n.json(".r.json") ] }; 	  
```

## Custom PlugIn
A plugin has to provide two functions: canHandle and parse. 

`canHandle` will be called to determine, if the plugin can extract tokens out of the given file.

`parse` implements the extraction and will receive the file and an addToken function. 
```js
var customPlugIn = {		
		canHandle : function(fileName) {			
			return true || false;
		},
		
		parse: function (file, addToken) {			
			addToken("i18nKey","extracted text");					
		}	
	}
	
var options = { plugIns:[ customPlugIn ] }; 
```

# License

[Apache 2.0](/license.txt)

[npm-url]: https://npmjs.org/package/gulp-i18n-extract
[npm-image]: http://img.shields.io/npm/v/gulp-i18n-extract.svg