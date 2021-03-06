![Logo](../assets/img/logo.png?raw=true)

# Introduction 

Credy is a company that specializes on the quality lead generation. 
This document further describes how to integrate API to send leads to Credy.
Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Production URL: [https://api.credy.eu/v3/](https://api.credy.eu/v3/)

Staging URL: [http://api.staging.credy.eu/v3/](http://api.staging.credy.eu/v3/)

API supports post fields, XML and JSON.

To get response error messages in spain, "Accept-Language" header’s value must be set to “es”.

## Signing requests

Each request must be signed. Signature consists of api_key, timestamp and hash.

Hash is a SHA1 hash of the concatenation of timestamp, api_key and secret_key.

Allowed deviation for timestamp is UTC +/- 60 seconds.

*ex. sha1(timestamp + apiKey + secretKey)*

Api key and secret key will be assigned to you by Credy.

## Lead registration flow

1. Request is sent to /v3/customers

2. If request is successful, customer uuid must be stored

3. Request is sent to /v3/leads with previously stored customer uuid in query parameter "customer" *ex. /v3/leads?customer=a3ff7c20-e9e8-43a0-bfb7-71a5cf43b8d3*

# API methods

### POST /v3/customers

This method will create or update customer and return customer uuid.

| Field | Validations | Description |
| ----- | ----------- | ----------- |
| first_name | Required | First name of customer |
| last_name | Required | Faternal last name of customer |
| second_last_name | Required | Maternal last name of customer |
| email | Required | Email address |
| personal_id | Required<br> Must match regex: ` /^[A-Z]{1}[AEIOUX]{1}[A-Z]{2}[0-9]{2}(0[1-9]|1[0-2])(0[1-9]|1[0-9]|2[0-9]|3[0-1])[HM]{1}(AS|BC|BS|CC|CS|CH|CL|CM|DF|DG|GT|GR|HG|JC|MC|MN|MS|NT|NL|OC|PL|QT|QR|SP|SL|SR|TC|TS|TL|VZ|YN|ZS|NE)[B-DF-HJ-NP-TV-Z]{3}[0-9A-Z]{1}[0-9]{1}$/ ` | Personal ID of customer |
| bank_account | Required<br> Must be valid CLABE | Bank account number of customer |
| phone | Required<br>Must be valid phone number | Customer mobile phone number |
| neto_income | Required | Neto income in MXN |
| remuneration_deadline |Must be valid date<br> Required when occupation is one of following: <br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER | Remuneration deadline |
| address | Required<br> Object | Contains registered address data |
| address[city] | Required | City |
| address[street] | Required | Street |
| address[house_number] | Required | House number |
| address[flat_number] | Optional | Flat number |
| address[postal_code] | Required<br> Must match regex: ` /^\d{5}$/ `  | Postal code |
| address[region] | Required | State |
| address[county] | Required | Municipality |
| address[district] | Required | Colony |
| occupation | Required | Occupation type <br> For allowed values see _Appendix A_ |
| employer_phone |Must be valid phone number<br> Required when occupation is one of following: <br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER | Employers phone number |
| employer | Required when occupation is one of following:<br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER<br> | Employer |
| current_job_position | Required when occupation is one of following:<br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER<br> | Current job position |
| tax_id_number | Required<br> Must match regex: ` /^[A-Z]{1}[AEIOUX]{1}[A-Z]{2}[0-9]{2}(?:0[1-9]|1[0-2])(?:0[1-9]|1[0-9]|2[0-9]|3[0-1])[A-Za-z0-9]{3}$/ ` | Customer RFC number |
| nationality | Required | Customer nationality |
| has_beneficiary | Required<br>Boolean | Does customer take a loan for himself/herself or not |
| has_carloan | Required<br>Boolean | Does customer have a car loan |
| has_mortgage | Required<br>Boolean | Does customer have a mortgage loan |
| credit_card_verification | Optional <br> Must match regex: ` /^\d{4}$/ ` | Last 4 digits of credit card number, if customer has credit card number |
| signature | Required <br> Object | request signature |
| signature[timestamp] | Required <br> unix timestamp<br> must be UTC +/- 60 seconds | unix timestamp, must be UTC +/- 60 seconds |
| signature[api_key] | Required | api key |
| signature[hash] | Required | sha1 concatenation of timestamp, api key and secret key |

### JSON examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |

Body:
```json
{
   "first_name":"Aureliano",
   "last_name":"Orozco",
   "second_last_name":"Pacana",
   "email":"miranda.doedoe@mailinator.com",
   "personal_id":"AEDK661027HNEGLLT9",
   "remuneration_deadline": "2017-01-08",
   "bank_account":"141807225454240832",
   "phone":"01 449 329 1020",
   "neto_income":960,
   "address":{
      "city":"Doe Doestad",
      "street":"Escarlata Haven",
      "house_number":"5052",
      "flat_number":3,
      "postal_code":91648,
      "region":"Quintana Roo",
      "county":"test",
      "district":"test"
   },
   "occupation":"WRITTEN_CONTRACT_OR_ORDER",
   "employer_phone":"687 493 9638",
   "tax_id_number":"AAMC160222CNX",
   "nationality":"Alemania",
   "has_beneficiary":false,
   "has_carloan":true,
   "has_mortgage":true,
   "credit_card_verification":3368,
   "employer":"Doe Doe-Doe Doe",
   "current_job_position":"Gas Plant Operator",
   "signature":{
      "timestamp":1491466495,
      "api_key":"mexico",
      "hash":"8a77fcd5f495b6d3a9c01fb11a4e9ce10078442e"
   }
}
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```json
{
   "uuid":"7b1be1e9-3c5f-578c-8e44-20af1a6de4b8",
   "product":"PAYDAY",
   "first_name":"Aureliano",
   "last_name":"Orozco",
   "email":"miranda.doedoe@mailinator.com",
   "personal_id":"AEDK661027HNEGLLT9",
   "phone":"+524493291020",
   "employer_phone":"+526874939638",
   "neto_income":"960",
   "lives_at_registered_address":1,
   "occupation":"WRITTEN_CONTRACT_OR_ORDER",
   "bank_account":"141807225454240832",
   "remuneration_deadline": "2017-01-08",
   "employer":"Doe Doe-Doe Doe",
   "current_job_position":"Gas Plant Operator",
   "second_last_name":"Pacana",
   "tax_id_number":"AAMC160222CNX",
   "nationality":"Alemania",
   "has_beneficiary":"0",
   "has_carloan":"1",
   "has_mortgage":"1",
   "credit_card_verification":3368,
   "address":{
      "region":"Quintana Roo",
      "county":"test",
      "district":"test",
      "city":"Doe Doestad",
      "street":"Escarlata Haven",
      "house_number":"5052",
      "flat_number":"3",
      "postal_code":"91648"
   }
}
```

**Failed response for /v3/customers**

Status code: 422

Response:
```json
[
   {
      "field":"email",
      "message":"Email no puede estar vacío."
   },
   {
      "field":"address.street",
      "message":"Dirección no puede estar vacío."
   }
]
```

### XML examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/xml  |
| Accept       | application/xml  |


Body:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<request>
    <first_name>Aureliano</first_name>
    <second_first_name>Gandharva</second_first_name>
    <last_name>Orozco</last_name>
    <email>miranda.doedoe@mailinator.com</email>
    <personal_id>AEDK661027HNEGLLT9</personal_id>
    <bank_account>141807225454240832</bank_account>
    <phone>01 449 329 1020</phone>
    <neto_income>60</neto_income>
    <remuneration_deadline>2017-01-08</remuneration_deadline>
    <address>
        <city>Doe Doestad</city>
        <street>Escarlata Haven</street>
        <house_number>5052</house_number>
        <flat_number>3</flat_number>
        <postal_code>16485</postal_code>
        <region>Quintana Roo</region>
        <county>test</county>
        <district>test</district>
    </address>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <employer_phone>687 493 9638</employer_phone>
    <tax_id_number>AAMC160222CNX</tax_id_number>
    <nationality>Alemania</nationality>
    <has_beneficiary>0</has_beneficiary>
    <has_carloan>1</has_carloan>
    <has_mortgage>1</has_mortgage>
    <credit_card_verification>6368</credit_card_verification>
    <employer>Doe Doe-Doe Doe</employer>
    <current_job_position>Gas Plant Operator</current_job_position>
    <signature>
        <timestamp>491466495</timestamp>
        <api_key>mexico</api_key>
        <hash>8a77fcd5f495b6d3a9c01fb11a4e9ce10078442e</hash>
    </signature>
</request>
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <uuid>7b1be1e9-3c5f-578c-8e44-20af1a6de4b8</uuid>
    <product>PAYDAY</product>
    <first_name>Aureliano</first_name>
    <last_name>Orozco</last_name>
    <email>miranda.doedoe@mailinator.com</email>
    <personal_id>AEDK661027HNEGLLT9</personal_id>
    <phone>+524493291020</phone>
    <employer_phone>+526874939638</employer_phone>
    <neto_income>60</neto_income>
    <remuneration_deadline>2017-01-08</remuneration_deadline>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <bank_account>141807225454240832</bank_account>
    <employer>Doe Doe-Doe Doe</employer>
    <current_job_position>Gas Plant Operator</current_job_position>
    <second_last_name>Pacana</second_last_name>
    <tax_id_number>AAMC160222CNX</tax_id_number>
    <nationality>Alemania</nationality>
    <has_beneficiary>0</has_beneficiary>
    <has_carloan>1</has_carloan>
    <has_mortgage>1</has_mortgage>
    <credit_card_verification>6368</credit_card_verification>
    <address>
        <region>Quintana Roo</region>
        <county>test</county>
        <district>test</district>
        <city>Doe Doestad</city>
        <street>Escarlata Haven</street>
        <house_number>5052</house_number>
        <flat_number>3</flat_number>
        <postal_code>16485</postal_code>
    </address>
</response>
```

**Failed response for /v3/customers**

Status code: 422

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <item>
        <field>personal_id</field>
        <message>Personal ID cannot be blank.</message>
    </item>
    <item>
        <field>address.postal_code</field>
        <message>Postal code cannot be blank.</message>
    </item>
</response>
```

### POST /v3/leads?customer={customer_uuid} *

Creates lead for existing customer.

* Replace customer_uuid placeholder with received uuid value from POST /v3/customers response.

| Field                | Validations                                | Description                                             |
|----------------------|--------------------------------------------|---------------------------------------------------------|
| loan_sum             | Required Must be valid number              | Amount of credit requested by customer                  |
| loan_period          | Required Must be valid number              | Term for credit requested                               |
| ip_address           | Must be valid IP address                   | IP address of customer                                  |
| aff_lead_id          |                                            | Your lead ID                                            |
| signature            | Required, object contains signature fields | request signature                                       |
| signature[timestamp] | Required                                   | unix timestamp, must be UTC +/- 60 seconds              |
| signature[api_key]   | Required                                   | api key                                                 |
| signature[hash]      | Required                                   | sha1 concatenation of timestamp, api key and secret key |


### JSON examples for /v3/leads

**POST /v3/leads?customer=bbf18703-1f8d-5c8a-a83b-9433f003807f**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |


Body:

```json
{
    "loan_sum": "50",
    "loan_period": "1",
    "ip_address": "127.0.0.1",
    "signature": {
        "api_key": "6acc49b6-540c-512f-b1b8-7e1a9eb7d7a5",
        "timestamp": 1465310979,
        "hash": "051d1de34995444d44ab344e62e5775727f10f36"
    }
}
```

**Successful response for /v3/leads**

Status code: 2xx

Response:
```json
{
	"loan_sum": "250.00",
	"loan_period": "30",
	"uuid": "76171735-1c22-5564-8438-fb597523fdeb",
	"status": "UNDER_INVESTIGATION",
	"date_created": "2017-01-04 08:33:09",
	"product": "PAYDAY"
}
```

**Example failed response for /v3/leads**

Status code: 422

Response:
```json
[
   {
      "field":"loan_sum",
      "message":"Loan Sum cannot be blank."
   }
]
```

### XML examples for /v3/leads

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/xml  |
| Accept       | application/xml  |


**POST /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Body:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<request>
	<loan_sum>50</loan_sum>
	<loan_period>1</loan_period>
	<ip_address>127.0.0.1</ip_address>
	<signature>
		<api_key>6acc49b6-540c-512f-b1b8-7e1a9eb7d7a5</api_key>
		<timestamp>1465310979</timestamp>
		<hash>051d1de34995444d44ab344e62e5775727f10f36</hash>
	</signature>
</request>
```

**Successful response for /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <loan_sum>50.00</loan_sum>
    <loan_period>1</loan_period>
    <uuid>5badfe4e-a157-55a4-bcdb-f9f3754ea054</uuid>
    <status>UNDER_INVESTIGATION</status>
    <date_created>2017-01-04 17:25:00</date_created>
</response>
```

**Failed response for /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Status code: 422

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <item>
        <field>loan_sum</field>
        <message>Loan Sum cannot be blank.</message>
    </item>
</response>
```

# Appendix A - ENUMs

| occupation                 |                                   |                                 |
|----------------------------|-----------------------------------|---------------------------------|
| value                      | English                           | Spanish                     |
EMPLOYED_INDEFINITE_PERIOD | Employed: indefinite time | Empleado por periodo indefinido |
EMPLOYED_SPECIFIED_PERIOD | Employed: specified time | Empleado por periodo especifico |
WRITTEN_CONTRACT_OR_ORDER | Written contract or order | Contrato u orden escrita |
PENSIONER1 | Pensioner | Pensionado |
STUDENT | Student | Estudiante |
UNEMPLOYED | Unemployed | Desempleado |
SELF_EMPLOYED | Self employed (does not own business) | Autoempleado |
FARMER | Farmer | Campesino |
FREELANCER | Freelancer | Freelancer |
OWN_BUSINESS | Own business (economic activity) | Negocio propio |
OTHER | Other | Otro |
