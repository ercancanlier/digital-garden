# Lab 2: Developing "Hello API Connect" API

## Purpose of this lab:

By following the steps, this tutorial demonstrates:
- Create a simple API definition.
- Explore the API definition.
- Edit the API definition and add a policy(operation) to the flow.
- Test the response of your very first simple "Hello API Connect" API.

## Step by step guide:

Follow the steps to develop and test your very first API:

### Step 1. Create a simple API definition

- In the API Manager homepage, select "Develop APIs and products".
  
![Develop APIs and products](images/step1.1.png)

- Select "Add" to develop API.

![Add API](images/step1.2.png)

- On the "Select API type" page, select "New OpenAPI" and click "Next".

![Select API type](images/step1.3.png)

- Enter details of the API and click "Next".

![Enter details of the API](images/step1.4.png)

- Configure the security for the API and click "Next".

![Security of the API](images/step1.5.png)

- A summary displays info for API definition.

![Summary of the API](images/step1.6.png)

### Step 2. Explore the API definition

- Select "Edit API" to open the API editor. The first tab "Design" displays details about the API definition.

![Edit API](images/step2.1.png)

- "Gateway" tab displays the operations in the API. "Policy" term is used for operations in API Connect.

![Gateway Tab](images/step2.2.png)

### Step 3. Edit the API definition

- Delete "Invoke" policy from the flow.

![Delete Invoke policy](images/step3.1.png)

- Select "GatewayScript" policy and add it to the flow. Rename it to "Set response" for better readability.

![GatewayScript Policy](images/step3.2.png)

- Add below gateway script to the policy.
  
```javascript
const response="Hello, API Connect!";

context.message.body.write(response);
```

- Click "Validate" to validate your API definition.

![Validate API definition](images/step3.3.png)

- Select "Online" to publish the API and test. API will be published to the sandbox.

![Publish API to Sandbox catalog](images/step3.4.png)

### Step 4. Test "Hello API Connect" API

- Select the "Test" tab and click "Send" to test the API.

![Test tab](images/step3.5.png)

**NOTE:** API endpoint displays /organization/sandbox/base-path as /canlier/sandbox/hello for our API.

- Disabled CORS causes this error:

![CORS error](images/step3.6.png)

- Enable CORS to pass the error.

![Enable CORS](images/step3.7.png)

- See the "Hello, API Connect" as a response.

![API Response](images/step3.8.png)

**DONE:** You have just finished creating your very first "Hello API Connect" API.
