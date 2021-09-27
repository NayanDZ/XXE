# XXE (XML external entity injection)

XXE injection allows an attacker to interfere with processing XML data of application.

Attacker can view internal file's of applicatgion server and interact with back end systems.

## How XXE vulnerability occur?

When aplications use the XML format to transmit data between the browser to server. they always using standard library or platform API to process XML data on the server.

XML specification contains various potentially dangerous features, and standard parsers support these features even if they are not normally used by the application.

Like XXE are a type of custom XML entity whose defined values are loaded from outside of the DTD in which they are declared. also external entities are allow an entity to be defined based on the contents of a file path or URL. 
