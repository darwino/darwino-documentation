# JSON library and data binding
A lot of Darwino is based on top of JSON and particularly JSON store. Darwino uses its own library for JSON because there is no standard JSON library in Java. There are libraries available, but they tend to be poor performers, or cumbersome to use, or just heavy-weight. Their dependencies make for heavy mobile app code. The Darwino library is based on top of one written at IBM. It is 100% compatible with the JSON standard, and has been optimized for JSON parsing, and it includes JSON extensions. 


 In standard JSON, we have key/value pairs. Keys must be wrapped in double quotes. JavaScript, though, allows single quotes or key names with no quotes at all. The Darwino JSON parser is permissive and allows those. When it serializes, it does so according to JSON standard, but when it reads, it is permissive.
 
 Also, in JSON you cannot insert commands. Command insertion is available in JavaScript, but not in JSON. The Darwino parser permits commands when reading, but does not, by default, emit them.
Use case: A progress bar. When the client tells the server that it supports commands, the server can emit comments in the JSON that describe activity progress. The client can use those progress comments to display a progress bar.
 
 We can also emit binary JSON in place of text. Darwino does not use this externally, as when writing to a file or to a database, but it can be used when communicating via http. For example, when the client is replicating with the server, it uses a REST API that is based on JSON. If the client sends the server a header saying that the client understands the binary form of JSON, then it can compress the data. Values are compressed, and names can be sent once and subsequently only pointed to. Also, you don’t have to parse the data at all. (Must expand on this topic) 
 
 There is a query language for JSON, so you can apply a query to a JSON document and get a result.
 
 (Must expand on this topic with some details about the query language)
