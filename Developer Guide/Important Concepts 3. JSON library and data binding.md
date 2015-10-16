# JSON library and data binding
A lot of Darwino is based on top of JSON, and particularly the JSON store. Darwino uses its own library for JSON because there is no standard JSON library in Java. There are libraries available, but they tend to be poor performers,  cumbersome, or just heavy-weight. Their dependencies make for heavy mobile app code. The Darwino library is based on top of one written at IBM. It is 100% compatible with the JSON standard, and has been optimized for JSON parsing, and it includes JSON extensions. 

 In standard JSON, we have key/value pairs. Keys must be wrapped in double quotes. JavaScript, though, allows single quotes or key names with no quotes at all. The Darwino JSON parser is permissive and allows those. When it serializes, it does so according to JSON standard, but when it reads, it is permissive.
```
 // A JSON parser is available through a Factory
JsonFactory f = JsonJavaFactory.instance;
JsonObject jo = (JsonObject)f.fromJson("{a:11, b:12, c: 13}");

// Json objects/arrays have easy to use methods
_formatText("JSON Object, compact: {0}",jo.toJson());
_formatText("JSON Object, pretty: {0}",jo.toJson(false));

// Or this can be done through a factory
_formatText("JSON Object, compact: {0}",f.toJson(jo));
_formatText("JSON Object, pretty: {0}",f.toJson(jo,false));

```
 
###Command Insertion
Standard JSON has no provision for inserting commands. While command insertion is available in JavaScript, it is not in JSON. The Darwino JSON parser permits commands when reading, but does not, by default, emit them.

In Darwino, the JSON implementation supports JavaScript-style comments as an extension, and the parser can read inside these comments. This feature enables reading and writing commands embedded in the JSON.
 
A typical use case for this is a progress bar. When the client tells the server that it supports commands, the server can emit comments in the JSON that describe activity progress. The client can use those progress comments to display a progress bar.
 
###JSON Compression
We can also emit compressed, binary JSON in place of text. Darwino does not use this externally, as when writing to a file or to a database, but it can be used when communicating via HTTP. For example, when the client is replicating with the server, it uses a REST API that is based on JSON. If the client sends the server a header saying that the client understands the binary form of JSON, then it can compress the data. Values are compressed, and names can be sent once and subsequently only pointed to. Also, this removes the need for parsing the data.
 
 ###JSON Query Language
 There is a query language for JSON, so you can apply a query to a JSON document and get a result.
 
## (Must expand on this topic with some details about the query language)
