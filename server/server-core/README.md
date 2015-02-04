## AeroGear Server Differential Synchronization Server Core
This module contains implementations for the [server side API](../api)

## SyncServerEngine
The [SyncServerEngine](./server-core/src/main/java/org/jboss/aerogear/sync/server/ServerSyncEngine.java) is
responsible for driving the main differential synchronization algorithm but leaves the details of how data is stored and how diffs/patches are processed on that data to injectable
implementations.
It does this by getting injected during [construction](./server-core/src/main/java/org/jboss/aerogear/sync/server/ServerSyncEngine.java#L46) with an instance of a _ServerSynchronizer_ and a _ServerDataStore_.

### ServerDataStore
The [ServerDataStore](../../api/src/main/java/org/jboss/aerogear/sync/server/ServerDataStore.java) defines the methods for
storing a specific document types.

### ServerSynchronizer
The [ServerSynchronizer](./../api/src/main/java/org/jboss/aerogear/sync/server/ServerSynchronizer.java) defines the methods
required to perform the specific diff/patch processing on the data type supported.

#### Example of constructing a SyncServerEngine
To construct a server that uses the JSON Patch [synchronizer](./synchronizers/json-patch) you would use the following code:

    final JsonPatchServerSynchronizer synchronizer = new JsonPatchServerSynchronizer();
    final ServerInMemoryDataStore<JsonNode, JsonPatchEdit> dataStore = new ServerInMemoryDataStore<JsonNode, JsonPatchEdit>();
    final ServerSyncEngine<JsonNode, JsonPatchEdit> syncEngine = new ServerSyncEngine<JsonNode, JsonPatchEdit>(synchronizer, dataStore);

### Synchronizers
A _synchronizer_ in AeroGear is a module that serves two purposes which are closely related.
One, is to provide storage for the data type, and the second is to provide the patching algorithm to be used on that data type.
The name _synchronizer_ is because they take care of the synchronization part of the Differential Synchronization algorithm

For example, one synchronizer might support plain text while another supports JSON Objects as the content of documents being stored. But a patching algorithm used for plain text might
not be appropriate for JSON Objects. Hence the server and client side components are made up of "pluggable" [synchronizers](../../synchronizers) and data stores implementations.

By implementing the _ServerDataStore_ and _ServerSynchronizer_ interfaces different data formats and diff/patching algoritms can be provided. The AeroGear project provides the following
[synchronizers](../..//synchronizers/json-patch) out of the box:

*  [DiffMatchPatch](../../synchronizers/diffmatchpatch)
This synchronizer uses plain strings the document data type and Neil Frasers DiffMatchPatch Java implementation
for its patching algorithm.

*  [JSON Patch](../../synchronizers/json-patch)
This synchronizer uses [Jackson's](http://wiki.fasterxml.com/JacksonHome) _JsonNode_ as the document data type and uses
[JSON PATCH](https://tools.ietf.org/html/rfc6902) for its patching algorithm.


