The JSON Store
====
The foundation of data manipulation in Darwino is JSON. Darwino provides a full JSON access library (included in the commons package). Darwino's JSON library is designed to be used in mobile device contexts, for this reason:

The Darwino JSON library is extremely lightweight,   
The Darwino JSON library is extremely high performing,  
The Darwino JSON library is platform agnostic.

>The Darwino JSON store is a highly modified combination of modified versions of JSONSmart and JSONPath. JSONSmart is the basis for the Darwino JSON parser, while JSONPath allows for accessing JSON objects in a manner similar to XPATH.

JSON and Darwino
--
The concept of a JSON Object in Darwino is extremely flexible. **By default**, Darwino represents JSON objects as a Hashmap (java.util.HashMap). This means that any map object can be treated as a JSON object in Darwino. The Darwino JSON object extends java.util.HashMap, and adds a large number of convenience methods, such as: getAsDouble, getAsDate, putArray, etc. See the [API Documentation](http://playground.darwino.com/playground.nsf/ApiDocumentation.xsp) for a full description of the API.


JSON Dates
---
As there is not a date object in JSON, Darwino stores dates as text ISO-8601 format. Darwino manages the conversion of these dates to and from Java dates. Also, Darwino stores all dates in GMT date unless a time zone is provided.

The Darwino JSON Factory
--
Because there are a number of JSON implementations (JSON.org, JAXON, etc.), and each has a different implementation, the Darwino JSON Factory (com.darwino.commons.json.JsonFactory) can be passed to any external library to allow for the use of any library's JSON format. This factory allows the developer to both read and write to any JSON library format.

Accessing Data in Darwino JSON
--
There are multiple ways to access/manipulate data in Darwino JSON. 

- JSONPath (see http://goessner.net/) is a way to access JSON data in a manner similar to XPath. Darwino has its own JSONPath implementation (com.darwino.commons.json.jsonpath)  
- Darwino also provides a JSON query API (com.darwino.commons.json.query), similar to MongoDB's query syntax.



