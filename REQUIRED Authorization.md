# **Authorization Requirements**

Authorizations on all Standard Entry Class (SEC) Codes detailed below 1) must be readily identifiable as an authorization and must have clear and readily understandable terms.

## **PPD**
PPD is for prearranged payment and deposit entries on personal bank account in which the Receiver is required to complete a written authorization form and must provide physical or digital signature. Digital signatures must be compliant with the ESign Act.  

Additionally, the authorization must provide the Receiver with the method to revoke his authorization by notifying the Originator in the manner prescribed, and the time frame in which the revocation of the authorization must be provided. 

The Originator (Merchant) must have the following verbiage (or substantially similar) on the written authorization form:

:heavy_exclamation_mark: “By signing below, I authorize the Merchant to convert this transaction into an Electronic Funds Transfer transaction or paper draft, and to debit this account for the amount as identified above and to the terms stated here. This authorization shall remain in effect until the Merchant receives written notification from me of intent to terminate at such time and in such manner as to afford the Merchant a reasonable opportunity to act. I authorize this plan to continue as long as the payment amount remains unchanged until the amount owed the Merchant is paid off, or unless the plan is terminated earlier by me as stated above. I understand that all changes such as payment amount, frequency, bank account number change, will require a new ACH Debit Payment Authorization Form to be filled out and submitted to Merchant 15 days prior to any change being implemented.

:heavy_exclamation_mark: In the event that I choose to revoke this authorization, I will do so by contacting the merchant directly.  Processing times may not allow for revocation of this authorization.

:heavy_exclamation_mark: I understand that this payment plan may be cancelled by the Merchant due to NSF (Non-sufficient Funds). In the event this draft, or EFT is returned unpaid, I will be liable to pay an NSF fee of $25.00 (or the amount allowable by law), that may be automatically debited to this bank account via draft or EFT for each NSF.”

## **CCD**

CCD is for Corporate Credit or Debit entries on business bank accounts in which the Receiver is required to complete a written authorization form, contract, or agreement that with the Originator and Receiver have both agreed to be bound by the ACH Rules and the Receiver must provide physical or digital signature. Digital signatures must be compliant with the ESign Act. 

Additionally, the authorization must provide the Receiver with the method to revoke his authorization by notifying the Originator in the manner prescribed, and the time frame in which the revocation of the authorization must be provided. 

The Originator (Merchant) must have the following verbiage (or substantially similar) listed on the written authorization form, contract, or agreement:

:heavy_exclamation_mark: “Submission of this transaction assumes an agreement is in place between both parties to allow converting this transaction into an Electronic Funds Transfer transaction or paper draft, and to debit this account for the amount of the transaction. Additionally, the agreement further states that in the event this draft or EFT is returned unpaid, a service fee, as allowable by law, will be charged to this account via draft or EFT.  In the event you choose to revoke this authorization, please do so by contacting the merchant directly.  Please note that processing times may not allow for revocation of this authorization.”

## **WEB**

For internet initiated (WEB) entries the receiver must have the following verbiage (or substantially similar) text listed on the authorization page of their site.  Additionally, the authorization must provide the Receiver with the method to revoke his authorization by notifying the Originator in the manner prescribed, and the time frame in which the revocation of the authorization must be provided. 

:heavy_exclamation_mark: “By authorizing this transaction, customer agrees that merchant may convert this transaction into an Electronic Funds Transfer (EFT) transaction or paper draft, and to debit this account for the amount of the transaction. Additionally, in the event this draft, or EFT is returned unpaid, a service fee, as allowable by law, will be charged to this account via EFT or draft.  In the event you choose to revoke this authorization, please do so by contacting the merchant directly.  Please note that processing times may not allow for revocation of this authorization.”

Merchant or Integrator is required to use commercially reasonable methods to authenticate customer identity PRIOR to transaction authorization. Possible methods to authenticate 
 - Shared secrets 
 - PIN
 - Password 
 - Request identifying customer information to verify against outsourced databases.
 - Ask challenge questions to verify against credit bureau or outsourced databases.
 - Sending customer a specific piece of information, either online or offline, and then ask customer to verify or provide that information as a second step in the authentication process.

Merchant or Integrator is required to retain the customers’ original authorization or copy of the original authorization in its original form. 

Merchant or Integrator MUST be able to reproduce Customer authorization upon request. Industry best practice for reproduction to appear the same way that website appeared and/or presented to customer on the website at time of authorization including all verbiage and agreement terms provided on the website at time of authorization.

 **NACHA** does not accept proof of an authorization as a list of the data or information captured at time of authorization.
 
The following information must be included in the authorization record:

 - Consumer IP Address of Origination
 - Consumer Name 
 - Consumer Address
 - Transaction Amount
 - Transaction Effective Date
 - Consumer E-mail address (optional; industry recommended best practice)
 - Website where payment was accepted 
 - Signifying whether authorization is for a single or recurring/multiple debits, and debit schedule if recurring/multiple
 - Consumer Banking information
 - Statement of how the consumer’s identity was authenticated 

## **Recorded Authorization TEL**

For telephone initiated (TEL) entries the receiver must have the following verbiage (or substantially similar) read and captured on the recorded customer authorization.  Additionally, the authorization must provide the Receiver with the method to revoke his authorization by notifying the Originator in the manner prescribed, and the time frame in which the revocation of the authorization must be provided.

:heavy_exclamation_mark:	“(Customer’s First and Last Name), by providing your bank account information and verbal authorization today, (Current Date MM/DD/YY),, you <Customer’s Name> are authorizing (Business Name)to create an ACH debit to your account and that this Check by Phone may be drafted from your account as early as today.  In the event your Check by Phone is returned from your bank unpaid, you further agree that a fee of $25.00 or as allowable by law shall also be charged to your account via draft or ACH debit. Do you authorize (Business Name) to proceed with this Check by Phone?  Customer must state “Yes” or “No.”

:heavy_exclamation_mark:	“A Check by Phone will be drafted from your bank account with the following information (Bank Routing Number, Account Number, Check Number, and Check by Phone Amount). Please allow 12 to 72 business hours for this transaction to post to your account.  Should you have any questions regarding your payment, or choose to revoke your authorization, you may reach our office at (Business Telephone that is answered during normal business hours)..   Be advised that depending on the timing of your scheduled ACH, revocation of the authorization may not be available.

## **Receipt Authorization – POP**
For point-of-purchase (POP) single debit entries the receiver must receive a copy of the receipt and the voided check. The receipt provided to the receiver must contain the following for each of the transaction types below.

 ### **Approved Sale or Override:**  
:heavy_exclamation_mark:	Footer Text: “I authorize the merchant to convert my check to an electronic funds transfer, or paper draft, and debit my account for the amount of the transaction. In the event that my draft or EFT is returned unpaid, I agree that a fee of $25 (or as allowed by law) may be charged to my account via draft or EFT.”

:heavy_exclamation_mark:	Signature Line:  Yes

**Verification Only**:  

:heavy_exclamation_mark:	Footer Text: “Must retain check for deposit.”
 - Signature Line:  No
 
**Decline**:  
 - Footer Text:  None
 - Signature Line:  No

**Void**:  
 - Footer Text:  None
 - Signature Line:  No
