Darwino DB API
=======================

#Accessing and storing social data
There is a set of social data that can be associated with any document. There are three kinds of social data:
-	Comments: Darwino creates a default store for the comment data. Keeping comments out of the documents themselves prevents unnecessary updates to the documents which then must replicate and could result in conflicts. Moreover, comments can contain full JSON content with attachments.
-	Tags: The tags are stored as an array in the _tags field in the document. The tags can be queried, and the store can return a list of tags that can be used, for example, to create a tag cloud.

 Private tags, visible only to their creator, can be stored as read-protected response documents associated with the document being tagged. Because they are read-protected, they will be private to that user. Because the store’s list of tags respects this security, only the private tag’s creator will see such a private tag in a tag cloud.
-	User-dependent values, which are handled at the store level. These don’t require that the document be loaded for them to be applied, and they are stored externally to the documents in order to avoid unnecessary document modification, replication conflicts, and excessive data in the documents (there can be a lot of ratings and read flags):
--	Ratings: With the rate() method, a document is assigned an integer value associated with a user. Appropriately, only one rating per document per user is stored. There are three methods for retrieving rating data:
 - getRate() – Given a unid and userName, returns that user’s rating for the document
 - getRateAvg() – Given a unid, returns the average rating from all users for the document
 - getRateSum() - Given a unid, returns the sum of all ratings for the document, for example to count votes where ratings would be defined by the application to range, say, from -1 to 1.

 -- Sharing: With the share method, a unid and username sets a Boolean flag indicating whether the document has been shared by that user. It is similar in concept to a Facebook “like”. Two methods exist to interrogate shares:
 - isShared() – Returns a Boolean indicating whether the document is shared by a specified username
 - getShareCount() – returns the number of shares by all users as an integer

 -- Read: While not specifically social, the read flag is functionally similar to the other social data. At the store level, the readMarkenabled option enables the auto-flagging of documents as being read when they are loaded. This applies only to documents that are actually loaded; their being included in a query result is not sufficient to mark them as read. A document load() option can also prevent the flag from being set, when necessary.
There are three methods in the store to enable manipulation and querying of the read flags:
 -- isRead() returns the read value for the specified document and username
 -- markRead() - Given a unid, a Boolean, and a username, sets the read flag for that document and user.
 -- getReadCount() – returns the number of reads for the specified document.

There are two ways to access this social data. The methods at the store level require the unid and the username, in addition to any flags being set, to identify the document. The same methods are available in the Document object, where they do not require those two identifying parameters. Which set of methods you choose will depend on the context. If you have the document loaded, then use the Document methods, if just for simplicity. If you’re in a view, use the Store methods since they will be significantly more efficient (they will not require loading the documents).

 This social data can be used in extracted fields in the documents, making it easy to create indexes based on their values for querying and sorting.

