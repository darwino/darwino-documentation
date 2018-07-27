# Boolean Fields

Because Domino doesn't have the concept of boolean field storage, developers have created many ways of specifying true/false values in Domino documents. When specifying a field as `type:BOOLEAN`, there are two new attributes available to control what is treated as true and what is false: `trueValue` and `falseValue`.

These values can be strings or numbers. For example:

```groovy
form("StringBoolean") {
	field "SomeField", type:BOOLEAN, trueValue: "Y", falseValue: "N"
}

form("NumberBoolean") {
	field "SomeField", type:BOOLEAN, trueValue: 1, falseValue: 0
}
```