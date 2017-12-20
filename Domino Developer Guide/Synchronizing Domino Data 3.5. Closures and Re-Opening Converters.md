# Closures Re-Opening Converters

Document converter closures can be "re-opened" to provide additional rules after the main block. For example:

```groovy
form("Foo") {
    field "FirstName"
}
form("Foo") {
    field "LastName"
}
```

In a normal case, this isn't terribly useful, but this can be combined with the fact that the form blocks are, in Groovy parlance, "closures", which are blocks of code that can be treated as data. For example, you can specify some common behavior across several forms:

```groovy
def personRules = {
    field "FirstName"
    field "LastName"
}
form("InternalUser", personRules)
form("ExternalUser", personRules)
form("ExternalUser") {
    field "ExternalEmail"
}
```