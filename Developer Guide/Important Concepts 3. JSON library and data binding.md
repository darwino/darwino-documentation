# JSON library and data binding
A lot of Darwino is based on top of JSON, and particularly the JSON store. There is no standard JSON library in Java, and the ones that are available can be inconsistent and incompatable with one-another; Darwino deals with this in two ways:

- The Darwino library provides a JSON Factory, which sits atop your libraries of choice and encapsulates all the features you need to manipulate JSON.  It provides a uniform view of JSON - a common API on top of disparate API implementations.

- Darwino also provides its own library for JSON. While other libraries can be used via the factory described above, these libraries tend to be poor performers, cumbersome, or just heavy-weight. Their dependencies make for heavy mobile app code. Also, it is not always convenient to have to work though the extra layer provided by the Darwino-provided adaptor.

The Darwino library is based on top of one written at IBM. It is 100% compatible with the JSON standard, and has been optimized for JSON parsing, and it includes JSON extensions. This library is used all over the Darwino product, and it makes it easy to deal with and consume JSON objects and arrays.
  
Because of this, best practice when manipulating JSON in your Darwino application is to use the default JsonJavaFactory instance and its JsonObject and JsonArray classes.

The JsonObject is a Map of string/object; the keys are strings and the values are objects. This means that if you have a function that is expecting a Map as a parameter, you can pass it a JsonObject.

The JsonArray object is a list of values implemented as a Java List. It is a List of objects. IT has all of the methids you'd expect from a JSON array, but it is also a List, so if you have a function expecting a List of objects, you can pass it a JsonArray as a parameter.

To make JSON values easily consumable in Java, a string inside a JsonObject is a Java String, a number is a Java Number, and a boolean is a Java Boolean.

In standard JSON, we have key/value pairs. Keys must be wrapped in double quotes. JavaScript, though, allows single quotes or key names with no quotes at all. The Darwino JSON parser is permissive and allows those. When it serializes, it does so according to JSON standard, but when it reads, it is permissive.
```
 // A JSON parser is available through a Factory
JsonFactory f = JsonJavaFactory.instance;
JsonObject jo = (JsonObject)f.fromJson("{a:11, b:12, c: 13}");

// Json objects/arrays have easy-to-use methods
_formatText("JSON Object, compact: {0}",jo.toJson());
_formatText("JSON Object, pretty: {0}",jo.toJson(false));

// Or this can be done through a factory
_formatText("JSON Object, compact: {0}",f.toJson(jo));
_formatText("JSON Object, pretty: {0}",f.toJson(jo,false));

```
 
###Command Insertion
Darwino takes advantage of JSON comments to provide a facility to insert commands in JSON. Comments in JSON is  an extension to the JSON standard, and is supported by Darwino. Similarly to JavaScript, the parser supports single-line (//) and multi-line (/* ... */) comments.

Standard JSON has no provision for inserting commands. Darwino's JSON implementation supports JavaScript-style comments as an extension, and the parser can read inside these comments. This feature enables reading and writing commands embedded in the JSON.

When a comment in JSON starts with "/*%=", the Darwino JSON interpreter will interpret the JSON object that follows as a command. It will parse the contents and return the result to whatever called the parser. The parser will always be looking for inersted commands, but if you haven't registered a callback to handle the commands it will do nothing.

A typical use case for this is a progress bar. Imagine that a very large JSON file is being returned to the client via REST services. When the client tells the server that it supports commands, the server can emit comments in the JSON that describe activity progress. The client can use those progress comments to display a progress bar.

A command is generally a notification from the code that generated the JSON payload, as in this example to track the progress of a file. It takes the form of a JSON value, containing data that can be interpreted by the consumer. For example, it can be the current progress in the whole file, like:
```
/*%={label:'Updating table TTT', progress: '56%'} */
```

It is up to the consumer to interpret its content appropriately.

The Darwino JSON parser can interpret commands when reading, but does not, by default, emit them. To enable insertion of command in JSON output, specify OPTION_PARTIALPROGRESS when creating the JsonWriter. The HttpServiceContext, when processing an HTTP request, will then look for a header with a value of "x-dwo-json-progress" from the caller, and only if it sees that header will it insert the commands in its output.
 
###JSON Compression
We can also emit compressed, binary JSON in place of text. Darwino does not use this externally, as when writing to a file or to a database, but it can be used when communicating via HTTP. For example, when the client is replicating with the server, it uses a REST API that is based on JSON. If the client sends the server a header saying that the client understands the binary form of JSON, then it can compress the data. Values are compressed, and names can be sent once and subsequently only pointed to. Also, this removes the need for parsing the data.
 
 ###JSON Query Language
 There is a query language for JSON, allowing you to quickly and easily query JSON data. The query language is actually a JSON document itself and is super-subset of the MongoDB one. This language is used throughout Darwino.
 
 Here is an example showing the query language being used to populate a [cursor](Darwino DB API 4. Cursors and Queries.md) based on JSON content:
```
 Cursor c = store.openCursor()
	.query("{$or: [{state:'MI'},{state:'TX'}]}");
```
 

###JSON Data Extractor
The JSON Data Extractor facility is similar to the query language, but used to extract fields and values from a JSON document:
```
Cursor c = store.openCursor()
	.extract("{first:'firstName',last:'lastName'}").range(0,5);

```
 See [Appendix 3. The Query Language](Appendix 3. The Query Language) for details on the Query Language and JSON Data Extractor.
