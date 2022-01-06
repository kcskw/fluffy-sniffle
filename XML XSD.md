## **How to determine which XML Template to Use**

The XML data packet can be built from scratch or one of the available XML templates can be used to build the XML data packet prior to submitting to the Authorization Gateway. The URI for the XML data packet for a given terminal can be retrieved from the Terminal Settings but can also be determined by using the criteria below.

The root path for all XML Templates is https://demo.eftchecks.com/webserivces/schemas/  followed by the SEC Code, “/Templates/”, and the XML Template name.  The XML Template is determined by the following criteria:

 - 	If the Terminal requires the Driver’s License Information.
 - 	If the Terminal is configured for Check Verification.
 - 	If the Terminal is configured for Identity Verification.

## [**How to determine which XSD to Use**](https://github.com/TKESuperDave/PayaServices/tree/XML/Authorization%20Gateway/XDS)
                       
The XSD that will be used can be retrieved from the Terminal Settings, but can also be determined by using the criteria below.  

The root path for all XSDs is http://demo.eftchecks.com/webservices/Schemas followed by the SEC Code and Schema Name. The Schema Name is determined by the following criteria:

 - If the Terminal requires the Driver’s License Information. 
 - If the Terminal is configured for Check Verification.
 - If the Terminal is configured for Identity Verification.
 - For PPD and CCD entries, If the Terminal is configured to allow Credit entries   
   
A matrix of the available XML Templates, and XSD Schemas for each SEC code can be found in the XML/XSD section, by SEC code. Each grid contains links to the templates and the schema needed determined by your required criteria. The grid also includes the Terminal IDs that can be used for testing and certifying against the provided schema. The Terminal ID will be different for guaranteed transactions and Non-guaranteed transactions.  
  
**Guaranteed terminals are numbered 1xxx, and Non-guaranteed terminals are numbered 2xxx**

An example XSD file path for a PPD terminal that does not require the driver’s license information, is setup for check verification, and is setup for identity verification, and does not allow credits would be as follows: 
https://demo.eftchecks.com/webservices/schemas/ppd/CheckVerificationIdentityVerificationDLOptional.xsd

There are published example XML data packets that contain example data, and XSD Schema packets. 
https://demo.eftchecks.com/webservices/schemas/ppd/examples/CheckVerificationIdentityVerificationDLOptional.xml

***Note about Special Characters**
Because the Data packet is XML, some special characters must be escaped to be included in the data. Please see the examples below.

|     Special Character    |     Symbol    |     Escaped Form     |
|--------------------------|---------------|----------------------|
|     Ampersand            |     &         |     \&amp;           |
|     Less-than            |     <         |     \&lt;            |
|     Greater-than         |     >         |     \&gt;            |
|     Quotes               |     “         |     \&quot;          |
|     Apostrophe           |     ‘         |     \&apos;          |

