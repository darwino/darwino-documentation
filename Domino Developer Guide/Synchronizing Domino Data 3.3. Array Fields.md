# Array Fields

The `arrayField` matcher can be used to transform values that are stored in multiple fields in common idioms in Domino for UI or Summary purposes to be combined into single fields in Darwino. This has a number of configuration options, but the simple case looks like:

	arrayField "Name", type:TEXT, delimiter: "_"

This command will convert the fields "Name_1", "Name_2", etc. into a single "name" array field in Darwino. On replication back, it will break apart the array from Darwino back into matching indexed fields in Domino. For example, a Domino document may contain the following fields:

	Name_1: "Joe Schmoe"
    Name_2: "Jane Doe"
    Form: "Person"

This would be converted to and from this JSON document in Darwino:

	{
    	"name": ["Joe Schmoe", "Jane Doe"],
        "form": "Person"
    }

There are a number of options to control the numbering and position of the numeric index:

* `zeroBased`: indicates that the first field in the array is indexed with a `0` instead of `1` in Domino. `false` by default.
* `initialIndexed`: indicates that the first field in the array contains a numeric index in Domino. `true` by default. Setting this to `false` will match e.g. "Name", "Name_2", etc.
* `prefix`: indicates that the numeric index appears at the start of the field name instead of the end in Domino. `false` by default.

