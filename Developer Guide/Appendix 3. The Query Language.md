#The Query Language

The query language in Darwino is modeled on [the one used in MongoDB](http://docs.mongodb.org/manual/tutorial/query-documents/). This is unsurprising, given that MongoDB is a database for JSON documents. It uses JavaScript as a query language; the queries are JSON.

Darwino's query language is used to apply an evaluation formula on top of the JSON store, but it can be used beyond that. It can be used to query any kind of JSON document; the JSON doesn't have to reside in the database.

Working in the context of the JSON store, the query language is used to populate cursors. After opening a cursor, pass a query string or a JSON object as the argument to the cursor's query() method.

The syntax is straightforward. All queries are enclosed in braces. A very simple query would be a search in a field for a specific value. This would take the form:

```
{city:'Springfield'}
```

Expanding on that, we can add an "AND" operator:

> Notes:
> This {fieldname:'value'} syntax is a shorthand form for {fieldname: {$eq: 'value'}}, using the "$eq" operator.
> This shorthand exists because most of the time we want to test for equality.
>
> The field name can be either a literal string or a complete JSON path. The string does not have to be quoted; this is a benefit of Darwino's extension of JSON standard notation. JSON specs have field names between a pair of double quotes. Darwino respects that, but also permits single quotes or none at all.


##Operators
Naturally, there is a set of comparison operators. Here is a partial list. ALso, more wil be added over time.

> 
> Note: All operators start with the dollar sign. Arguments are typed: "1" is not equal to 1.
> 

- $and - Filters on all documents that match every field/value pair in the provided array of conditions.

 `{$and: [{city:'Springfield'}, {state:'New York'}])`


- $contains - Is the first argument contained within the second? The search value can be an array or a string.

 `{array: {$contains: 'a'}}`
 
 `{string: {$contains: 'g'}}`

- $eq - Tests for equality. See above for an example, and a description of the shorthand version.

- $exists - Tests whether a field exists or not.

 `{field1: {exists$: true}`

- $gte - Greater than or equal to.
- $gt - Greater than.
- $in - Test whether a value is in an an array of values

 `{it: {in$ ['can','we','find','it']}}
`
- $lte - Less than or equal to.
- $lt - Less than.
- $ne - Tests for inequality.

 ` {Joe: {$ne: 'Joseph'}}`

- $nin - Tests whether a value is NOT in an array of values.
- $nor - Returns documents that fail to match both conditions.
- $not - Used in conjunction with other operators, it will negate the result, returning documents that do not match the query.
- $or - Returns documents that match both conditions.
- $path - Evaluates the provided JSON path and returns the value.

 `{$upperCase: {$path: 'g.il'}}`

- $type - Tests whether the value is of the specified type.
`1: number`
`2: string`
`3: object`
`4: array`
`8: Boolean`

- $upperCase and $lowerCase - Converts the case of the argument.

The [MongoDB Query documentation](https://docs.mongodb.org/manual/tutorial/query-documents/) serves as a good reference for the Darwino query language, as long as you keep in mind that the two are not 100% identical. When using the MongoDB reference, ignore the MongoDB API and focus on the query language itself.

##Optimization
Darwino will take these queries and transform them to SQL as much as it can, given the capabilities of the underlying RDBMS for which the query generator is coded. There are three possible outcomes of this attempt:
- The entire query can be converted to SQL.
- Only fragments of the query can be converted.
- No conversion is possible.

In cases where only partial or no conversion is possible, Darwino will use the SQL that it CAN generate to first select from the database, and then it will "manually" filter the result fully to satisfy the query. It will do this by loading the resulting documents one at a time and then applying the remainder of the conditions against the in-memory document.

It is good practice when querying large datasets to keep in mind the capabilities of the RDBMS, and to write your queries in such a way as to permit the query generator to do the best possible job in translating the query into SQL.

##Extraction Language
A query will return the entire document; you will often be interested in only certain values from the document. Darwino can apply an extraction to the result of a query.

The extraction formula is in the form of a JSON document. The JSON document is a list of columns, and each column has a value. If the value is a literal string, it will be evaluated as a JSON path and the value of that path will be the result.

In this example, the result wil be two columns named "first" and "last", populated with the values found in the JSON paths "firstName" and "lastName" in the document.
```
{ first: "firstName", last: "lastName" }

```
Extending this, we can apply functions during the extraction to transform the results.
```
{ first: "firstName", last: {$upperCase: "lastName"}}

```
All of the query operators and functions can be used here, and it is also possible to add your own functions, making this a very powerful feature.

The extraction takes place server-side. While this clearly has the performance benefit that comes from not transmitting unneeded data to the client, it also allows your functions to utilize server resources, such as the cache and connections–such as to LDAP directories for lookups.

##Aggregation
There is a set of aggregation operators in the query language, permitting actions such as counting and summing and categorization of query results. SImilar to the extraction language, the syntax is a JSON document in which every entry is a column. Also similar to extraction, the order of the entries doesn't matter.

The aggregate operators ($count, $sum, $avg, $min, and $max) use a JSON path as a parameter.

```
{Count: {$count: "@manufacturer"}, Sum: {$sum: "@released"}, Avg: {$avg: "@released"}, Min: {$min: "@released"}, Max: {$max: "@released"}}
```

Behind the scenes, Darwino constructs a SQL query that uses the database's native aggregation operators. This is good in terms of efficiency: the document selection, value extraction, and aggregation is done server-side using efficient SQL statements. The database does the work.




