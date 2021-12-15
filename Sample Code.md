# **Sample Code**

The first step is to add a Web Reference to the web service URL below in your project called com.eftchecks.demo. 

https://demo.eftchecks.com/Webservices/AuthGateway.asmx

## **VB.NET**

**Example Code – GetCertificationTerminalSettings()**
```
Public Function GetCertificationTerminalSettings() As String
        ‘This function will get the Certification Terminal Settings for Terminal 1010.

        ‘Create variable to hold Authorization Gateway Response
        Dim myAuthGatewayResponse As String

        ‘Create an instance of the Authorization Gateway
        Dim myAuthGateway As New com.eftchecks.demo.AuthGateway

        ‘Create an instance of the Authorization Header
        Dim myAuthHeader As New com.eftchecks.demo.AuthGatewayHeader

        ‘Populate the Auth Header with the User Name, Password, and Terminal ID
        With myAuthHeader
            .UserName = “myUserNameGoesHere”
            .Password = “myPasswordGoesHere”
            .TerminalID = 1010
        End With

        ‘Apply the Auth Header to the Auth Gateway
        myAuthGateway.AuthGatewayHeaderValue = myAuthHeader

        ‘Get the Certification Terminal Settings from the Authorization Gateway
        myAuthGatewayResponse = myAuthGateway.GetCertificationTerminalSettings()

        ‘Create a new XML Document for the Certification Terminal Settings
        Dim myTerminalSettings As New System.Xml.XmlDocument

        ‘Load the Certification Terminal Settings XML into an XML Document
        myTerminalSettings.LoadXml(myAuthGatewayResponse)

        ‘Return the Certification Terminal Settings
        Return myTerminalSettings.OuterXml.ToString

    End Function
```

## **C#**
```
public string GetCertificationTerminalSettings() 
{ 
  //This function will get the Certification Terminal Settings for Terminal 1010. 
    
  //Create variable to hold Authorization Gateway Response 
  string myAuthGatewayResponse; 
    
  //Create an instance of the Authorization Gateway 
  com.eftchecks.demo.AuthGateway myAuthGateway = new com.eftchecks.demo.AuthGateway(); 
    
  //Create an instance of the Authorization Header   
  com.eftchecks.demo.AuthGatewayHeader myAuthHeader = new    
  com.eftchecks.demo.AuthGatewayHeader(); 
    
  //Populate the Auth Header with the User Name, Password, and Terminal ID 
	{ 
	   myAuthHeader.UserName = “myUserNameGoesHere”; 
	   myAuthHeader.Password = “myPasswordGoesHere”; 
	   myAuthHeader.TerminalID = 1010; 
	} 
    
  //Apply the Auth Header to the Auth Gateway 
  myAuthGateway.AuthGatewayHeaderValue = myAuthHeader; 
    
  //Get the Certification Terminal Settings from the Authorization Gateway 
  myAuthGatewayResponse = myAuthGateway.GetCertificationTerminalSettings(); 
    
  //Create a new XML Document for the Certification Terminal Settings 
  System.Xml.XmlDocument myTerminalSettings = new System.Xml.XmlDocument(); 
    
  //Load the Certification Terminal Settings XML into an XML Document 
  myTerminalSettings.LoadXml(myAuthGatewayResponse); 
    
  //Return the Certification Terminal Settings 
  return myTerminalSettings.OuterXml.ToString; 
    
}
```


## **SOAP Message Sample**
```
<?xml version=”1.0” encoding=”utf-8”?>
<soap:Envelope xmlns:xsi=”http://www.w3.org/2001/XMLSchema-instance” xmlns:xsd=”http://www.w3.org/2001/XMLSchema” xmlns:soap=”http://schemas.xmlsoap.org/soap/envelope/”>
  <soap:Header>
    <AuthGatewayHeader    xmlns=”http://tempuri.org/GETI.eMagnus.WebServices/AuthGateway” >
      <UserName> GATEWAYUserName </UserName>
      <Password> GATEWAYPassword</Password>
      <TerminalID>1210</TerminalID>
    </AuthGatewayHeader>
  </soap:Header>
  <soap:Body>
    <ProcessSingleCertificationCheck xmlns=”http://tempuri.org/GETI.eMagnus.WebServices/AuthGateway”>
      <DataPacket>&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; standalone=&quot;no&quot;?&gt;
        &lt;AUTH_GATEWAY REQUEST_ID=
&quot;8949a6093fbc414b871eb65e019a8f08&quot;&gt;
        &lt;TRANSACTION&gt;
        &lt;TRANSACTION_ID&gt;&lt;/TRANSACTION_ID&gt;
        &lt;MERCHANT&gt;
        &lt;TERMINAL_ID&gt;1210&lt;/TERMINAL_ID&gt;
        &lt;/MERCHANT&gt;
        &lt;PACKET&gt;
        &lt;IDENTIFIER&gt;A&lt;/IDENTIFIER&gt;
        &lt;ACCOUNT&gt;
        &lt;ROUTING_NUMBER&gt;490000018&lt;/ROUTING_NUMBER&gt;
        &lt;ACCOUNT_NUMBER&gt;999999&lt;/ACCOUNT_NUMBER&gt;
        &lt;CHECK_NUMBER&gt;9999&lt;/CHECK_NUMBER&gt;
        &lt;ACCOUNT_TYPE&gt;Checking&lt;/ACCOUNT_TYPE&gt;
        &lt;/ACCOUNT&gt; &lt;CONSUMER&gt;
        &lt;FIRST_NAME&gt;Doug&lt;/FIRST_NAME&gt;
        &lt;LAST_NAME&gt;Fresh&lt;/LAST_NAME&gt;
        &lt;ADDRESS1&gt;22 West Way&lt;/ADDRESS1&gt;
        &lt;ADDRESS2&gt;&lt;/ADDRESS2&gt;
        &lt;CITY&gt;Los Angls Afb&lt;/CITY&gt;
        &lt;STATE&gt;CA&lt;/STATE&gt;
        &lt;ZIP&gt;90009&lt;/ZIP&gt;
        &lt;PHONE_NUMBER&gt;2073331234&lt;/PHONE_NUMBER&gt;
        &lt;DL_STATE&gt;&lt;/DL_STATE&gt;
        &lt;DL_NUMBER&gt;&lt;/DL_NUMBER&gt;
        &lt;COURTESY_CARD_ID&gt;&lt;/COURTESY_CARD_ID&gt;
        &lt;/CONSUMER&gt;
        &lt;CHECK&gt;
        &lt;CHECK_AMOUNT&gt;24.55&lt;/CHECK_AMOUNT&gt;
        &lt;/CHECK&gt;
        &lt;/PACKET&gt;
        &lt;/TRANSACTION&gt;
        &lt;/AUTH_GATEWAY&gt;
      </DataPacket>
    </ProcessSingleCertificationCheck>
  </soap:Body>
</soap:Envelope>
```


## **Contact Information**
For questions or to receive certification and live username/passwords and URLs please contact:

Integration Department
integration@eftsupport.com


**Code Sample Kits**
Java, VB.Net and PHP Sample Kits are available via FTP.

FTP: 		ftp.eftchecks.com
UserID:     	SampleKits
Password:   	60cJSK13%0ymgzab

