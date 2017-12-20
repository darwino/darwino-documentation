# Introduction to the Groovy DSL

The DSL used to define Domino data transformation is designed to be straightforward for normal cases but to have enough hooks to cover much more complicated situations.

When using the Darwino Sync Admin application, the "Generate From Database" button allows you to choose a database from a server and generate a default script based on the database's forms. For example, here is the result of choosing the local server's Catalog database:

![catalog.nsf DSL example](dsl-example.png)

This generated code shows off the primary elements of the DSL:

- `nsfName` is the top-level command that tells the adapter which NSF this connects to. This field uses Notes's "network path" format, and can include a remote server name separated by `!!`, such as `"RemoteServer/Org!!catalog.nsf"`
- The `form` command specifies a form name and opens a per-document converter block
- The `field` command specifies how to handle a given item name when found in a document. The names themselves are case-insensitive in Domino documents, while the keys in the Darwino JSON are always lowercased for consistency
	- The `type` attribute for fields specifies the data type to convert to; when left out, it defaults to text. The available types are:
		* `TEXT` for normal text and text-list values
		* `NUMBER` for numeric values (always treated as floating point on both Domino and Darwino)
		* `DATETIME` for date and time values, including date-only and time-only
		* `RICHTEXT` for rich text or MIME data (always converted to MIME when transforming to JSON)
		* `BOOLEAN` for values that should be converted to `true`/`false` in Darwino. The way this translates to Domino will covered later
		* `USERDATA` for binary data stored as "user data" in Domino, which can be used in advanced cases
		* `JSON` for values stored as JSON-compatible strings in Domino, which is converted to its native representation in Darwino
	- The `flags` attribute for fields specifies additional information about how to handle conversion. The available flags are:
		* `MULTIPLE` for fields that are expected to contain multiple values. For consistency, these values are always stored as arrays in JSON, even if there is only one entry
		* `NAMES` for Domino names fields that are neither reader nor author fields. These values are converted to and from LDAP format in JSON
		* `READERS` for readers fields, which are also converted to and from LDAP format and are stored in the special `_readers` field structure in JSON
		* `AUTHORS` for authors fields, which are also converted to and from LDAP format and are stored in the special `_writers` field structure in JSON
		* `NONSUMMARY` to denote fields that should have their summary flag un-set in Domino, to allow for storing larger data
		* `TAGS` to denote a single field per document that will be stored in the `_tags` field in JSON, which is treated specially in Darwino
		* `DISABLED` to mark a field as ignored on a per-field basis

## Item and Document Matching

Though the DSL generator generates definitions for every form and field, the DSL is not, by default, an exclusive descriptor of the data being transferred between the systems. In the absence of additional options, forms and items that are not defined in the DSL are transferred over in a "best match" way and are not coaxed into consistent data types. Because of this, an adapter DSL could contain nothing other than the `nsfName` line and would act as a basic data replicator on its own.

What the form and field definitions provide is a way to ensure that the app's data is in a consistent format when transferred to Darwino (and, as a side effect, cleaned up when sent back to Domino). Due to the nature of Notes development, many apps have inconsistent data types for the same field across documents, such as a value being a string in one field and a number in another. By specifying a defined type in the DSL, you avoid the necessity to write data-type checks into the Darwino app for every field.

There are two options to alter this behavior:

- `restrictToDefinedForms true` at the top level of the DSL will cause replication to ignore any documents in either direction that don't have a matching converter block. These documents are left untouched
- `restrictToDefinedFields true` within a document converter block will cause conversion to ignore any fields on matching documents that aren't explicitly defined. These fields are left untouched in replicated documents