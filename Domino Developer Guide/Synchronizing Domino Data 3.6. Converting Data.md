# Converting Data

Field data can be converted programmatically when transferring between Domino and Darwino, via the use of Groovy closures.

```groovy
form("Foo") {
    field "ConversionField",
        toDarwino: { value, targetDoc, sourceDoc ->
            value + " -- stored in Darwino"
        },
        toDomino: { value, sourceDoc, targetDoc ->
            value - " -- stored in Darwino"
        }
}
```

The two closures are passed different object types for their source and target:

- `toDarwino`
    - `targetDoc` is an instance of [`com.darwino.commons.json.JsonObject`](https://documentation.darwino.com/doc/javadoc/platform/com/darwino/commons/json/JsonObject.html) containing the destination document's JSON
    - `sourceDoc` is an instance of [`com.darwino.domino.napi.wrap.NSFNote`](https://documentation.darwino.com/doc/javadoc/domino/com/darwino/domino/napi/wrap/NSFNote.html), which is an object similar to `lotus.domino.Document` but based on direct C API access
- `toDomino`
    - `sourceDoc` is an instance of [`com.darwino.jsonstore.replication.impl.ReplicationSourceDocument`](https://documentation.darwino.com/doc/javadoc/platform/com/darwino/jsonstore/replication/impl/ReplicationSourceDocument.html), with attachments available via `#getAttachments()` and the JSON available via `#getJson()`, which will in turn be a [`com.darwino.commons.json.JsonObject`](https://documentation.darwino.com/doc/javadoc/platform/com/darwino/commons/json/JsonObject.html)
    - `targetDoc` is an instance of `lotus.domino.Document`