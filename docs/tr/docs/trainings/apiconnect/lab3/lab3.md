# Lab 3: Adding additional security to "Hello API Connect" API

## Purpose of this lab:

By following the steps, this tutorial demonstrates:
- Add Client ID security requirements.
- Test the response of your "Hello API Connect" API by providing Client ID.
- Add Client Secret security requirements.
- Test the response of your "Hello API Connect" API by providing Client ID and Client Secret.
  
## Step by step guide:

Follow the steps to add additional security requirements and test your API:

### Step 1. Add Client ID security requirement

- In the API Manager homepage, select "Develop APIs and products".
  
![Develop APIs and products](images/step1.1.png)

- Select "Hello API Connect" to edit API definition.

![Edit API definition](images/step1.2.png)

- In the "Design" tab, select "Security Schemes". As it is shown, API hasn't any security scheme definition.

![Select Security Schemes](images/step1.3.png)

- Click "Add" to add Client ID to "Security Schemes".
  
   Fill in the fields listed below and click "Create":
   - Security Scheme Name (Key): clientID
   - Type: apiKey
   - Key Type(optional): client_id
   - Located in: header
   - Variable name: X-IBM-Client-ID

![Client ID Security scheme definition](images/step1.4.png)
  
- Click "Save" to save the Client ID security scheme definition.

![Save Client ID](images/step1.5.png)

- Select "Security" to list the security requirements for your API.
  
![List security requirements of API](images/step1.6.png)

- Select Client ID security scheme to add a security requirement to your API and click "Create".

![Client ID security requirement](images/step1.7.png)

### Step 2. Test "Hello API Connect" API by providing Client ID

- Select the "Test" tab and click "Send" to test the API.

![Test tab](images/step2.1.png)

- See the "Hello, API Connect" as a response.

![API Response](images/step2.3.png)

- Delete the X-IBM-Client-ID header from the reuquest and click "Send" to test the API.

![Delete Client ID](images/step2.4.png)

- Verify that requesting the API with missing Client ID security requirement is denied with a "401 Unauthorized Error".

![API Error Response](images/step2.5.png)

### Step 3. Add Client Secret security requirement 

- Click "Design" and "Security Schemes" again and click "Add +" to add Client Secret as a security scheme.
  
     Fill in the fields listed below and click "Create":
   - Security Scheme Name (Key): clientSecret
   - Type: apiKey
   - Key Type(optional): client_secret
   - Located in: header
   - Variable name: X-IBM-Client-Secret

![Add Client Secret](images/step2.6.png)

- Click "Save" to save the Client Secret security scheme definition.

![Save Client Secret](images/step2.7.png)

- Select "Security" to list the security requirements for your API.
  
![List security requirements of API](images/step2.8.png)

- Select Client Secret security scheme to add a security requirement to your API and click "Submit" and then "Save".

![Client Secret security requirement](images/step2.9.png)

### Step 4. Test "Hello API Connect" API by providing both Client ID and Client Secret

- Select the "Test" tab and click "Send" to test the API.

![Test tab](images/step2.10.png)

- See the "Hello, API Connect" as a response.

![API Response](images/step2.11.png)

- Delete the X-IBM-Client-Secret header from the reuquest and click "Send" to test the API.

![Delete Client Secret](images/step2.12.png)

- Verify that requesting the API with missing Client Secret security requirement is denied with a "401 Unauthorized Error" as well as we faced before with Client ID security requirement.

![API Error Response](images/step2.13.png)

**DONE:** You have just finished adding additional security to "Hello API Connect" API
