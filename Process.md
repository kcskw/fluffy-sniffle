# **Phase 1 Preparation**

## **Connect to the Authorization Gateway**

Once you have successfully connected to the Authorization Gateway and are comfortable with adding the SOAP header, you will test your request and response with the GetCertificationTerminalSettings.
 
The GetCertificationTerminalSettings web method is defined in the “**Terminal Settings – XML Specification**” section, providing an example of the request and response. The invocation of this web method is part of the Preparation Phase because it is the simplest web method and requires no input parameters.

This web method can be invoked if your implementation team determines the host system needs to acquire information about the Authorization Gateway Terminal, and does not need to be invoked on a continuous basis.

_Please note this is not to be confused with the GetTerminalSettings, which performs the same function for production terminals during the Production Phase._

## **SEC Standard Entry Class Codes**
The Authorization Gateway uses the Standard Entry Class (SEC) codes to determine what information is required to be sent in the submission. The National Automated Clearing House Association (NACHA) requires the use of SEC Codes for each transaction settled through the Automated Clearing House (ACH).  Each code identifies what type of transaction occurred. In addition, the SEC_CODE element in the response XML Data Packet form the GetCertificationTerminalSettings web method will include the SEC code used from the terminal ID provided.  A definition of each of the SEC codes used by the Authorization Gateway can be found below.

•	**PPD** - Prearranged Payment and Deposit Entry :  A prearranged payment and deposit entry is either a standing or single entry authorization where the funds are transferred to or from a consumers account. 

•	**CCD** - Corporate Credit or Debit :  A prearranged payment and deposit entry is either a standing or single entry authorization where the funds are transferred to or from a business account. 

•	**WEB** - Internet Initiated Entry :  An internet initiated entry is a method of payment for goods or services made via the internet.    

•	**TEL** - Telephone Initiated Entry :  A telephone initiated entry is a payment for goods or services made with a single entry debit with oral authorization obtained from the consumer via the telephone.

•	**POP** - Point-of-Purchase Entry : The Point-of-Purchase method of payment is for purchases made for goods or services in person by the consumer.  These are non-recurring debit entries. A check reading device must be used to capture the routing number, account number, and check number from the source document (check). The source document cannot be previously used for any prior POP entry, and the source document must be voided and returned to the customer at the point-of-purchase. In addition, a signed receipt must be obtained at the point-of-purchase and retained for 2 years from the settlement date. The “Authorization Requirements” section in the Authorization Gateway Specification document contains additional information on the receipt requirements.

•	**C21** - Check 21 :  Although not an SEC Code C21 is used to denote Check 21 transactions. Check 21 requires a check reading device capture the routing number, account number, and check number from the source document (Check) as well as capture images of both the front and back of the source document.  


How to determine which XML Template to Use
The XML data packet can be built from scratch or one of the available XML templates can be used to build the XML data packet prior to submitting to the Authorization Gateway. The URI for the XML data packet for a given terminal can be retrieved from the Terminal Settings but can also be determined by using the criteria below.

The root path for all XML Templates is https://demo.eftchecks.com/webserivces/schemas/  followed by the SEC Code, “/Templates/”, and the XML Template name.  The XML Template is determined by the following criteria:

  - 	If the Terminal requires the Driver’s License Information.
  - 	If the Terminal is configured for Check Verification.
  - 	If the Terminal is configured for Identity Verification.

A matrix of the available XML Templates for each SEC code can be found in the XML templates section. Each grid contains the name of the XML Template, based on the XML Templates determining criteria, and a link to the actual XML Template. 

The grid also includes the Terminal IDs that can be used for testing and certifying the XML data packet that can be built from the provided XML Template. The Terminal ID will be different for guaranteed transactions and Non-guaranteed transactions.  Guaranteed terminals are numbered 1xxx, and Non-guaranteed terminals are numbered 2xxx.

There are also published example XML data packets that contain example data. 
https://demo.eftchecks.com/webservices/schemas/ppd/examples/CheckVerificationIdentityVerificationDLOptional.xml

***Note about Special Characters**
Because the Data packet is XML, some special characters must be escaped to be included in the data. Please see the examples below.
|     Special Character    |     Symbol    |     Escaped Form    |
|--------------------------|---------------|---------------------|
|     Ampersand            |     &         |     &amp;           |
|     Less-than            |     <         |     &lt;            |
|     Greater-than         |     >         |     &gt;            |
|     Quotes               |     “         |     &quot;          |
|     Apostrophe           |     ‘         |     &apos;          |

# **Phase 2 Development**

**Interfacing with the Authorization Gateway**
The best place to start is to determine your application architecture for interfacing with the Authorization Gateway.  You will choose which published XSD(s) your XML data packets will be validated against, and you also know the URL for the corresponding XML template(s) for your schema(s).  
This leaves you with the following possibilities for creating your XML data packets that are sent to the Authorization Gateway:

1.	XML Schema Definition Tool (such as Xsd.exe for .Net or Svcutil.exe) to generate a class based on the published XSD, populate the class properties, and then serialize the object.
2.	LINQ to XML to build your xml and populate the elements and attributes.
3.	You can load the XML template into an XML document object and use Xpath to populate the elements and attributes.
4.	You can build your own XML document and use Xpath to populate the elements and attributes.

We recommend you leverage the published XSDs and XML templates and use either the first or second options when creating the data packets to be sent. All these methods use the .NET platform however other languages have successfully been used. 

We have provided example request XML Data Packets to assist your integration team with getting started. A link to these examples can be found at the end of the “How to determine which XML Template to Use” section.


Once you have determined how you will create your XML data packets in your system; we recommend reviewing each element and attribute and when they are best used. The “**Data Packet – XML Specification**” provides links to XML templates, and text description of the regular expressions, data types, or enumerations that control the allowed data formats for each element.

## **Data Identification**
The specification for the Authorization Gateway XML Data Packet allows you to optionally identify your data in two distinct ways. The **REQUEST_ID** attribute contained within the AUTH_GATEWAY element and the **TRANSACTION_ID** element. These are built in so your host system can match a response from the Authorization Gateway with the original request. 

These identifiers are not inherently unique, rather the Authorization Gateway leaves the responsibility of determining if an identifier is unique to the host system. It is not required that optional identifiers are unique, but it is strongly recommended. If an identifier is not unique it may become difficult for your host system to match responses or retrieve archived responses.  In the examples we have provided, GUIDs have been used as optional identifiers. The use of GUIDs ensures uniqueness, but any value can be used as an identifier, including database identity column values. It is also important to note that if the implementation team determines an identifier needs to be unique, that it only needs to be unique for a specific terminal ID, but it can be unique across all terminal IDs for a given user. 
 

The REQUEST_ID attribute should be a unique identifier that is used to identify the overall data packet. When your data packet is received by the Authorization Gateway it is processed, and asynchronously stored along with the response. This is done so the host system can invoke the GetArchivedResponse web method to request a previous response. 

The GetArchivedResponse web method accepts the REQUEST_ID as an input parameter and will return the corresponding response.  It is important to note that the GetArchivedResponse is a production only web method and can only be effectively used if the host system keeps track of and submits values in the REQUEST_ID attribute.  The value in the REQUEST_ID attribute of the request data packet is also returned in the response data packet in the REQUEST_ID attribute of the RESPONSE element.

The TRANSACTION_ID element should be a unique identifier that is used to identify a specific transaction.  The value contained in the TRANSACTION_ID element is recorded by the Authorization Gateway but is not used internally and cannot be used to request a specific transaction. The value in the TRANSACTION_ID element is however returned in the response data packet in the TRANSACTION_ID element within the parent AUTHORIZATION_MESSAGE element. This was done so that your host system can match the response for a specific transaction to an internal record in the host system. 

## **Valid Identifiers**
Each request XML Data Packet must contain a valid identifier for its schema. The identifier you use will change depending on the context of the transaction being sent. Your integration team will become more familiar with the different identifiers as you begin to work on each milestone. However, a list of all the valid identifiers can be found below.  

|                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     Authorize (A)    |     This   is used in schemas for POP, TEL, WEB, and Check 21 to indicate that an   authorization is requested for the XML Data Packet being sent.  It is also used to process credit   transactions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|     Recurring (R)    |     This   is used in schemas for PPD, CCD, TEL and WEB to indicate that an   authorization is requested for a single or reoccurring transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|     Void (V)         |     This   is used in schemas for PPD, CCD, POP, TEL, WEB, and Check 21 to void a   previously authorized transaction. However, it should be noted that   transactions can only be voided on the same calendar day they were   authorized.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|     Override (O)     |     This   is used in schemas for POP, TEL, and Check 21 when the host system receives a   manager needed message to void the previous transaction and input a new   transaction in its place.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     Payroll (P)      |     This   is used in schemas for POP and Check 21 for business and payroll checks. What   this does is NOT link the driver’s license to the routing/ account numbers   since the person writing/cashing the check is usually not the business.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|     Update (U)       |     This   is used in schemas for POP and Check 21 for OCR transactions that already   have complete data in the data packet. It forces the transaction to run as a   normal POP or Check 21 transaction on an OCR terminal. This is normally done   when a change is needed to a transaction that was submitted under a normal   OCR transaction. Example: A transaction is sent through using the OCR engine.   The data that is returned does not match the image. If the transaction was   still successful and a change is warranted, a Void Transaction is sent. Then   another transaction with updated data (from the response and corrected from   user) is sent back through the system with a complete data packet and “U” as   the identifier. If the transaction failed, other actions will need to be taken.    |

## **Verification Only**
If the gateway terminal is setup as verification only or the VERIFICATION_ONLY element is set to true, then the transaction will be processed as verification only. This means that an authorization will be run, but that the check will not undergo Electronic Check Conversion (ECC) and will have to be taken to the bank for deposit. Depending on the merchant’s program, the funds may or may not be guaranteed.

## **Account Section Data**
All PPD, CCD, TEL and WEB schemas define that the ACCOUNT child elements must contain values.  The child elements within the ACCOUNT element for POP and Check 21 (C21) schemas define what ACCOUNT child elements must contain values and what ACCOUNT child elements can be left empty.  All of the child elements within the ACCOUNT element for POP and Check 21 (C21), except the ACCOUNT_TYPE for POP schemas, define the data as optional. This is because for these SEC codes you can either provide the swiped MICR data or provide the routing, account, and check numbers.   If the MICR_DATA, ROUTING_NUMBER, ACCOUNT_NUMBER, and CHECK_NUMBER are all left empty in the request data packet then the transaction cannot be processed. Either the MICR_DATA or the ROUTING_NUMBER, ACCOUNT_NUMBER, and CHECK_NUMBER elements must contain values. 

It is important to note that for POP transactions, that if the swiped MICR data in the MICR_DATA element is missing, but the ROUTING_NUMBER, ACCOUNT_NUMBER, and CHECK_NUMBER elements contain values then the transaction will be processed as verification only; even if the CONTROL_CHAR indicates that the information was retrieved from a check reader. In addition, if the MICR_DATA, ROUTING_NUMBER, ACCOUNT_NUMBER, and CHECK_NUMBER elements all contain values, then the Authorization Gateway will only use the information in the MICR_DATA element and will parse it out overwriting any values sent in the ROUTING_NUMBER, ACCOUNT_NUMBER, and CHECK_NUMBER elements.

## **Identity information**
Identity information needs to be included when the terminal is setup to do identity verification. There are schemas that will handle the validation for terminals that are setup to do identity verification, and the GetCertificationTerminalSettings web method will return a response of “true” in the RUN_IDENTITY_VERIFICATION element. If a terminal is setup to do identity verification, then the host system is required to send either the last 4 of the check writers social security number OR their birth year (not both). 

## **Connection Method**
Paya Services supports connection via secure (https) webservice using SOAP.  SOAP is a simple XML-based protocol to let applications exchange information over HTTP.  
The webservice address used for certification and testing is as follows:

https://demo.eftchecks.com/webservices/AuthGateway.asmx

Each web method contains a custom SOAP header used for authentication. A username and password for certification will be provided.
You can reach our Integration Department by email at integration@eftsupport.com.