Link to [XML Examples](https://github.com/TKESuperDave/PayaServices/tree/XML/Authorization%20Gateway/XML)
Link to [XSD Schemas](https://github.com/PayaDev/PayaServices/tree/main/Authorization%20Gateway/XSD)

                      
                      
                      
| **PPD**                                                    | Certification Terminal ID                |                |                     |             |                       |             |              |           |
|------------------------------------------------------------|------------------------------------------|----------------|---------------------|-------------|-----------------------|-------------|--------------|-----------|
|                                                            | Guarenteed 1000's  Non-Guarenteed 2000's | XSD Guarenteed | XSD Non- Guarenteed | XML Example | XML Exampl with Token | DL Required | Verify Check | Verify ID |
| **Debit Only Transactions**                                |                                          |                |                     |             |                       |             |              |           |
| CheckNoVerification - DL Optional                          | 1010 / 2010                              | XSD            | XSD                 | XML         | XML                   |             |              |           |
| CheckNoVerification - DL Required                          | 1011 / 2011                              | XSD            | XSD                 | XML         | XML                   | X           |              |           |
| CheckVerification - IdentityVerification DL Optional       | 1012 / 2012                              | XSD            | XSD                 | XML         | XML                   |             | X            | X         |
| CheckVerification - IdentityVerification DL Required       | 1013 / 2013                              | XSD            | XSD                 | XML         | XML                   | X           | X            | X         |
| CheckVerification - Only DL Optional                       | 1014 / 2014                              | XSD            | XSD                 | XML         | XML                   |             | X            |           |
| CheckVerification - Only DL Required                       | 1015 / 2015                              | XSD            | XSD                 | XML         | XML                   | X           | X            |           |
| IdentityVerification - Only DL Optional                    | 1016 / 2016                              | XSD            | XSD                 | XML         | XML                   |             |              | X         |
| IdentityVerification - Only DL Required                    | 1017 / 2017                              | XSD            | XSD                 | XML         | XML                   | X           |              | X         |
|                                                            |                                          |                |                     |             |                       |             |              |           |
| **Credit & Debit Transactions**                            |                                          |                |                     |             |                       |             |              |           |
| CreditCheck - NoVerification DLOptional                    | 1810 / 2810                              | XSD            | XSD                 | XML         | XML                   |             |              |           |
| CreditCheck - NoVerificationDLRequired                     | 1811 / 2811                              | XSD            | XSD                 | XML         | XML                   | X           |              |           |
| CreditCheck - Verification IdentityVerification DLOptional | 1812 / 2812                              | XSD            | XSD                 | XML         | XML                   |             | X            | X         |
| CreditCheck - Verification IdentityVerification DLRequired | 1813 / 2813                              | XSD            | XSD                 | XML         | XML                   | X           | X            | X         |
| CreditCheck - Verification Only DLOptional                 | 1814 / 2814                              | XSD            | XSD                 | XML         | XML                   |             | X            |           |
| CreditCheck - Verification OnlyDLRequired                  | 1815 / 2815                              | XSD            | XSD                 | XML         | XML                   | X           | X            |           |
| CreditIdentity - Verification OnlyDLOptional               | 1816 / 2816                              | XSD            | XSD                 | XML         | XML                   |             |              | X         |
| CreditIdentity - Verification OnlyDLRequired               | 1817 / 2817                              | XSD            | XSD                 | XML         | XML                   | X           |              | X         |
                      
                      
| **PPD**                                                    | Certification Terminal ID                |                |                     |             |                       |
|------------------------------------------------------------|------------------------------------------|----------------|---------------------|-------------|-----------------------|
|                                                            | Guarenteed 1000's  Non-Guarenteed 2000's | XSD Guarenteed | XSD Non- Guarenteed | XML Example | XML Exampl with Token |
| **Debit Only Transactions**                                |                                          |                |                     |             |                       |
| CheckNoVerification - DL Optional                          | 1010 / 2010                              | XSD            | XSD                 | XML         | XML                   |
| CheckNoVerification - DL Required                          | 1011 / 2011                              | XSD            | XSD                 | XML         | XML                   |
| CheckVerification - IdentityVerification DL Optional       | 1012 / 2012                              | XSD            | XSD                 | XML         | XML                   |
| CheckVerification - IdentityVerification DL Required       | 1013 / 2013                              | XSD            | XSD                 | XML         | XML                   |
| CheckVerification - Only DL Optional                       | 1014 / 2014                              | XSD            | XSD                 | XML         | XML                   |
| CheckVerification - Only DL Required                       | 1015 / 2015                              | XSD            | XSD                 | XML         | XML                   |
| IdentityVerification - Only DL Optional                    | 1016 / 2016                              | XSD            | XSD                 | XML         | XML                   |
| IdentityVerification - Only DL Required                    | 1017 / 2017                              | XSD            | XSD                 | XML         | XML                   |
|                                                            |                                          |                |                     |             |                       |
| **Credit & Debit Transactions**                            |                                          |                |                     |             |                       |
| CreditCheck - NoVerification DLOptional                    | 1810 / 2810                              | XSD            | XSD                 | XML         | XML                   |
| CreditCheck - NoVerificationDLRequired                     | 1811 / 2811                              | XSD            | XSD                 | XML         | XML                   |
| CreditCheck - Verification IdentityVerification DLOptional | 1812 / 2812                              | XSD            | XSD                 | XML         | XML                   |
| CreditCheck - Verification IdentityVerification DLRequired | 1813 / 2813                              | XSD            | XSD                 | XML         | XML                   |
| CreditCheck - Verification Only DLOptional                 | 1814 / 2814                              | XSD            | XSD                 | XML         | XML                   |
| CreditCheck - Verification OnlyDLRequired                  | 1815 / 2815                              | XSD            | XSD                 | XML         | XML                   |
| CreditIdentity - Verification OnlyDLOptional               | 1816 / 2816                              | XSD            | XSD                 | XML         | XML                   |
| CreditIdentity - Verification OnlyDLRequired               | 1817 / 2817                              | XSD            | XSD                 | XML         | XML                   |
