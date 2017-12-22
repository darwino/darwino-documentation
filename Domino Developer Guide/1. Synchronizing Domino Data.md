# Synchronizing Domino Data
Darwino has an extensible replication engine. It’s used with Darwino databases as well as any others for which there is a connector. A database connector provides a JSON view of the data sources. You can implement a connector on top of ANY datasource. We created a connector for Domino, so there's a JSON view of Domino databases for the replication engine.

The replication engine and its connectors are written entirely in Java, and are fully extensible. You register a class on the server and it will be used to handle the replications. Because Java is verbose, we make this customization easier by use of a Groovy interface, described in [Synchronizing Domino Data 3. Customizing the data transformation](Synchronizing Domino Data 3. Customizing the data transformation.md).


## Field Mapping
When replicating with Domino, it is necessary to map between Darwino concepts and Domino concepts. The NSF and the Darwino database fields are mapped one-to-one. Everything is configurable, but by default we replicate all of the documents from Domino into one store in Darwino. The notion of a store doesn’t exist in IBM Domino; in Domino, you have the NSF, and the NSF is a container for Notes documents. In Darwino, you have one extra level: The database is a set of stores, and each store is a container for documents.

Often multiple Domino NSFs will share a common design, inherited from a template. Darwino replication can be configured to replicate sync multiple such NSFs into one Darwino database. When doing this, each Domino database goes into one Darwino instance. A simple example is the discussion database, where the design is typically identical among all Domino instances. Several discussion databases could be replicated to a single Darwino database on a mobile device. The mobile user or application would simply select the appropriate instance from the database.

At the document level, we map every field in a Domino document into a JSON entry in the JSON document. By default, myField in Domino will become myFIeld in the JSON document. Again, this is completely configurable. We can exclude, add, and calculate fields during replication. Along with the fields, we can have attachments. A key difference between the two environments: in Darwino a document has a list of attachments; in Domino, it can be in the rich text field or in the document itself. So we replicate them all, and then we use a coding convention in the Darwino name of the attachments to distinguish. This is so we can later use that convention to properly replicate back to the same places in Domino.

Edge case in attachment transformation: when you have an embedded image in a rich text item, you have to distinguish between an embedded image and an attachment in the rich text, so can know to display inline. The is a filename convention to do this distinguishing.

In Domino, rich text can be native or MIME. Generally, the client uses native format. Darwino understands only HTML and MIME, and needs it in this format. During replication, it transforms the rich text to HTML plus attachments. If it's MIME in Notes, it’s taken as-is. If you change and replicate it back to Domino, it will save the rich text as MIME. There is no re-transformation on the way back.

Darwino uses the Domino API to do this; as a result it’s only as good as what IBM provides. There are 3rd-party tools that can handle the transformation with better fidelity, and they can be utilized by Darwino.

Replicating with Domino, there are two possible routes:
- Darwino can replicate through HTTP. Darwino puts a replication connector on Domino that lets it replicate through HTTP. Because it uses the HTTP protocol, the remote repicator doesn’t have to know it’s Domino.

- The better architecture is to have Domino handle the replication through a Java replication engine. This removes two translations: into and out of HTTP.

When replicating from on-premise Domino with cloud-based (for exaple, BlueMix) Darwino, the cloud-side server cannot initiate the replication from the Domino side. The Domino scheduler handles the replication through the firewall with BlueMix. This scheduled replication is described in [Synchronizing Domino Data 1. Installing Darwino on a Domino Server](Synchronizing Domino Data 1. Installing Darwino on a Domino Server.md).


