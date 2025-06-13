# Lab 4: Developing a loopback REST API that returns the request body as a response

## Purpose of this lab:

By following the steps, this tutorial demonstrates:
- Create a REST API definition.
- Edit the API definition and add a policy(operation) to the flow to return back the request response.
- Test the response of your loopback REST API.
  
## Step by step guide:

Follow the steps to create a loopback REST API and test your API to verify the response is same as request.body:

### Step 1. Create an API definition

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

### Step 2. Edit the API definition and add a "Parse" policy

- Delete "Invoke" policy from the flow.

![Delete Invoke policy](images/step2.1.png)

- Select "Parse" policy and add it to the flow. Rename it to "Parse request body" for better readability.

![Parse Policy](images/step2.2.png)

- Click "Validate" to validate your API definition.

![Validate API](images/step2.3.png)

- Select "Online" to publish the API and test. API will be published to the sandbox.

![Publish API to Sandbox catalog](images/step2.4.png)

### Step 3. Test "Loopback" API

- Select the "Test" tab and select  POST as an operation.

![Select POST operation](images/step3.1.png)

- Select "Body" and add a request body.

![Add a request body](images/step3.2.png)

- Click "Send" to test the API.
![Test the API](images/step3.3.png)

- See the "Request Body" as a response.

![API Response](images/step3.4.png)

**DONE:** You have just finished creating a loopback API.