## **SOAP Header**
The SOAP header contains the following fields:
|                   |                |                                                                                                                                                                    |
|-------------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    **UserName**      |     String     |     Username   provided by Paya Services for authorization.                                                                                                        |
|     **Password**      |     String     |     Password   provided by Paya Services for authorization.                                                                                                        |
|     **TerminalID**    |     Integer    |     Unique to   each terminal used.  Provided by Paya   Services at time of terminal approval.    Terminal IDs for certification are provided in this document.    |

**Example:** 
```
  <soap:Header>
    <AuthGatewayHeader    xmlns=”http://tempuri.org/GETI.eMagnus.WebServices/AuthGateway” >
      <UserName> GATEWAYUserName </UserName>
      <Password> GATEWAYPassword</Password>
      <TerminalID>1210</TerminalID>
    </AuthGatewayHeader>
  </soap:Header>
```

## **Web Methods**
A definition of the web methods can be found below. Each web method contains a hyperlink to a sample SOAP request and response.

## **Certification Web Methods** 
Definition and hyperlink to sample SOAP request and response.

- [**GetCertificationTerminalSettings**](https://demo.eftchecks.com/webservices/AuthGateway.asmx?op=GetCertificationTerminalSettings)
  - **Description**: This method will return the Terminal Settings for a certification Terminal. This method is used during interface testing and certification.
  - **Input**:  Accepts no parameters. 
  - **Output**: Outputs an XML string. 

- [**AuthGatewayCertification**](https://demo.eftchecks.com/webservices/AuthGateway.asmx?op=AuthGatewayCertification)	
  - **Description**:  This method will validate that the interface is sending a data packet that conforms to its schema and is used during interface testing and certification.
  - **Input**:  Accepts an XML string called a data packet that must conform to the terminals schema provided in the certification Terminal Settings.
  - **Output**: Outputs an XML string.

- [**ProcessSingleCertificationCheck**](https://demo.eftchecks.com/webservices/AuthGateway.asmx?op=ProcessSingleCertificationCheck)
  - **Description**:  This method will run the authorization for a single certification check based on the settings for the provided certification terminal. A list of the valid certification routing numbers and their purpose is below.  This method is used during interface testing and certification.

|     Routing   Number    |     Purpose                 |
|-------------------------|-----------------------------|
|     490000018           |     Authorization           |
|     490000034           |     Decline                 |
|     490000021           |     Manager   Needed        |
|     490000047           |     Re-Presented   Check    |
|     490000050           |     No   ACH                |
|     490000015           |     MICR   ERROR            |

   - **Input**:  Accepts an XML string called a data packet that must conform to the certification terminals schema provided in the certification Terminal Settings.
   - **Output**: Outputs an XML string.

## **Certification Web Methods when using Tokens**
Definition using tokens and hyperlink to samples of SOAP request and response.

- [**GetCertificationTerminalSettings]
  - **Description**: This method will return the Terminal Settings for a certification Terminal. This method is used during interface testing and certification.
  - **Input**:  Accepts no parameters. 
  - **Output**: Outputs an XML string. 

- [**AuthGatewayCertification]
  - **Description**:  This method will validate that the interface is sending a data packet that conforms to its schema and is used during interface testing and certification.
  - **Input**:  Accepts an XML string called a data packet that must conform to the terminals schema provided in the certification Terminal Settings.
  - **Output**: Outputs an XML string.

- [**ProcessSingleCertificationCheckWithToken]
  - **Description**:  This method will run the authorization for a single certification check based on the settings for the provided certification terminal using either, a given Token or the Account Type, Routing Number, and Account Number. A list of the valid certification routing numbers and their purpose is below.  This method is used during interface testing and certification.

|     Routing Number    |     Token                               |     Purpose               |
|-----------------------|-----------------------------------------|---------------------------|
|     490000018         |     05944FB3E1DA4663868455AF630F45BE    |     Authorization         |
|     490000034         |     15944FB3E1DA4663868455AF630F45BE    |     Decline               |
|     490000021         |     25944FB3E1DA4663868455AF630F45BE    |     Manager Needed        |
|     490000047         |     35944FB3E1DA4663868455AF630F45BE    |     Re-Presented Check    |

  - **Input**:  Accepts an XML string called a data packet that must conform to the certification terminals schema provided in the certification Terminal Settings.
  - **Output**: Outputs an XML string.

_NOTE: Using this method by passing the Account Type, Routing Number, and Account Number will create a TOKEN and pass it back in the Authorization Message Response. If a TOKEN already exists for the Account Type, Routing Number, and Account Number, the current TOKEN will be passed back in the Authorization Message Response._

- [**GetCertificationToken**]
  - **Description**: This method will return a Token for the Account Type, Routing Number, and Account Number.
  - **Input**:  Accepts an XML string called a data packet that must conform to the schema provided in this [Link](https://demo.eftchecks.com/webservices/Schemas/other/gettoken.xsd).
  - **Output**: Outputs an XML string.
  - 
- [**ParseCertificationMICR**]
  - **Description**: This method will return an Account Type, Routing Number and Account Number.
  - **Input**:  Accepts an XML string called a data packet that must conform to the schema provided in this [Link](https://demo.eftchecks.com/webservices/Schemas/other/parsemicr.xsd).
  - **Output**: Outputs an XML string.

## **Production Web Methods** 
Definition and hyperlink to sample SOAP request and response.

- [**GetTerminalSettings**]
  - **Description **: This method will return the Terminal Settings for a terminal.
  - **Input**:  Accepts no parameters.
  - **Output**: Outputs an XML string.

- [**ProcessSingleCheck**]
  - **Description**:  This method will run the authorization for a single check based on the settings for the terminal.
  - **Input**:  Accepts an XML string called a data packet that must conform to the terminals schema provided in the Terminal Settings.
  - **Output**: Outputs an XML string.

- [**GetArchivedResponse**]
  - **Description**:  This method will retrieve a response for a previously processed transaction.
  - **Input**:  Accepts a Request ID string.
  - **Output**: Outputs an XML string.

## **Production Web Methods when using Tokens**
Definition using tokens and hyperlink to a sample SOAP request and response.

- [**ProcessSingleCheckWithToken**]
  - **Description**:  This method will run the authorization for a single check based on the settings for the terminal using either, a given Token or the Account Type, Routing Number, and Account Number.
  - **Input**:  Accepts an XML string called a data packet that must conform to the terminals schema provided in the Terminal Settings.
  - **Output**: Outputs an XML string.

_NOTE: Using this method by passing the Account Type, Routing Number, and Account Number will create a TOKEN and pass it back in the Authorization Message Response. If a TOKEN already exists for the Account Type, Routing Number, and Account Number, the current TOKEN will be passed back in the Authorization Message Response._


- [**GetToken**]
  - **Description**:  This method will return a Token for the Account Type, Routing Number, and Account Number.
  - **Input**:  Accepts an XML string called a data packet that must conform to the schema provided in this [Link](https://demo.eftchecks.com/webservices/Schemas/other/gettoken.xsd).
  - **Output**: Outputs an XML string.

- [**ParseMICR**]
  - **Description**:  This method will return an Account Type, Routing Number and Account Number.
  - **Input**:  Accepts an XML string called a data packet that must conform to the schema provided in this [Link](https://demo.eftchecks.com/webservices/Schemas/other/parsemicr.xsd).
  - **Output**: Outputs an XML string.

## **Terminal Settings – XML Specification**
The GetCertificationTerminalSettings and GetTerminalSettings web methods will return the following XML string.

### **Terminal Settings XML Example**:
```
<?xml version=”1.0” encoding=”utf-8”?>
<TERMINAL_SETTINGS xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” 
xmlns:xsd=”http://www.w3.org/2001/XMLSchema”>
  <TERMINAL_ID>2318</TERMINAL_ID>
  <SEC_CODE>WEB</SEC_CODE>
  <IS_GATEWAY_TERMINAL>true</IS_GATEWAY_TERMINAL>
  <ALLOW_CNSMR_CREDITS>false</ALLOW_CNSMR_CREDITS>
  <DL_REQUIRED>false</DL_REQUIRED>
  <RUN_CHECK_VERIFICATION>false</RUN_CHECK_VERIFICATION>
  <RUN_IDENTITY_VERIFICATION>false</RUN_IDENTITY_VERIFICATION>
  <SCHEMA_FILE_PATH>	
http://localhost/geti.emagnus.webservices/Schemas/WEB/Ng_CheckNoVerificationDLOptional.xsd
  </SCHEMA_FILE_PATH>
  <XML_TEMPLATE_PATH>
http://localhost/geti.emagnus.webservices/Schemas/WEB/Templates/CheckNoVerificationDLOptional.xml
  </XML_TEMPLATE_PATH>
</TERMINAL_SETTINGS>
```
### **The Terminal Settings XML will contain the following elements**:
|                                  |                                                                                                                                                                        |
|----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     TERMINAL_SETTINGS            |     Is the parent   element and contains all other elements within the Terminal Settings XML   document.                                                               |
|     TERMINAL_ID                  |     Contains the ID   for the terminal. The Terminal ID will be numeric value.                                                                                         |
|     SEC_CODE                     |     Contains the   Standard Entry Class. This will either be Ck21, PPD, CCD, POP, TEL, or WEB.                                                                         |
|     IS_GATEWAY_TERMINAL          |     Contains true or   false indicating if the Terminal is a gateway terminal or not.                                                                                  |
|     DL_REQUIRED                  |     Contains true or   false indicating if the terminal requires the driver’s license state and   number is to be included in the data packet request.                 |
|     RUN_CHECK_VERIFICATION       |     Contains true or   false indicating if the terminal is setup for check verification.                                                                               |
|     RUN_IDENTITY_VERIFICATION    |     Contains true or   false indicating if the terminal is setup for identity verification.                                                                            |
|     SCHEMA_FILE_PATH             |     Contains the   Uniform Resource Identifier (URI) specifying the published XML Schema Definition   (XSD) that the data packet request will be validated against.    |
|     XML_TEMPLATE_PATH            |     Contains the   Uniform Resource Identifier (URI) specifying the published XML template that   can be used as the basis for creating the data packet request.       |

## **Validation Handling**
When the AuthGatewayCertification web method receives a request it will first validate your request XML Data Packet against the published XSD for your terminal. Each returned response will include a VALIDATION_MESSAGE element.  If the request XML Data Packet successfully passes validation the RESULT child element of the VALIDATION_MESSAGE element will contain a value of “Passed”, but if the validation failed, the RESULT element will contain a value of “Failed”.  These values can be coded into your host system for determining if a request passed or failed validation. The VALIDATION_MESSAGE element will also contain a SCHEMA_FILE_PATH element. The SCHEMA_FILE_PATH element will be present regardless of if the request XML Data Packet passed or failed validation and will include the full URI for the XSD that was used for validating the request XML Data Packet. In addition, if the RESULT element contains “Passed” then only the RESULT and SCHEMA_FILE_PATH elements will be present as child elements of the VALIDATION_MESSAGE. However, if the request XML Data Packet fails validation, and the RESULT element contains a value of “Failed”, then the VALIDATION_MESSAGE will contain one or more VALIDATION_ERROR elements.  The VALIDATION_ERROR element will contain SEVERITY and MESSAGE elements that will detail exactly what failed in the request XML Data Packet as well as LINE_NUMBER and LINE_POSITION attributes that will define exactly where the validation error occurred.  

The host system should always check each response to make sure the RESULT child element of the VALIDATION_MESSAGE is set to “Passed”.  If it is not, then there are validation errors and the transaction was not processed. The host system will have to correct any validation errors outlined in the VALIDATION_ERROR element(s) and then resubmit the request XML Data Packet.

## **Data Packet – XML Specification**
The data packet is an XML string sent using the AuthGatewayCertification, ProcessSingleCheck, and ProcessSingleCheckWithToken web methods. The XML data packet must conform to the XSD specified in the Terminal Settings. The XML Template provided in the Terminal Settings can be used as a basis to create the Data Packet.

_NOTE:  Methods with Token will operate the same as those without tokens. Tokens are used in place of Account Type, Routing Number, and Account Number._

### **Authorization Gateway XML Data Packet Example**:
This XML data packet example contains all available elements. The elements and data types that are required for a specific terminal are defined in that terminal’s XSD.
```
<?xml version=”1.0” encoding=”utf-8”?>
<AUTH_GATEWAY REQUEST_ID=”4654”>
  <TRANSACTION>
    <TRANSACTION_ID>0a4f529d-70fd-4ddb-b909-b5598dc07579</TRANSACTION_ID>
    <MERCHANT>
      <TERMINAL_ID>1113</TERMINAL_ID>
    </MERCHANT>
    <PACKET>
      <IDENTIFIER>A</IDENTIFIER>
      <CONTROL_CHAR>S</CONTROL_CHAR>
      <VERIFICATION_ONLY>false</VERIFICATION_ONLY>
      <ACCOUNT>
        <MICR_DATA>T490000018T 24413815O 4456</MICR_DATA>
        <ROUTING_NUMBER>490000018</ROUTING_NUMBER>
        <ACCOUNT_NUMBER>24413815</ACCOUNT_NUMBER>
        <CHECK_NUMBER>4456</CHECK_NUMBER>
        <ACCOUNT_TYPE>Checking</ACCOUNT_TYPE>
      </ACCOUNT>
      <CONSUMER>
        <FIRST_NAME>Test</FIRST_NAME>
        <LAST_NAME>Guy</LAST_NAME>
        <ADDRESS1>1001 Test Ave.</ADDRESS1>
        <ADDRESS2>#200</ADDRESS2>
        <CITY>Destin</CITY>
        <STATE>FL</STATE>
        <ZIP>32540</ZIP>
        <PHONE_NUMBER>2345678912</PHONE_NUMBER>
        <DL_STATE>FL</DL_STATE>
        <DL_NUMBER>D12346544</DL_NUMBER>
        <COURTESY_CARD_ID></COURTESY_CARD_ID>
        <IDENTITY>
          <DOB_YEAR>1961</DOB_YEAR>
        </IDENTITY>
</CONSUMER>
      <CHECK>
        <CHECK_AMOUNT>1.25</CHECK_AMOUNT>
        <IMAGE_FRONT />
        <IMAGE_BACK />
      </CHECK>
      <CUSTOM>
        <CUSTOM1></CUSTOM1>
        <CUSTOM2></CUSTOM2>
        <CUSTOM3></CUSTOM3>
        <CUSTOM4></CUSTOM4>
      </CUSTOM>
    </PACKET>
  </TRANSACTION>
</AUTH_GATEWAY>
```
### The Authorization Gateway XML data packet may contain the following elements:

|                            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     AUTH_GATEWAY:          |      Is the parent   element and contains all other elements within the Auth Gateway Request XML   document.                                                                                                                                                                                                                                                                                                                                                                       |
|     REQUEST_ID:            |      Is an optional   attribute that contains a unique user defined ID to identify the   authorization gateway request. The Request ID will be returned in the   Authorization Gateway response.  It   only persists with that specific connection and once the authorization is   returned it is gone.   The purpose of this attribute is to link the   authorization request with the returned response and to provide a “lookup”   value for the GetArchivedResponse method.    |
|     TRANSACTION:           |     Contains   all of the elements for a given transaction.                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     TRANSACTION_ID:        |     Is   an optional element that contains a unique user defined ID to identify the   transaction. The Transaction ID will be returned in the Authorization Gateway   response.                                                                                                                                                                                                                                                                                                    |
|     MERCHANT:              |     Contains   all of the elements for the merchant.                                                                                                                                                                                                                                                                                                                                                                                                                               |
|     TERMINAL_ID:           |     Contains   the ID for the Terminal. The Terminal ID will be numeric value.                                                                                                                                                                                                                                                                                                                                                                                                     |
|     PACKET:                |     Contains   all of the elements for packet.                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|     IDENTIFIER:            |     Contains   a value that identifies the packet being sent as an Authorization, Void,   Override, or Payroll transaction. The identifier is a single alpha character.   A list   of identifiers follows, but valid identifiers will vary by schema.   A=Authorize, R=Recurring, V=Void, F = Reversal, O=Override, P=Payroll,   U=Update                                                                                                                                          |
|     NOTE:                  |     Identifier   R (Recurring) is in name only. We will NOT run the transaction more than   once.                                                                                                                                                                                                                                                                                                                                                                                  |
|     CONTROL_CHAR:          |     Contains   a value that identifies the information in the packet as being entered   manually or retrieved from a check reader.    Valid   control characters are as follows: M=Manual, S=Swipe.                                                                                                                                                                                                                                                                                |
|     VERIFICATION_ONLY:     |     Contains   a true or false value identifying if the transaction should be processed as   verification only.  NOTE: The Boolean   data type in the XSD will require that true/false be all lower case.                                                                                                                                                                                                                                                                          |
|     ACCOUNT:               |     Contains   all of the elements for a given account.                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     MICR_DATA:             |     Contains   the MICR data read from a check reader.    The MICR   Datacan be up to 200 characters including the following:  0-9, T, O, -, D, A, $, U, :, <, ;, =, b,   o, t.                                                                                                                                                                                                                                                                                                    |
|     ROUTING_NUMBER:        |      Contains the keyed   in 9 digit routing   number.                                                                                                                                                                                                                                                                                                                                                                                                                             |
|     ACCOUNT_NUMBER:        |     Contains   the keyed in account number. Valid account   numbers should be between 3 and 17 numeric characters.                                                                                                                                                                                                                                                                                                                                                                 |
|     CHECK_NUMBER:          |     Contains   the keyed in check number. Valid check   numbers should be between 1 and 15 characters.                                                                                                                                                                                                                                                                                                                                                                             |
|     ACCOUNT_TYPE:          |     Contains   the type of account. Valid   valuesare Checking or Savings.                                                                                                                                                                                                                                                                                                                                                                                                         |
|     CONSUMER:              |     Contains   all of the elements for a given consumer.                                                                                                                                                                                                                                                                                                                                                                                                                           |
|     FIRST_NAME:            |     Contains   the first name of the consumer.  The First   Namecan be up to 100 alpha characters.                                                                                                                                                                                                                                                                                                                                                                                 |
|     LAST_NAME:             |     Contains   the last name of the consumer. The Last   Namecan be up to 100 alpha characters.                                                                                                                                                                                                                                                                                                                                                                                    |
|     ADDRESS1:              |     Contains   the first line of the consumer’s address. The Address1   can be up to 200 alpha-numeric characters and can include the   following:  # , - , : , ;                                                                                                                                                                                                                                                                                                                  |
|     ADDRESS2:              |     Contains   the second line of the consumer’s address. The Address2   can be up to 200 alpha-numeric characters and can include the   following:  # , - , : , ;                                                                                                                                                                                                                                                                                                                 |
|     CITY:                  |     Contains   the city of the consumer’s address. The Citycan   be up to 50 alpha characters.                                                                                                                                                                                                                                                                                                                                                                                     |
|     STATE:                 |     Contains   the state or province of the consumer’s address. Valid state and province   codes can be found here.                                                                                                                                                                                                                                                                                                                                                                |
|     ZIP:                   |     Contains   the zip code of the consumer’s address.                                                                                                                                                                                                                                                                                                                                                                                                                             |
|     PHONE_NUMBER:          |      Contains the   consumer’s contact phone number. The phone   numberis expected as a 10 digit number without a – or ().                                                                                                                                                                                                                                                                                                                                                         |
|     DL_STATE:              |     Contains   the consumer’s driver’s license state or province code.  Valid state and province codes can be found   here.                                                                                                                                                                                                                                                                                                                                                        |
|     DL_NUMBER:             |     Contains   the consumer’s driver’s license number.    The driver’s   license numbercan be up to 50 alpha-numeric characters.                                                                                                                                                                                                                                                                                                                                                   |
|     COURTESY_CARD_ID:      |     Contains   the consumer’s courtesy card ID. The Courtesy   Card ID can be up to 50 alpha-numeric characters. This is a number   generated by the merchant for the specific customer.                                                                                                                                                                                                                                                                                           |
|     IDENTITY:              |     Contains   all of the possible elements available for identifying the consumer. The   IDENTITY element can only contain one child element. If the XML data packet   template provided in the terminal settings contains more than one child element,   than one element must be populated with a value and the other elements   removed from the XML data packet prior to submitting it to the Authorization   Gateway.                                                        |
|     SSN4:                  |     Contains   the last four digits of the consumer’s social security number. The SSN4   must be 4 numeric characters.                                                                                                                                                                                                                                                                                                                                                             |
|     DOB_YEAR:              |     Contains   the date of birth of the consumer. The date   of birthmust be 4 numeric characters begin with either 19 or 20.                                                                                                                                                                                                                                                                                                                                                      |
|     CHECK:                 |     Contains   all of the elements for the check.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|     CHECK_AMOUNT:          |     Contains   the amount of the check and should be between $0.01 and $999999.99.                                                                                                                                                                                                                                                                                                                                                                                                 |
|     IMAGE_FRONT:           |     Contains   the image data for the check front. Image data must be base64.                                                                                                                                                                                                                                                                                                                                                                                                      |
|     SIZE:                  |     The   size attribute contains the image size in bytes. The size can be expressed as   a decimal.                                                                                                                                                                                                                                                                                                                                                                               |
|     TYPE:                  |     The   type attribute contains the content type of the image. Valid   TYPE values are “tiff”.                                                                                                                                                                                                                                                                                                                                                                                   |
|     IMAGE_BACK:            |     Contains   the image data for the check back. Image data must be base64.                                                                                                                                                                                                                                                                                                                                                                                                       |
|     SIZE:                  |     The   size attribute contains the image size in bytes. The size can be expressed as   a decimal.                                                                                                                                                                                                                                                                                                                                                                               |
|     TYPE:                  |     The   type attribute contains the content type of the image. Valid   TYPE values are “tiff”.                                                                                                                                                                                                                                                                                                                                                                                   |
|     MRDCIMGCOUNT:          |     This   is an optional element for transactions that have an SEC code of POP or   Check21. NOTE:  Please view POP or Check21   XSD’s for implementation.                                                                                                                                                                                                                                                                                                                        |
|     CUSTOM1- CUSTOM4:      |     These   are optional elements that can contain up to 50 alpha numeric   characters.  We will return this in   reporting.                                                                                                                                                                                                                                                                                                                                                       |
### **Authorization Gateway XML Data Packet with Token Example**:
This XML data packet example contains all available elements. The elements and data types that are required for a specific terminal are defined in that terminal’s XSD.
```
<?xml version=”1.0” encoding=”utf-8”?>
<AUTH_GATEWAY REQUEST_ID=”4654”>
<TRANSACTION>
<TRANSACTION_ID>0a4f529d-70fd-4ddb-b909b5598dc07579</TRANSACTION_ID>
<MERCHANT>
<TERMINAL_ID>1113</TERMINAL_ID>
    </MERCHANT>
    <PACKET>
<IDENTIFIER>A</IDENTIFIER>
<CONTROL_CHAR>S</CONTROL_CHAR>
<VERIFICATION_ONLY>false</VERIFICATION_ONLY>
<ACCOUNT>
    <TOKEN>05944FB3E1DA4663868455AF630F45BE</TOKEN>
    <CHECK_NUMBER>4456</CHECK_NUMBER>
</ACCOUNT>
<CONSUMER>
    <FIRST_NAME>Test</FIRST_NAME>
 <LAST_NAME>Guy</LAST_NAME>
 <ADDRESS1>1001 Test Ave.</ADDRESS1>
 <ADDRESS2>#200</ADDRESS2>
 <CITY>Destin</CITY>
 <STATE>FL</STATE>
 <ZIP>32540</ZIP>
 <PHONE_NUMBER>2345678912</PHONE_NUMBER>
 <DL_STATE>FL</DL_STATE>
 <DL_NUMBER>D12346544</DL_NUMBER>
 <COURTESY_CARD_ID></COURTESY_CARD_ID>
 <IDENTITY>
     <DOB_YEAR>1961</DOB_YEAR>
     </IDENTITY>
   </CONSUMER>
   <CHECK>
   <CHECK_AMOUNT>1.25</CHECK_AMOUNT>
   <IMAGE_FRONT />
   <IMAGE_BACK />
   </CHECK>
<CUSTOM>
        <CUSTOM1></CUSTOM1>
        <CUSTOM2></CUSTOM2>
        <CUSTOM3></CUSTOM3>
        <CUSTOM4></CUSTOM4>
       </CUSTOM>
         </PACKET>
       </TRANSACTION>
 </AUTH_GATEWAY>
```
### **The Authorization Gateway XML data packet may contain the following elements:**

|                            |                                                                                                                                                                                                                                                                                                                                                                                                                                |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     AUTH_GATEWAY:          |     Is   the parent element and contains all other elements within the Terminal   Settings XML document.                                                                                                                                                                                                                                                                                                                       |
|     REQUEST_ID:            |     Is   an optional attribute that contains a unique user defined ID to identify the   authorization gateway request. The Request ID will be returned in the   Authorization Gateway response.                                                                                                                                                                                                                                |
|     TRANSACTION:           |      Contains all of the   elements for a given transaction.                                                                                                                                                                                                                                                                                                                                                                   |
|     TRANSACTION_ID:        |     Is   an optional element that contains a unique user defined ID to identify the   transaction. The Transaction ID will be returned in the Authorization Gateway   response.                                                                                                                                                                                                                                                |
|     MERCHANT:              |     Contains   all of the elements for the merchant.                                                                                                                                                                                                                                                                                                                                                                           |
|     TERMINAL_ID:           |      Contains the ID for   the Terminal. The Terminal ID will be numeric value.                                                                                                                                                                                                                                                                                                                                                |
|     PACKET:                |     Contains   all of the elements for packet.                                                                                                                                                                                                                                                                                                                                                                                 |
|     IDENTIFIER:            |     Contains   a value that identifies the packet being sent as an Authorization, Void,   Override, or Payroll transaction. The identifier is a single alpha character.   A list   of identifiers follows, but valid identifiers will vary by schema.   A=Authorize, R=Recurring, V=Void, F = Reversal, O=Override, P=Payroll                                                                                                  |
|     NOTE:                  |     Identifier   R (Recurring) is in name only. We will NOT run the transaction more than   once.                                                                                                                                                                                                                                                                                                                              |
|     CONTROL_CHAR:          |     Contains   a value that identifies the information in the packet as being entered   manually or retrieved from a check reader.    Valid   control charactersare as follows: M=Manual, S=Swipe.                                                                                                                                                                                                                             |
|     VERIFICATION_ONLY:     |     Contains   a true or false value identifying if the transaction should be processed as   verification only.  NOTE: The Boolean   data type in the XSD will require that true/false be all lower case.                                                                                                                                                                                                                      |
|     ACCOUNT:               |     Contains   all of the elements for a given account.                                                                                                                                                                                                                                                                                                                                                                        |
|     TOKEN:                 |     Contains   a 32 character alphanumeric value all uppercase.  NOTE: This token replaces the account type,   routing number, and account number.                                                                                                                                                                                                                                                                             |
|     CHECK_NUMBER:          |     Contains   the keyed in check number. Valid check   numbers should be between 1 and 15 characters.                                                                                                                                                                                                                                                                                                                         |
|     CONSUMER:              |     Contains   all of the elements for a given consumer.                                                                                                                                                                                                                                                                                                                                                                       |
|     FIRST_NAME:            |     Contains   the first name of the consumer.  The First   Namecan be up to 100 alpha characters.                                                                                                                                                                                                                                                                                                                             |
|     LAST_NAME:             |     Contains   the last name of the consumer. The Last   Namecan be up to 100 alpha characters.                                                                                                                                                                                                                                                                                                                                |
|     ADDRESS1:              |     Contains   the first line of the consumer’s address. The Address1   can be up to 200 alpha-numeric characters and can include the   following:  # , - , : , ;                                                                                                                                                                                                                                                              |
|     ADDRESS2:              |     Contains   the second line of the consumer’s address. The Address2   can be up to 200 alpha-numeric characters and can include the   following:  # , - , : , ;                                                                                                                                                                                                                                                             |
|     CITY:                  |     Contains   the city of the consumer’s address. The City   can be up to 50 alpha characters.                                                                                                                                                                                                                                                                                                                                |
|     STATE:                 |     Contains   the state or province of the consumer’s address. Valid state and province   codes can be found here.                                                                                                                                                                                                                                                                                                            |
|     ZIP:                   |     Contains   the zip code of the consumer’s address.                                                                                                                                                                                                                                                                                                                                                                         |
|     PHONE_NUMBER:          |     Contains   the consumer’s contact phone number. The phone   numberis expected as a 10 digit number without a – or ().                                                                                                                                                                                                                                                                                                      |
|     DL_STATE:              |     Contains   the consumer’s driver’s license state or province code.  Valid state and province codes can be found   here.                                                                                                                                                                                                                                                                                                    |
|     DL_NUMBER:             |     Contains   the consumer’s driver’s license number.    The driver’s   license numbercan be up to 50 alpha-numeric characters.                                                                                                                                                                                                                                                                                               |
|     COURTSEY_CARD_ID:      |     Contains   the consumer’s courtesy card ID. The Courtesy   Card IDcan be up to 50 alpha-numeric characters. This is a number   generated by the merchant for the specific customer.                                                                                                                                                                                                                                        |
|     IDENTITY:              |     Contains   all of the possible elements available for identifying the consumer. The   IDENTITY element can only contain one child element. If the XML data packet   template provided in the terminal settings contains more than one child   element, than one element must be populated with a value and the other   elements removed from the XML data packet prior to submitting it to the   Authorization Gateway.    |
|     SSN4:                  |     Contains   the last four digits of the consumer’s social security number. The SSN4   must be 4 numeric characters.                                                                                                                                                                                                                                                                                                         |
|     DOB_YEAR:              |     Contains   the date of birth of the consumer. The date   of birthmust be 4 numeric characters begin with either 19 or 20.                                                                                                                                                                                                                                                                                                  |
|     CHECK:                 |     Contains   all of the elements for the check.                                                                                                                                                                                                                                                                                                                                                                              |
|     CHECK_AMOUNT:          |     Contains   the amount of the check and should be between $0.01 and $999999.99.                                                                                                                                                                                                                                                                                                                                             |
|     IMAGE_FRONT:           |     Contains   the image data for the check front. Image data must be base64.                                                                                                                                                                                                                                                                                                                                                  |
|     SIZE:                  |     The   size attribute contains the image size in bytes. The size can be expressed as   a decimal.                                                                                                                                                                                                                                                                                                                           |
|     TYPE:                  |     The   type attribute contains the content type of the image. Valid   TYPE values are “tiff”.                                                                                                                                                                                                                                                                                                                               |
|     IMAGE_BACK:            |     Contains   the image data for the check back. Image data must be base64.                                                                                                                                                                                                                                                                                                                                                   |
|     SIZE:                  |     The   size attribute contains the image size in bytes. The size can be expressed as   a decimal.                                                                                                                                                                                                                                                                                                                           |
|     TYPE:                  |     The   type attribute contains the content type of the image. Valid   TYPE valuesare “tiff”.                                                                                                                                                                                                                                                                                                                                |
|     MRDCIMGCOUNT:          |     This   is an optional element for transactions that have an SEC code of POP or   Check21. NOTE:  Please view POP or   Check21 XSD’s for implementation.                                                                                                                                                                                                                                                                    |
|     CUSTOM1- CUSTOM4:      |     These   are optional elements that can contain up to 50 alpha numeric   characters.  We will return this in   reporting.                                                                                                                                                                                                                                                                                                   |
## **XML Templates** 

### **Standard XML Templates**
A matrix of the available XML Templates for each SEC code can be found below. The grid contains the name of the XML Template, based on the XML Templates determining criteria, and a link to the actual XML Template. 

PPD XML Templates	
(Root path:  https://demo.eftchecks.com/webservices/schemas/ppd/templates)
|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     [CheckNoVerificationDLOptional.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckNoVerificationDLOptional.xml)                      |                        |                         |                      |     1010 / 2010                                   |
|     [CheckNoVerificationDLRequired.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckNoVerificationDLRequired.xml)                      |     X                  |                         |                      |     1011 / 2011                                   |
|     [CheckVerificationIdentityVerificationDLOptional.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckVerificationIdentityVerificationDLOptional.xml)    |                        |     X                   |     X                |     1012 / 2012                                   |
|     [CheckVerificationIdentityVerificationDLRequired.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckVerificationIdentityVerificationDLRequired.xml)    |     X                  |     X                   |     X                |     1013 / 2013                                   |
|     [CheckVerificationOnlyDLOptional.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckVerificationOnlyDLOptional.xml)                    |                        |     X                   |                      |     1014 / 2014                                   |
|     [CheckVerificationOnlyDLRequired.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/CheckVerificationOnlyDLRequired.xml)                    |     X                  |     X                   |                      |     1015 / 2015                                   |
|     [IdentityVerificationOnlyDLOptional.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/IdentityVerificationOnlyDLOptional.xml)                 |                        |                         |     X                |     1016 / 2016                                   |
|     [IdentityVerificationOnlyDLRequired.xml](https://demo.eftchecks.com/webservices/schemas/ppd/templates/IdentityVerificationOnlyDLRequired.xml)                 |     X                  |                         |     X                |     1017 / 2017                                   |

### **CCD XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/ccd//templates)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     CheckNoVerificationDLOptional.xml                      |                        |                         |                      |     1710 / 2710                                   |
|     CheckNoVerificationDLRequired.xml                      |     X                  |                         |                      |     1711 / 2711                                   |
|     CheckVerificationIdentityVerificationDLOptional.xml    |                        |     X                   |     X                |     1712 / 2712                                   |
|     CheckVerificationIdentityVerificationDLRequired.xml    |     X                  |     X                   |     X                |     1713 / 2713                                   |
|     CheckVerificationOnlyDLOptional.xml                    |                        |     X                   |                      |     1714 / 2714                                   |
|     CheckVerificationOnlyDLRequired.xml                    |     X                  |     X                   |                      |     1715 / 2715                                   |
|     IdentityVerificationOnlyDLOptional.xml                 |                        |                         |     X                |     1716 / 2716                                   |
|     IdentityVerificationOnlyDLRequired.xml                 |     X                  |                         |     X                |     1717 / 2717                                   |

### **WEB XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/web/templates)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xml                      |                        |                         |                      |     2310                           |
|     CheckNoVerificationDLRequired.xml                      |     X                  |                         |                      |     2311                           |
|     CheckVerificationIdentityVerificationDLOptional.xml    |                        |     X                   |     X                |     2312                           |
|     CheckVerificationIdentityVerificationDLRequired.xml    |     X                  |     X                   |     X                |     2313                           |
|     CheckVerificationOnlyDLOptional.xml                    |                        |     X                   |                      |     2314                           |
|     CheckVerificationOnlyDLRequired.xml                    |     X                  |     X                   |                      |     2315                           |
|     IdentityVerificationOnlyDLOptional.xml                 |                        |                         |     X                |     2316                           |
|     IdentityVerificationOnlyDLRequired.xml                 |     X                  |                         |     X                |     2317                           |

### **TEL XML Templates**	
(Root path:  https://demo.eftchecks.com/webservices/schemas/tel/templates)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     CheckNoVerificationDLOptional.xml                      |                        |                         |                      |     1210 / 2210                                   |
|     CheckNoVerificationDLRequired.xml                      |     X                  |                         |                      |     1211 / 2211                                   |
|     CheckVerificationIdentityVerificationDLOptional.xml    |                        |     X                   |     X                |     1212 / 2212                                   |
|     CheckVerificationIdentityVerificationDLRequired.xml    |     X                  |     X                   |     X                |     1213 / 2213                                   |
|     CheckVerificationOnlyDLOptional.xml                    |                        |     X                   |                      |     1214 / 2214                                   |
|     CheckVerificationOnlyDLRequired.xml                    |     X                  |     X                   |                      |     1215 / 2215                                   |
|     IdentityVerificationOnlyDLOptional.xml                 |                        |                         |     X                |     1216 / 2216                                   |
|     IdentityVerificationOnlyDLRequired.xml                 |     X                  |                         |     X                |     1217 / 2217                                   |

### **POP XML Templates	**
(Root path:  https://demo.eftchecks.com/webservices/schemas/pop/templates)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xml                      |                        |                         |                      |     1110                           |
|     CheckNoVerificationDLRequired.xml                      |     X                  |                         |                      |     1111                           |
|     CheckVerificationIdentityVerificationDLOptional.xml    |                        |     X                   |     X                |     1112                           |
|     CheckVerificationIdentityVerificationDLRequired.xml    |     X                  |     X                   |     X                |     1113                           |
|     CheckVerificationOnlyDLOptional.xml                    |                        |     X                   |                      |     1114                           |
|     CheckVerificationOnlyDLRequired.xml                    |     X                  |     X                   |                      |     1115                           |
|     IdentityVerificationOnlyDLOptional.xml                 |                        |                         |     X                |     1116                           |
|     IdentityVerificationOnlyDLRequired.xml                 |     X                  |                         |     X                |     1117                           |

### **Check21 XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/c21/templates)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xml                      |                        |                         |                      |     1610                           |
|     CheckNoVerificationDLRequired.xml                      |     X                  |                         |                      |     1611                           |
|     CheckVerificationIdentityVerificationDLOptional.xml    |                        |     X                   |     X                |     1612                           |
|     CheckVerificationIdentityVerificationDLRequired.xml    |     X                  |     X                   |     X                |     1613                           |
|     CheckVerificationOnlyDLOptional.xml                    |                        |     X                   |                      |     1614                           |
|     CheckVerificationOnlyDLRequired.xml                    |     X                  |     X                   |                      |     1615                           |
|     IdentityVerificationOnlyDLOptional.xml                 |                        |                         |     X                |     1616                           |
|     IdentityVerificationOnlyDLRequired.xml                 |     X                  |                         |     X                |     1617                           |

## **XML Templates using Tokens**

A matrix of the available XML Templates when using tokens for each SEC code can be found below. Each grid contains the name of the XML Template, based on the XML Templates determining criteria, and a link to the actual XML Template. 

### **PPD XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/ppd/templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     1010 / 2010                                   |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     1011 / 2011                                   |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     1012 / 2012                                   |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     1013 / 2013                                   |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     1014 / 2014                                   |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     1015 / 2015                                   |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     1016 / 2016                                   |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     1017 / 2017                                   |

### **CCD XML Templates	**
(Root path:  https://demo.eftchecks.com/webservices/schemas/ccd//templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     1710 / 2710                                   |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     1711 / 2711                                   |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     1712 / 2712                                   |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     1713 / 2713                                   |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     1714 / 2714                                   |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     1715 / 2715                                   |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     1716 / 2716                                   |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     1717 / 2717                                   |

### **WEB XML Templates	**
(Root path:  https://demo.eftchecks.com/webservices/schemas/web/templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     2310                           |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     2311                           |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     2312                           |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     2313                           |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     2314                           |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     2315                           |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     2316                           |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     2317                           |

### **TEL XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/tel/templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID     (Guar/Non)    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|---------------------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     1210 / 2210                                   |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     1211 / 2211                                   |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     1212 / 2212                                   |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     1213 / 2213                                   |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     1214 / 2214                                   |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     1215 / 2215                                   |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     1216 / 2216                                   |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     1217 / 2217                                   |

### **POP XML Templates**	
(Root path:  https://demo.eftchecks.com/webservices/schemas/pop/templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     1110                           |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     1111                           |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     1112                           |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     1113                           |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     1114                           |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     1115                           |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     1116                           |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     1117                           |

### **Check21 XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/c21/templates)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLWithTokenOptional.xml                      |                        |                         |                      |     1610                           |
|     CheckNoVerificationDLWithTokenRequired.xml                      |     X                  |                         |                      |     1611                           |
|     CheckVerificationIdentityVerificationDLWithTokenOptional.xml    |                        |     X                   |     X                |     1612                           |
|     CheckVerificationIdentityVerificationDLWithTokenRequired.xml    |     X                  |     X                   |     X                |     1613                           |
|     CheckVerificationOnlyDLWithTokenOptional.xml                    |                        |     X                   |                      |     1614                           |
|     CheckVerificationOnlyDLWithTokenRequired.xml                    |     X                  |     X                   |                      |     1615                           |
|     IdentityVerificationOnlyDLWithTokenOptional.xml                 |                        |                         |     X                |     1616                           |
|     IdentityVerificationOnlyDLWithTokenRequired.xml                 |     X                  |                         |     X                |     1617                           |

## **OCR XML Templates**
There are two different ways of processing images, one with OCR and one with Mobile OCR.  Images captured by a Mobile Device are handled differently (due to patent restrictions) than images captured via a desktop scanner, such as an RDM, Panini, Magtek or other peripheral device.  Images submitted via a mobile device must be submitted as a .JPG, while images submitted via a peripheral device must be submitted in a .TIFF format.  

Images submitted via a Mobile device, will have the MICR, Courtesy Amount, and Legal Amount recognized by the standard Mobile OCR function.  In order to submit for FULL OCR to receive the additional fields (Payee, Address, Endorsement, etc.), it will be necessary that you code for the full set of OCR responses.    

A matrix of the available XML Templates when using OCR for each SEC code can be found below. Each grid contains the name of the XML Template, based on the XML Templates determining criteria, and a link to the actual XML Template. The Trans Type will indicate what type of response you should receive. NOTE: Verify Check can be applied to any terminal. This will NOT impact the terminal schemas or template.



### Trans Type:
 -	P – Successful Transaction
 -	O -  Successful Transaction with failed optional fields (Fields that are not required to pass the transaction in the system. NOTE: Fields may not match image depending on how OCR translates the image).
 -	F – Failed Transaction
_Note:  Optional values submitted for OCR will be TRUSTED and not validated by OCR  _

### **POP XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/pop/templates)

|     Template                       |     Trans   Type    |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------|---------------------|------------------------|----------------------|------------------------------------|
|     OCR.xml                        |     P               |                        |                      |     4010                           |
|     OCR.xml                        |     O               |                        |                      |     4020                           |
|     OCR.xml                        |     F               |                        |                      |     4030                           |
|     OCR.xml                        |     P               |     X                  |                      |     4011                           |
|     OCR.xml                        |     O               |     X                  |                      |     4021                           |
|     OCR.xml                        |     F               |     X                  |                      |     4031                           |
|     OCRIdentityVerification.xml    |     P               |                        |     X                |     4012                           |
|     OCRIdentityVerification.xml    |     O               |                        |     X                |     4022                           |
|     OCRIdentityVerification.xml    |     F               |                        |     X                |     4032                           |
|     OCRIdentityVerification.xml    |     P               |     X                  |     X                |     4013                           |
|     OCRIdentityVerification.xml    |     O               |     X                  |     X                |     4023                           |
|     OCRIdentityVerification.xml    |     F               |     X                  |     X                |     4033                           |

### **Check21 XML Templates**
(Root path:  https://demo.eftchecks.com/webservices/schemas/c21/templates)

|     Template                        |     Trans   Type    |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|-------------------------------------|---------------------|------------------------|----------------------|------------------------------------|
|      OCR.xml                        |     P               |                        |                      |     4110                           |
|      OCR.xml                        |     O               |                        |                      |     4120                           |
|      OCR.xml                        |     F               |                        |                      |     4130                           |
|      OCR.xml                        |     P               |     X                  |                      |     4111                           |
|      OCR.xml                        |     O               |     X                  |                      |     4121                           |
|      OCR.xml                        |     F               |     X                  |                      |     4131                           |
|      OCRIdentityVerification.xml    |     P               |                        |     X                |     4112                           |
|      OCRIdentityVerification.xml    |     O               |                        |     X                |     4122                           |
|      OCRIdentityVerification.xml    |     F               |                        |     X                |     4132                           |
|      OCRIdentityVerification.xml    |     P               |     X                  |     X                |     4113                           |
|      OCRIdentityVerification.xml    |     O               |     X                  |     X                |     4123                           |
|      OCRIdentityVerification.xml    |     F               |     X                  |     X                |     4133                           |

## **Templates for Mobile**

### **POP XML Teamplates for Mobile** 
Root path:  https://demo.eftchecks.com/webservices/schemas/pop/templates)

|     Template                       |     Trans   Type    |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------|---------------------|------------------------|----------------------|------------------------------------|
|     OCR.xml                        |     P               |                        |                      |     4210                           |
|     OCR.xml                        |     O               |                        |                      |     4220                           |
|     OCR.xml                        |     F               |                        |                      |     4230                           |
|     OCR.xml                        |     P               |     X                  |                      |     4211                           |
|     OCR.xml                        |     O               |     X                  |                      |     4221                           |
|     OCR.xml                        |     F               |     X                  |                      |     4231                           |
|     OCRIdentityVerification.xml    |     P               |                        |     X                |     4212                           |
|     OCRIdentityVerification.xml    |     O               |                        |     X                |     4222                           |
|     OCRIdentityVerification.xml    |     F               |                        |     X                |     4232                           |
|     OCRIdentityVerification.xml    |     P               |     X                  |     X                |     4213                           |
|     OCRIdentityVerification.xml    |     O               |     X                  |     X                |     4223                           |
|     OCRIdentityVerification.xml    |     F               |     X                  |     X                |     4233                           |

### **Check21 XML Templates for Mobile** 
(Root path:  https://demo.eftchecks.com/webservices/schemas/c21/templates)

|     Template                        |     Trans   Type    |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|-------------------------------------|---------------------|------------------------|----------------------|------------------------------------|
|      OCR.xml                        |     P               |                        |                      |     4310                           |
|      OCR.xml                        |     O               |                        |                      |     4320                           |
|      OCR.xml                        |     F               |                        |                      |     4330                           |
|      OCR.xml                        |     P               |     X                  |                      |     4311                           |
|      OCR.xml                        |     O               |     X                  |                      |     4321                           |
|      OCR.xml                        |     F               |     X                  |                      |     4331                           |
|      OCRIdentityVerification.xml    |     P               |                        |     X                |     4312                           |
|      OCRIdentityVerification.xml    |     O               |                        |     X                |     4322                           |
|      OCRIdentityVerification.xml    |     F               |                        |     X                |     4332                           |
|      OCRIdentityVerification.xml    |     P               |     X                  |     X                |     4313                           |
|      OCRIdentityVerification.xml    |     O               |     X                  |     X                |     4323                           |
|      OCRIdentityVerification.xml    |     F               |     X                  |     X                |     4333                           |

## **How to determine which XSD to Use**
The XSD that will be used can be retrieved from the Terminal Settings, but can also be determined by using the criteria below.  

The root path for all XSDs is http://demo.eftchecks.com/webservices/Schemas followed by the SEC Code and Schema Name. The Schema Name is determined by the following criteria:

 - If the Terminal requires the Driver’s License Information. 
 - If the Terminal is configured for Check Verification.
 - If the Terminal is configured for Identity Verification.
 - For PPD and CCD entries, If the Terminal is configured to allow Credit entries

A matrix of the available XSDs for each SEC code can be found below. Each grid contains the name of the schema, based on the schemas determining criteria, and a link to the actual schema.  The grid also includes the Terminal IDs that can be used for testing and certifying against the provided schema.

An example XSD file path for a PPD terminal that does not require the driver’s license information, is setup for check verification, and is setup for identity verification, and does not allow credits would be as follows: 
https://demo.eftchecks.com/webservices/schemas/ppd/CheckVerificationIdentityVerificationDLOptional.xsd

## **Standard XSD Schemas**
A matrix of the available XSDs can be found below. Each grid contains the name of the schema, based on the schemas determining criteria, and a link to the actual schema.  The grid also includes the Terminal IDs that can be used for testing and certifying against the provided schema.

### **PPD Schemas – Guaranteed**
(Root path:  http://demo.eftchecks.com/webservices/Schemas/ppd/)

|     Template                                                     |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|    ** Debit   Only Transactions **                                   |                        |                         |                      |                                    |
|     CheckNoVerificationDLOptional.xsd                            |                        |                         |                      |     1010                           |
|     CheckNoVerificationDLRequired.xsd                            |     X                  |                         |                      |     1011                           |
|     CheckVerificationIdentityVerificationDLOptional.xsd          |                        |     X                   |     X                |     1012                           |
|     CheckVerificationIdentityVerificationDLRequired.xsd          |     X                  |     X                   |     X                |     1013                           |
|     CheckVerificationOnlyDLOptional.xsd                          |                        |     X                   |                      |     1014                           |
|     CheckVerificationOnlyDLRequired.xsd                          |     X                  |     X                   |                      |     1015                           |
|     IdentityVerificationOnlyDLOptional.xsd                       |                        |                         |     X                |     1016                           |
|     IdentityVerificationOnlyDLRequired.xsd                       |     X                  |                         |     X                |     1017                           |
|     **Credit   & Debit Transactions**                                |                        |                         |                      |                                    |
|     CreditCheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     1810                           |
|     CreditCheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     1811                           |
|     CreditCheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     1812                           |
|     CreditCheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     1813                           |
|     CreditCheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     1814                           |
|     CreditCheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     1815                           |
|     CreditIdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     1816                           |
|     CreditIdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     1817                           |

### **Schemas – Non-Guaranteed**
(Root path:  http://demo.eftchecks.com/webservices/Schemas/ppd/)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     **Debit   Only Transactions**                                       |                        |                         |                      |                                    |
|     Ng_CheckNoVerificationDLOptional.xsd                            |                        |                         |                      |     2010                           |
|     Ng_CheckNoVerificationDLRequired.xsd                            |     X                  |                         |                      |     2011                           |
|     Ng_CheckVerificationIdentityVerificationDLOptional.xsd          |                        |     X                   |     X                |     2012                           |
|     Ng_CheckVerificationIdentityVerificationDLRequired.xsd          |     X                  |     X                   |     X                |     2013                           |
|     Ng_CheckVerificationOnlyDLOptional.xsd                          |                        |     X                   |                      |     2014                           |
|     Ng_CheckVerificationOnlyDLRequired.xsd                          |     X                  |     X                   |                      |     2015                           |
|     Ng_IdentityVerificationOnlyDLOptional.xsd                       |                        |                         |     X                |     2016                           |
|     Ng_IdentityVerificationOnlyDLRequired.xsd                       |     X                  |                         |     X                |     2017                           |
|     **Credit   & Debit Transactions**                                   |                        |                         |                      |                                    |
|     Ng_CreditCheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     2810                           |
|     Ng_CreditCheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     2811                           |
|     Ng_CreditCheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     2812                           |
|     Ng_CreditCheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     2813                           |
|     Ng_CreditCheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     2814                           |
|     Ng_CreditCheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     2815                           |
|     Ng_CreditIdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     2816                           |
|     Ng_CreditIdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     2817                           |
### **CCD Schemas – Guaranteed**
(Root path:  http://demo.eftchecks.com/webservices/Schemas/ccd/)

|     Template                                                     |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|   **Debit   Only Transactions**                                    |                        |                         |                      |                                    |
|     CheckNoVerificationDLOptional.xsd                            |                        |                         |                      |     1710                           |
|     CheckNoVerificationDLRequired.xsd                            |     X                  |                         |                      |     1711                           |
|     CheckVerificationIdentityVerificationDLOptional.xsd          |                        |     X                   |     X                |     1712                           |
|     CheckVerificationIdentityVerificationDLRequired.xsd          |     X                  |     X                   |     X                |     1713                           |
|     CheckVerificationOnlyDLOptional.xsd                          |                        |     X                   |                      |     1714                           |
|     CheckVerificationOnlyDLRequired.xsd                          |     X                  |     X                   |                      |     1715                           |
|     IdentityVerificationOnlyDLOptional.xsd                       |                        |                         |     X                |     1716                           |
|     IdentityVerificationOnlyDLRequired.xsd                       |     X                  |                         |     X                |     1717                           |
|     **Credit   & Debit Transactions**                                |                        |                         |                      |                                    |
|     CreditCheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     1910                           |
|     CreditCheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     1911                           |
|     CreditCheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     1912                           |
|     CreditCheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     1913                           |
|     CreditCheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     1914                           |
|     CreditCheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     1915                           |
|     CreditIdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     1916                           |
|     CreditIdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     1917                           |

CCD Schemas – Non-Guaranteed
(Root path:  http://demo.eftchecks.com/webservices/Schemas/ccd/)

|     Template                                                        |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     Debit   Only Transactions                                       |                        |                         |                      |                                    |
|     Ng_CheckNoVerificationDLOptional.xsd                            |                        |                         |                      |     2710                           |
|     Ng_CheckNoVerificationDLRequired.xsd                            |     X                  |                         |                      |     2711                           |
|     Ng_CheckVerificationIdentityVerificationDLOptional.xsd          |                        |     X                   |     X                |     2712                           |
|     Ng_CheckVerificationIdentityVerificationDLRequired.xsd          |     X                  |     X                   |     X                |     2713                           |
|     Ng_CheckVerificationOnlyDLOptional.xsd                          |                        |     X                   |                      |     2714                           |
|     Ng_CheckVerificationOnlyDLRequired.xsd                          |     X                  |     X                   |                      |     2715                           |
|     Ng_IdentityVerificationOnlyDLOptional.xsd                       |                        |                         |     X                |     2716                           |
|     Ng_IdentityVerificationOnlyDLRequired.xsd                       |     X                  |                         |     X                |     2717                           |
|     Credit   & Debit Transactions                                   |                        |                         |                      |                                    |
|     Ng_CreditCheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     2910                           |
|     Ng_CreditCheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     2911                           |
|     Ng_CreditCheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     2912                           |
|     Ng_CreditCheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     2913                           |
|     Ng_CreditCheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     2914                           |
|     Ng_CreditCheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     2915                           |
|     Ng_CreditIdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     2916                           |
|     Ng_CreditIdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     2917                           |

### **WEB Schemas** 
(Root path:  http://demo.eftchecks.com/webservices/Schemas/web/)

|     Template                                                  |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     Ng_CheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     2310                           |
|     Ng_CheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     2311                           |
|     Ng_CheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     2312                           |
|     Ng_CheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     2313                           |
|     Ng_CheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     2314                           |
|     Ng_CheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     2315                           |
|     Ng_IdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     2316                           |
|     Ng_IdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     2317                           |

### **TEL Schemas – Guaranteed**
(Root path:  http://demo.eftchecks.com/webservices/Schemas/tel/)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     1210                           |
|     CheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     1211                           |
|     CheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     1212                           |
|     CheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     1213                           |
|     CheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     1214                           |
|     CheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     1215                           |
|     IdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     1216                           |
|     IdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     1217                           |

### **TEL Schemas – Non-Guaranteed**
(Root path:  http://demo.eftchecks.com/webservices/Schemas/tel/)

|     Template                                                  |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|---------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     Ng_CheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     2210                           |
|     Ng_CheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     2211                           |
|     Ng_CheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     2212                           |
|     Ng_CheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     2213                           |
|     Ng_CheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     2214                           |
|     Ng_CheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     2215                           |
|     Ng_IdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     2216                           |
|     Ng_IdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     2217                           |

### **POP Schemas** 
(Root path:  http://demo.eftchecks.com/webservices/Schemas/pop/)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     1110                           |
|     CheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     1111                           |
|     CheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     1112                           |
|     CheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     1113                           |
|     CheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     1114                           |
|     CheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     1115                           |
|     IdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     1116                           |
|     IdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     1117                           |

### **Check21 Schemas** 
(Root path:  http://demo.eftchecks.com/webservices/Schemas/c21/)

|     Template                                               |     DL     Required    |     Verify     Check    |     Verify     ID    |     Certification   Terminal ID    |
|------------------------------------------------------------|------------------------|-------------------------|----------------------|------------------------------------|
|     CheckNoVerificationDLOptional.xsd                      |                        |                         |                      |     1610                           |
|     CheckNoVerificationDLRequired.xsd                      |     X                  |                         |                      |     1611                           |
|     CheckVerificationIdentityVerificationDLOptional.xsd    |                        |     X                   |     X                |     1612                           |
|     CheckVerificationIdentityVerificationDLRequired.xsd    |     X                  |     X                   |     X                |     1613                           |
|     CheckVerificationOnlyDLOptional.xsd                    |                        |     X                   |                      |     1614                           |
|     CheckVerificationOnlyDLRequired.xsd                    |     X                  |     X                   |                      |     1615                           |
|     IdentityVerificationOnlyDLOptional.xsd                 |                        |                         |     X                |     1616                           |
|     IdentityVerificationOnlyDLRequired.xsd                 |     X                  |                         |     X                |     1617                           |

## **OCR XSD Schemas**
A matrix of the available OCR XSDs can be found below. Each grid contains the name of the schema, based on the schemas determining criteria, and a link to the actual schema.  The grid also includes the Terminal IDs that can be used for testing and certifying against the provided schema. NOTE: Verify Check can be applied to any terminal. This will NOT impact the terminal schemas or template.

### **POP Schemas** 
(Root path:  http://demo.eftchecks.com/webservices/Schemas/pop/)

|     Template                                 |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|----------------------------------------------|------------------------|----------------------|------------------------------------|
|     OCRDLOptional.xsd                        |                        |                      |     4010/4020/4030/4210            |
|     OCRDLRequired.xsd                        |     X                  |                      |     4011/4021/4031/4211            |
|     OCRIdentityVerificationDLOptional.xsd    |                        |     X                |     4012/4022/4032/4212            |
|     OCRIdentityVerificationDLRequired.xsd    |     X                  |     X                |     4013/4023/4033/4213            |

### **Check21 Schemas** 
(Root path:  http://demo.eftchecks.com/webservices/Schemas/c21/)

|     Template                                 |     DL     Required    |     Verify     ID    |     Certification   Terminal ID    |
|----------------------------------------------|------------------------|----------------------|------------------------------------|
|     OCRDLOptional.xsd                        |                        |                      |     4110/4120/4130/4310            |
|     OCRDLRequired.xsd                        |     X                  |                      |     4111/4121/4131/4311            |
|     OCRIdentityVerificationDLOptional.xsd    |                        |     X                |     4112/4122/4132/4312            |
|     OCRIdentityVerificationDLRequired.xsd    |     X                  |     X                |     4113/4123/4133/4313            |

## **Data Types**
Each element in the XML data packet that is sent to the Authorization Gateway has a data type that defines the format of the data contained within the element.  The Terminal’s XSD defines which elements are of what data type. A list and links to the available data types is located below.
 - States and Provinces
https://demo.eftchecks.com/Webservices/schemas/types/StatesAndProvincesSimpleType.xsd
 - Authorization Gateway Types
https://demo.eftchecks.com/Webservices/schemas/types/AuthGatewayTypes.xsd 
 - Authorization Gateway Response Types
https://demo.eftchecks.com/Webservices/schemas/types/AuthGatewayResponseTypes.xsd

## **Responses**
Each web method in the Authorization Gateway will return an XML string and detail the success or failure of the submission.  If the transaction is accepted (authorized) an authorization number will be returned at a minimum.

The Authorization Gateway XML response may contain the following elements:
 - **REQUEST_ID**:  Is an attribute that contains a unique user defined ID to identify the authorization gateway request. The Request ID contained in the Authorization Gateway Request is returned in the Authorization Gateway response.
 - **VALIDATION_MESSAGE**:  Contains all of the elements in the validation message. 
 - **AUTHORIZATION_MESSAGE**:  Contains all of the elements in the authorization message.
 
_NOTE: The AuthGatewayCertification web method response will not contain this element._

### Validation Message Response
The AuthGatewayCertification, ProcessSingleCheck, and ProcessSingleCheckWithToken web methods will validate that the interface is sending a data packet that conforms to its schema. 
Validation Message Example – Success Response
```
<?xml version=”1.0” encoding=”utf-8”?>
<RESPONSE xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” xmlns:xsd=”http://www.w3.org/2001/XMLSchema” REQUEST_ID=”4654”>
                <VALIDATION_MESSAGE>
<RESULT>Passed</RESULT>
<SCHEMA_FILE_PATH>http://demo.eftchecks.com/webservices/Schemas/WEB/CheckNoVerificationDLOptional.xsd
</SCHEMA_FILE_PATH>
                </VALIDATION_MESSAGE>
</RESPONSE>
```

### **Validation Message Example – Failure Response**

This data packet failed validation because the Driver’s License Information is required by the XSD and was not provided in the data packet.
```
<<?xml version=”1.0” encoding=”utf-8” ?> 
       <RESPONSE xmlns:xsd=”http://www.w3.org/2001/XMLSchema” xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” REQUEST_ID=”4654”>>
    <VALIDATION_MESSAGE>
          <RESULT>Failed</RESULT> 
                <SCHEMA_FILE_PATH>
                         http://localhost/GETI.eMagnus.WebServices/Schemas/PPD/
                         CheckNoVerificationDLRequired.xsd
                </SCHEMA_FILE_PATH> 
               <VALIDATION_ERROR LINE_NUMBER=”1” LINE_POSITION=”561” >
               <SEVERITY>Error</SEVERITY> 
               <MESSAGE>
                              The ‘DL_STATE’ element has an invalid value according to its data
                               type. An error occurred at (1, 561).
                          </MESSAGE> 
           </VALIDATION_ERROR>
               <VALIDATION_ERROR LINE_NUMBER=”1” LINE_POSITION=”583”>
               <SEVERITY>Error</SEVERITY> 
               <MESSAGE>
                               The ‘IDENTIFIER’ element has an invalid value according to its data      
                                type.
                         </MESSAGE> 
           </VALIDATION_ERROR>
      </VALIDATION_MESSAGE>
   </RESPONSE>
```

## **The Validation Message may contain the following elements and attributes**:

|     RESULT              |     Contains Passed   or Failed indicating if the validation was successful or not.                                                                                           |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     SCHEMA_FILE_PATH    |     Contains the   Uniform Resource Identifier (URI) specifying the published XML Schema   Definition (XSD) that the data packet request will be validated against.           |
|     VALIDATION_ERROR    |     Contains all of   the elements in the validation error.                                                                                                                   |
|     LINE_NUMBER         |     Contains the line   the number where the validation error occurred.                                                                                                       |
|     LINE_POSITION       |     Contains the line   position where the validation error occurred.                                                                                                         |
|     SEVERITY            |     Contains warning   or error indicating the severity of the validation error.                                                                                              |
|     MESSAGE             |     Contains the   complete validation error message and will include the element that failed   the validation and may contain the location the validation error occurred.    |

## Authorization Message Response
The ProcessSingleCheck web method will process a valid XML data packet and return an Authorization Message within the response. An example of the Authorization message is below.

### Authorization Message Example 
```
<?xml version=”1.0” encoding=”utf-8” ?> 
<RESPONSE xmlns:xsd=”http://www.w3.org/2001/XMLSchema”xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” REQUEST_ID=”4654”>
<VALIDATION_MESSAGE>
<RESULT>Passed</RESULT> 
<SCHEMA_FILE_PATH>http://localhost/GETI.eMagnus.WebServices/Schemas/PPD/CheckVerificationIdentityVerificationDLRequired.xsd</SCHEMA_FILE_PATH> 
</VALIDATION_MESSAGE>
<AUTHORIZATION_MESSAGE>
<TRANSACTION_ID>0a4f529d-70fd-4ddb-b909-b5598dc07579</TRANSACTION_ID> 
<RESPONSE_TYPE>A</RESPONSE_TYPE> 
<RESPONSE_TYPE_TEXT>APPROVED</RESPONSE_TYPE_TEXT> 
<RESULT_CODE>0</RESULT_CODE> 
<TYPE_CODE>4096</TYPE_CODE> 
<CODE>AUTH NUM 272-172</CODE> 
<MESSAGE>APPROVAL</MESSAGE> 
</AUTHORIZATION_MESSAGE>
</RESPONSE>
```
### **The Authorization Message may contain the following elements**:

|     TRANSACTION_ID        |     Contains   the unique user defined ID to identify the packet. The Transaction ID   provided for a given transaction in the data packet is returned in the   authorization message in the response.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     RESPONSE_TYPE         |     Contains   an identifier that will give your host system a general overview of the   processed transaction, and the RESPONSE_TYPE_TEXT element will contain the   full text description of the identifier contained in the RESPONSE_TYPE   element. The RESULT_CODE should be the primary driver for determining how the   host system should act in response to various responses from the   Authorization Gateway, but the values in the RESPONSE_TYPE can be used to   determine additional information for processing by the host system. This   includes determining if a transaction was processed as Verification   Only.  A complete list of response   types is located in the Authorization   Response Types XSD.    |
|     RESPONSE_TYPE_TEXT    |     Contains   the full text description of the response type identifier.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|     RESULT_CODE           |     Contains   a numeric bit that indicates one or many result messages. Examples of result   messages are Approved, Decline, or Unpaid Check Limit Exceeded.  A complete list of result codes is located   in the Authorization   Response Types XSD.  The host system should conduct a bit   comparison of the RESULT_CODE to determine exactly how the transaction was   processed and from there can determine exactly what action to take if any.                                                                                                                                                                                                                                                                             |
|     TYPE_CODE             |     Contains   a numeric bit that indicates one or many type messages.  The host system should conduct a bit   comparison of the TYPE_CODE element to determine additional detailed   information about the transaction which can be provided to the user.  Examples of type messages are Personal   Check, Business Check, or Voided Check. A complete list of type codes is   located in the Authorization   Response Types XSD.                                                                                                                                                                                                                                                                                                 |
|     CODE                  |     Contains   the text message with the Authorization Number if the transaction was   approved or additional information if the transaction was not approved.  The CODE element should be used by the host   system to display and record any authorization numbers or additional   information.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|     MESSAGE               |     Contains   additional text and should be used by the host system to display and record   any additional information about the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

### **Process Single Certification Check – Authorization**
When processing a single certification check for Authorization you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000018 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.  If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  A
 - RESPONSE_TYPE_TEXT:  APPROVED
 - RESULT_CODE:  0
 - TYPE_CODE:  4096
 - CODE:  AUTH NUM 272-172
 - MESSAGE:   APPROVAL

Again, the host system should first check to make sure the RESULT child element of the VALIDATION_MESSAGE is set to “Passed”.  If the request XML Data Packet passed validation it was successfully processed, and the above elements will be present as child elements of the AUTHORIZATION_MESSAGE element.  The host system should store all of the returned data and at a minimum conduct a bit comparison of the value in the RESULT_CODE element. If the value in the RESULT_CODE is 0 then the transaction has been approved. The response Data Packet also contains a value of 4096 for the TYPE_CODE element which indicates an Internal Override which in this instance means that Authorization Gateway returned a predetermined fixed response.  

## **Authorization Message Response with Token**
The ProcessSingleCheckWithToken web method will process a valid XML data packet and return an Authorization Message within the response. An example of the Authorization message is below.

### **Authorization Message Example with Token**
```
<?xml version=”1.0” encoding=”utf-8” ?> 
<RESPONSE xmlns:xsd=”http://www.w3.org/2001/XMLSchema”xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” REQUEST_ID=”4654”>
<VALIDATION_MESSAGE>
<RESULT>Passed</RESULT> 
<SCHEMA_FILE_PATH>http://localhost/GETI.eMagnus.WebServices/Schemas/PPD/CheckVerificationIdentityVerificationDLRequired.xsd</SCHEMA_FILE_PATH> 
</VALIDATION_MESSAGE>
<AUTHORIZATION_MESSAGE>
<TRANSACTION_ID>0a4f529d-70fd-4ddb-b909-b5598dc07579</TRANSACTION_ID> 
<RESPONSE_TYPE>A</RESPONSE_TYPE> 
<RESPONSE_TYPE_TEXT>APPROVED</RESPONSE_TYPE_TEXT> 
<RESULT_CODE>0</RESULT_CODE> 
<TYPE_CODE>4096</TYPE_CODE> 
<CODE>AUTH NUM 272-172</CODE> 
<MESSAGE>APPROVAL</MESSAGE>
<TOKEN>C7E057491C4A4D67B617EE512D1300AE</TOKEN> 
</AUTHORIZATION_MESSAGE>
</RESPONSE>
```
### **The Authorization Message may contain the following elements**:

|     TRANSACTION_ID:           |      Contains   the unique user defined ID to identify the packet. The Transaction ID   provided for a given transaction in the data packet is returned in the   authorization message in the response.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     RESPONSE_TYPE:            |     contains an   identifier that will give your host system a general overview of the   processed transaction, and the RESPONSE_TYPE_TEXT element will contain the   full text description of the identifier contained in the RESPONSE_TYPE   element. The RESULT_CODE should be the primary driver for determining how the   host system should act in response to various responses from the   Authorization Gateway, but the values in the RESPONSE_TYPE can be used to   determine additional information for processing by the host system. This   includes determining if a transaction was processed as Verification   Only.  A complete list of response   types is located in the Authorization   Response Types XSD.    |
|     RESPONSE_TYPE_TEXT:       |     Contains the full   text description of the response type identifier.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|     RESULT_CODE:              |     Contains a numeric   bit that indicates one or many result messages. Examples of result messages   are Approved, Decline, or Unpaid Check Limit Exceeded.  A complete list of result codes is located   in the Authorization   Response Types XSD.  The host system should conduct a bit   comparison of the RESULT_CODE to determine exactly how the transaction was   processed and from there can determine exactly what action to take if any.                                                                                                                                                                                                                                                                             |
|     TYPE_CODE:                |     Contains a numeric   bit that indicates one or many type messages.    The host system should conduct a bit comparison of the TYPE_CODE   element to determine additional detailed information about the transaction   which can be provided to the user.    Examples of type messages are Personal Check, Business Check, or   Voided Check. A complete list of type codes is located in the Authorization   Response Types XSD.                                                                                                                                                                                                                                                                                               |
|     CODE:                     |     Contains the text   message with the Authorization Number if the transaction was approved or   additional information if the transaction was not approved.  The CODE element should be used by the host   system to display and record any authorization numbers or additional   information.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|     MESSAGE:                  |     Contains   additional text and should be used by the host system to display and record   any additional information about the transaction.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|     TOKEN:                    |     Contains the   return Token that is used in place of the Account Type, Routing Number, and   Account Number. This token can then be used for future transactions.  NOTE: This is only available   when using a Token or when requesting a Token.                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |

## **Single Certification Check Types**

### **Check Limit Exceeded**
The check limit for processing single certification checks is $25. If a check amount in excess of $25 is sent to the Authorization Gateway during development or certification phases then the Authorization Gateway will return “Check Limit Exceeded – Decline”.

When processing a single certification check for Check Limit Exceeded you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000018 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas. Finally, you will need to include a check amount larger than $25 in the CHECK_AMOUNT element. If the request XML Data Packet is valid then this will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  D
 - RESPONSE_TYPE_TEXT:  DECLINED
 - RESULT_CODE:  136
 - TYPE_CODE:  256
 - CODE:  DECLINE CHECK LIMIT EXCEEDED
 - MESSAGE:   DECLINE CHECK LIMIT EXCEEDED

If a transaction is declined the Authorization Gateway will return an 8 in the RESULT_CODE element which indicates a Decline Message. In the returned response above the RESULT_CODE element has a value of 136. If the host system is setup to do a bit comparison of the value in the RESULT_CODE, you will discover that the 136 is made of an 8 indicating a Decline Message and 128 which indicates that the transaction limit has been exceeded.  The fixed decline response also contains a value of 256 in the TYPE_CODE element. This indicates that the host system was unable to determine if this was the first time the check was presented or if it is a representment.

**Decline**
When processing a single certification check for Decline you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000034 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.   If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  D
 - RESPONSE_TYPE_TEXT:  DECLINED
 - RESULT_CODE:  520
 - TYPE_CODE:  4100
 - CODE:  DECLINE CHECK 5 UNPAID (ALL) AMT=$5478 GLOBAL eTELECOM 888-481-0757
 - MESSAGE:   DECLINE CHECK 5 UNPAID (ALL) AMT=$5478 GLOBAL eTELECOM 888-481-0757

If a transaction is declined the Authorization Gateway will return an 8 in the RESULT_CODE element which indicates a Decline Message. In the returned response above the RESULT_CODE element has a value of 520. If the host system is setup to do a bit comparison of the value in the RESULT_CODE, you will discover that the 520 is made of an 8 indicating a Decline Message and 512 which indicates that the unpaid check Limit has been exceeded.  The fixed decline response also contains a value of 4100 in the TYPE_CODE element. This again indicates an internal override was done because the Authorization Gateway is returning a predetermined fixed response. However, if the host system is setup to conduct a bit comparison on the value of the TYPE_CODE element it can also be determined that TYPE_CODE also contains a 4, which indicates a Business Check was sent for processing.

**Void**
When voiding a previously approved single certification check you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000018, 490000021, or 490000047 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “V”.  This milestone has been built into the development phase so that you can incorporate this functionality into your host system. If the request XML Data Packet is valid then a Void identifier for previously approved transaction with the routing numbers noted above will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  A
 - RESPONSE_TYPE_TEXT:  APPROVED
 - RESULT_CODE:  0
 - TYPE_CODE:  5120
 - CODE: VOID ACCEPTED
 - MESSAGE:  VOID ACCEPTED

You should note that the returned information for a voided transaction contains a RESULT_CODE of 0. This indicates that the void was approved, but it also illustrates the importance of examining the information contained with the TYPE_CODE. If the host systems interface with the Authorization Gateway was only set to interpret the RESULT_CODE, the full meaning of the overall response would be lost. In this case the TYPE_CODE returned in the response XML Data Packet contains 5120. A bit comparison of this value indicates that the value contains 1024 indicating a voided check, and 4096 indicating that there was an internal override due to a predetermined fixed response being returned.  

**Reversal**
When reversing a previously approved single certification check you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000018, 490000021, or 490000047 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “F”.  This milestone has been built into the development phase so that you can incorporate this functionality into your host system. If the request XML Data Packet is valid then a Reversal identifier for previously approved transaction with the routing numbers noted above will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  A
 - RESPONSE_TYPE_TEXT:  APPROVED
 - RESULT_CODE:  0
 - TYPE_CODE:  5120
 - CODE: REVERSAL ACCEPTED
 - MESSAGE:  REVERSAL ACCEPTED

 **Credit**
When processing a single certification check for Authorization you will need to invoke the ProcessSingleCertificationCheck web method.  A credit transaction is processed with a negative sign in front of the amount.  Set the routing number to 490000018 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.  If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  A
 - RESPONSE_TYPE_TEXT:  APPROVED
 - RESULT_CODE:  0
 - TYPE_CODE:  4096
 - CODE:  AUTH NUM 272-172
 - MESSAGE:   APPROVAL

**Manager Needed**
When processing a single certification check for Manager Needed you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000021 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.   If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  W
 - RESPONSE_TYPE_TEXT:  Warning
 - RESULT_CODE:  132
 - TYPE_CODE:  4100
 - CODE: MANAGER NEEDED CHECK TOO LARGE  
 - MESSAGE: MANAGER NEEDED CHECK TOO LARGE  

If a transaction is returned with a warning message, the RESULT_CODE element will contain a 4. In the above response message, a bit comparison will show that the RESULT_CODE element also contains a 128 which indicates that the transaction limit has been exceeded.  The host system should be setup to recognize warning messages and any other additional information contained within the RESULT_CODE element.  In this case the MESSAGE element indicates that the Manager is needed because the check is too large. The TYPE_CODE again shows that 4096 was returned indicating that there was an internal override due to a predetermined fixed response being returned as well as a 4 indicating a Business Check was sent for processing. 

If the host system receives a warning message back and indicates “MANAGER NEEDED” and you are not doing PPD, then you have the option of sending an override request packet back to the Authorization Gateway.  The override request is created by sending the same request XML Data Packet back to the Authorization Gateway. However, you must change the value of the IDENTIFIER element to “O”. You also have the option of changing the values of the TRANSACTION_ID element and REQUEST_ID attribute so that your host system can record and track the override request as a separate transaction. In this instance you also have the option of changing the value in the CHECK_AMOUNT element.  Again, identifying a request XML Data Packet as an override will void the previous transaction and input a new transaction in its place, and your host system will receive an authorization in return.

**Represented Check**
When processing a single certification check for Re-Presented Check you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000047 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.   If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  W
 - RESPONSE_TYPE_TEXT:  Warning
 - RESULT_CODE:  4
 - TYPE_CODE:  4228
 - CODE: MANAGER NEEDED REPRESENTED CHECK
 - MESSAGE: MANAGER NEEDED REPRESENTED CHECK  

If a transaction is returned with a warning message, the RESULT_CODE element will contain a 4.  In this case the MESSAGE element indicates that the Manager is needed because this is a represented check. The TYPE_CODE in this response contains a lot of information. A bit comparison will show that the value of 4228 in the TYPE_CODE element contains 128 which indicates a represented check as well as 4096 indicating that there was an internal override due to a predetermined fixed response being returned, and a 4 indicating a Business Check was sent for processing.  The host system should be able to recognize that a warning message was received from the Authorization Gateway and that it was a represented check. 

If the host system receives a warning message back and indicates “MANAGER NEEDED” and you are not doing PPD, then you have the option of sending an override request packet back to the Authorization Gateway.  The override request is created by sending the same request XML Data Packet back to the Authorization Gateway. However, you must change the value of the IDENTIFIER element to “O”. You also have the option of changing the values of the TRANSACTION_ID element and REQUEST_ID attribute so that your host system can record and track the override request as a separate transaction. Again, identifying a request XML Data Packet as an override will void the previous transaction and input a new transaction in its place, and your host system will receive an authorization in return.

**No ACH**
When processing a single certification check for No ACH Check you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000050 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.   If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  D, VA
 - RESPONSE_TYPE_TEXT:  Decline, Verification Approved
 - RESULT_CODE:  8,0
 - TYPE_CODE:  512, 768
 - CODE: DECLINE, NO ACH
 - MESSAGE: DECLINE, NO ACH

**For SEC Codes (WEB, TEL, PPD, or CCD)**
If a transaction is returned with a Decline message, the RESULT_CODE element will contain an 8. In this case the MESSAGE element indicates that the transaction is Declined because of the type of SEC code assigned to the terminal. The TYPE_CODE in this response contains a lot of information. A bit comparison will show that the value of 512 in the TYPE_CODE element means that this is a Block for ACH transaction.  The host system should be able to recognize that a warning message was received from the Authorization Gateway and that it was a Decline with No ACH. 

**For All Other SEC Codes.**
If a transaction is returned with a Verification Approved message, the RESULT_CODE element will contain a 0. In this case the MESSAGE element indicates that the transaction is Verification Approved. The TYPE_CODE in this response contains a lot of information. A bit comparison will show that the value of 768 in the TYPE_CODE element contains 512 which indicates a Block for ACH and a 256 which indicates it is also for an Unknown Presentment.

**MICR ERROR**
When processing a single certification check for MICR ERROR Check you will need to invoke the ProcessSingleCertificationCheck web method and set the routing number to 490000015 in the ROUTING_NUMBER element of the request XML Data Packet. You will also have to set the value of the IDENTIFER element to “R” if you are using a PPD or CCD schema or “A” for all other schemas.   If the request XML Data Packet is valid then this routing number will trigger the Authorization Gateway to return a response with the following information to the host system:

 - RESPONSE_TYPE:  VE
 - RESPONSE_TYPE_TEXT: Verification Error
 - RESULT_CODE:  2
 - TYPE_CODE:  0
 - CODE: MICR ERROR
 - MESSAGE: MICR ERROR  

If a transaction is returned with a Verification Error message, the RESULT_CODE element will contain a 2. In this case the MESSAGE element indicates that the transaction is a MICR ERROR. The TYPE_CODE in this response contains a lot of information. A bit comparison will show that the value of 0 in the TYPE_CODE element means that this is Nothing other than an Error.  The host system should be able to recognize that a warning message was received from the Authorization Gateway and that it was a MICR ERROR and that no other information is provided. 

## **Exception Handling**
Incorporating exception handling into your host system for the Authorization Gateway interface is important so that the host system can check to ensure that nothing unexpected occurred during processing.  There are basically two reasons the Authorization Gateway may return an exception in the response XML Data Packet. 

Although the Authorization Gateway has been rigorously tested it is possible that an internal error may occur. If this happens the Authorization Gateway will return an EXCEPTION element with a message of “An internal error occurred. The Transaction was NOT processed”. If this exception is return to the host system our software team will be immediately notified with detailed information about the problem and will work to correct the issue. We work hard to ensure these types of exceptions do not occur, but your integration team should understand what internal errors mean, how they are handled, and configure the host system to take appropriate action.

 The Authorization Gateway may also return exception messages if there are authentication, authorization, or data related errors. The message for these types of exceptions will vary, but the host system will receive a detailed message within the EXCEPTION element that outlines exactly what the problem was. These types of exceptions are built into the Authorization Gateway by design and the Authorization Gateway relies on the host system to resolve the issue.

We expect that your integration team has included at least a minimal level of exception handling into your host system prior to beginning the Certification Phase.

If an error occurs within the Authorization Gateway, the XML string response will detail the reason for the error within an Exception element. The Exception element will NOT be present if an error did not occur. However, should an error occur, the Exception element may be found as a child element of either the Response element, or the Transaction element.

### EXCEPTION **Element – Example as a child of the RESPONSE element**
```
<? Xml version=”1.0” encoding=”utf-8” ?> 
          <RESPONSE xmlns:xsd=”http://www.w3.org/2001/XMLSchema”     
                             xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance”    
            REQUEST_ID=”” >
                 <EXCEPTION>
               <MESSAGE>An internal error occurred. The transaction was NOT      
                           Processed.
                         </MESSAGE> 
         </EXCEPTION>
      </RESPONSE>
```

### **The Exception element will contain the following elements.**

| MESSAGE: | Contains text information about the exception |
|----------|-----------------------------------------------|

### **Request an Archived Response**
Each time a valid data packet request is processed the Authorization Message that is returned is archived. To maintain a high level of performance Authorization Messages are archived asynchronously while the original Authorization Message is returned to the requestor.

If needed an Authorization Message for a previously processed transaction can be requested again by invoking the GetArchivedResponse web method.  It is important to note that the transaction is not processed again, only the original Authorization Message that was archived is returned. Each Authorization Message is archived along with the unique user defined Request ID and Terminal ID that was provided in the data packet request. The GetArchivedResponse web method accepts the Request ID as an input parameter and will return the original Authorization Message for the given Request ID and Terminal ID.
 
_NOTE: If Authorization Gateway Request IDs are duplicated for a given Terminal, only the last Authorization Message for the pairing will be returned._ 

## *Requesting a Certification Script**

Requesting a certification script is the major milestone of the Development Phase. It signifies that your integration team has completed the integration effort and alerts our software team that the host system is ready to undergo certification. It is important that your integration team contact us to request a certification script. If a certification script is not requested, but you begin the Certification Phase, our software team will not be able to properly certify your host system and your team will have to rerun the certification script prior to moving to the Production Phase. 
You can reach our Integration Department by email at integration@eftsupport.com

# **Phase 3 Certification**

During the certification phase your integration team will be responsible for sequentially completing the objectives in the certification script provided. Your team should now be intimately familiar with the Authorization Gateway and the host system should now be able to handle the completion of these objectives without any problems.  During the certification phase our software team will closely monitor each transaction to ensure it is valid, and that the host system is properly configured. We will alert you to the status of the transaction in our system and advise you if there are any modifications that need to be made to the host system. The successful completion of each objective outlined in the certification script signifies the completion of the major milestone for the Certification Phase and marks the opportunity to begin the Production Phase.

# **Phrase 4 Migrating to Production**
The Production Phase is the final phase of the integration effort. During this phase you will have to make some minimal changes to the host system in order to use the Authorization Gateway in a production environment. This includes the following:

 - You will need to request a user name and password for production. This user name and password will be different from the user name and password provided for certification and will only be valid for production.  The host system will then need to be modified to include this user name and password in the authentication header when invoking a production web method.
 - You will need to request the production URL for the Authorization Gateway. This URL will be different then the URL used for certification, however it will contain identical web methods. The host system will need to be modified to invoke web methods on the production URL.
 - You will also need to change the certification web methods listed below to their sister production web methods.

|     Certification Web Method            |                  |     Production Web Method    |
|-----------------------------------------|------------------|------------------------------|
|     GetCertificationTerminalSettings    |     change to    |     GetTerminalSettings      |
|     ProcessSingleCertificationCheck     |     change to    |     ProcessSingleCheck       |

 - Once the host system has been modified to include these changes for processing transactions in a production environment you will need to request a “Go Live” date. Requesting a “Go Live” date signifies completion of the last major milestone of the integration effort and indicates to our software team that the host system is ready for production. 

Congratulations!! :tada:
