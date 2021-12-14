# Phase 1 Preparation

## Connect to the Authorization Gateway

Once you have successfully connected to the Authorization Gateway and are comfortable with adding the SOAP header, you will test your request and response with the GetCertificationTerminalSettings.
 
The GetCertificationTerminalSettings web method is defined in the “**Terminal Settings – XML Specification**” section, providing an example of the request and response. The invocation of this web method is part of the Preparation Phase because it is the simplest web method and requires no input parameters.

This web method can be invoked if your implementation team determines the host system needs to acquire information about the Authorization Gateway Terminal, and does not need to be invoked on a continuous basis.

    - Please note this is not to be confused with the GetTerminalSettings, which performs the same function for production terminals during the Production Phase.  

## SEC Standard Entry Class Codes
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

  - o	If the Terminal requires the Driver’s License Information.
  - o	If the Terminal is configured for Check Verification.
  - o	If the Terminal is configured for Identity Verification.

A matrix of the available XML Templates for each SEC code can be found in the XML templates section. Each grid contains the name of the XML Template, based on the XML Templates determining criteria, and a link to the actual XML Template. 

The grid also includes the Terminal IDs that can be used for testing and certifying the XML data packet that can be built from the provided XML Template. The Terminal ID will be different for guaranteed transactions and Non-guaranteed transactions.  Guaranteed terminals are numbered 1xxx, and Non-guaranteed terminals are numbered 2xxx.

There are also published example XML data packets that contain example data. 
https://demo.eftchecks.com/webservices/schemas/ppd/examples/CheckVerificationIdentityVerificationDLOptional.xml

*Note about Special Characters
Because the Data packet is XML, some special characters must be escaped to be included in the data. Please see the examples below.
|     Special Character    |     Symbol    |     Escaped Form    |
|--------------------------|---------------|---------------------|
|     Ampersand            |     &         |     &amp;           |
|     Less-than            |     <         |     &lt;            |
|     Greater-than         |     >         |     &gt;            |
|     Quotes               |     “         |     &quot;          |
|     Apostrophe           |     ‘         |     &apos;          |
