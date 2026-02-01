# Azure HTTP Function Template

A Python-based Azure Function template that demonstrates an HTTP-triggered function using the Azure Functions Python programming model.

## Overview

This project contains a simple HTTP-triggered Azure Function that accepts a name parameter and returns a personalized greeting. It serves as a starting point for building HTTP-based serverless applications on Azure.

## Prerequisites

- Python 3.9 or higher
- [Azure Functions Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local) (v4.x or higher)
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) (optional, for deployment)
- Visual Studio Code with the [Azure Functions extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) (optional)

## Project Structure

```
az_http_func/
├── function_app.py          # Main function application and HTTP trigger handler
├── host.json                # Azure Functions host configuration
├── local.settings.json      # Local development settings
├── requirements.txt         # Python dependencies
└── README.md                # This file
```

## Setup & Installation

### 1. Create a Python Virtual Environment

```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

## Running Locally

Start the Azure Functions runtime locally:

```bash
func start
```

The function will be available at: `http://localhost:7071/api/http_trigger`

### Testing the Function

#### Using Query Parameters

```bash
curl "http://localhost:7071/api/http_trigger?name=John"
```

Response:
```
Hello, John. This HTTP triggered function executed successfully.
```

#### Using POST Request Body

```bash
curl -X POST http://localhost:7071/api/http_trigger \
  -H "Content-Type: application/json" \
  -d '{"name": "Jane"}'
```

Response:
```
Hello, Jane. This HTTP triggered function executed successfully.
```

#### Without Name Parameter

```bash
curl http://localhost:7071/api/http_trigger
```

Response:
```
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.
```

## Function Details

### HTTP Trigger Function

**Route:** `http_trigger`

**HTTP Methods:** GET, POST

**Parameters:**
- `name` (string, optional) - The name to personalize the greeting

**Description:**
The `http_trigger` function processes HTTP requests and returns a greeting message. It accepts the name parameter from either:
1. Query string: `?name=value`
2. JSON request body: `{"name": "value"}`

**Authentication:** Anonymous (no authentication required)

### Code Overview

```python
@app.route(route="http_trigger")
def http_trigger(req: func.HttpRequest) -> func.HttpResponse:
    # Logs the request
    logging.info('Python HTTP trigger function processed a request.')
    
    # Retrieves 'name' from query parameters
    name = req.params.get('name')
    
    # If not found, attempts to get it from JSON request body
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
    
    # Returns personalized or generic response
    if name:
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
             "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
             status_code=200
        )
```

## Configuration Files

### `host.json`
Contains Azure Functions host configuration settings for the local runtime.

### `local.settings.json`
Stores local development settings and environment variables. **Note:** This file should not be committed to source control as it may contain sensitive information.

### `requirements.txt`
Lists Python package dependencies for the project.

## Deployment to Azure

### Prerequisites for Deployment
- Azure subscription
- Azure CLI authenticated with your account
- Azure Storage account for the function app

### Deploy Using Azure CLI

1. Create a resource group:
```bash
az group create --name myResourceGroup --location eastus
```

2. Create a storage account:
```bash
az storage account create --name mystorageaccount --resource-group myResourceGroup --location eastus
```

3. Create a function app:
```bash
az functionapp create --resource-group myResourceGroup \
  --consumption-plan-location eastus \
  --runtime python \
  --runtime-version 3.11 \
  --functions-version 4 \
  --name myFunctionApp \
  --storage-account mystorageaccount
```

4. Deploy the function:
```bash
func azure functionapp publish myFunctionApp
```

## Troubleshooting

### Port Already in Use
If port 7071 is already in use, specify a different port:
```bash
func start --port 7072
```

### Module Import Errors
Ensure all dependencies are installed:
```bash
pip install -r requirements.txt
```

### Virtual Environment Not Activated
Make sure your virtual environment is activated:
```bash
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

## Resources

- [Azure Functions Python Developer Guide](https://learn.microsoft.com/en-us/azure/azure-functions/functions-reference-python)
- [Azure Functions HTTP Trigger](https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook)
- [Azure Functions Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local)

## License

This project is provided as-is for educational and development purposes.
