---
title: How to Register an AE
author: Andreas Kraft
date: 2023-08-10
tags: [ basics, ae, example, code]
---

# How to Register an AE

In [What is an AE?](What-is-an-AE.md) we discussed that an *Application Entity* (AE) is the representation of an application on a oneM2M device or on a oneM2M node, and that it is represented by an *&lt;AE>* resource in a oneM2M resource tree.

In this article we want to show how the registration process looks like.

## Preparation

We use Python 3 for the code snippets in this article. Please see [Installing Packages and other Software](Installing-Packages-and-other-Software.md) for further instructions to install the necessary packages.

We assume that a local CSE runs on the local machine and can receive requests at *localhost* port 8080. 

## Registering an AE

In the following sections we will explain the registration code step-by-step.

### Importing Necessary Packages

```py
import requests
```

This makes the functions of the *request* package available to use.

### Defining the CSE URL

```python
# The URL of the oneM2M CSE
cse_url = 'http://localhost:8080/cse-in'
```

The assignment in **Line 2** defines the base URL to the CSE's *CSEBase* resource for the http requests.

### Define the oneM2M Request Attributes

```python
# Set the oneM2M headers
headers = {
    'Content-Type': 'application/json;ty=2',
    'X-M2M-Origin': 'CMyApplication',
    'X-M2M-RI': '12345',
    'X-M2M-RVI': '4'
}
```

This snippet defines the dictionary *headers* that contains the header fields required by oneM2M for the http request.

- **Line 3** sets the http *Content-Type* header field to *application/json*. In addition, we add the necessary argument `ty=2` that specifies that we are going to create a resource of type &lt;AE> (which resource type is 2).
- **Line 4** sets the oneM2M specific header field *X-M2M-Origin* to the value of *"CMyApplication"*. This header field i

> The *X-M2M-Origin* header field is mapped to the oneM2M *originator* request attribute. It is mandatory in all oneM2M requests to identify the sender of a request. In the case of registering a new AE this attribute is handled a bit differently: The value, here *CMyApplication*, specifies a new *originator* that is provided by the application itself. It must not contain an already existing *originator*. Alternatively it is also possible to provide an empty value to the header field; in this case the CSE will assign a unique new one.
>
> An originator must start with either a "C" (for "client defined") or an "S" character (for "service provider defined". 

- **Line 5** sets a *Request Identifier* for the request. This identifier must be unique for each request. It is also returned in the headers of the http response later.
- **Line 6** specifies the *Release Version Indicator*, which tells the CSE to tread the request regarding a specific oneM2M release.
    We will use oneM2M release 4 in our examples.

### Define the oneM2M Request Content

```python
# Define the oneM2M body for the AE
body = {
    'm2m:ae': {
        'rn': 'CMyApplication',
        'api': 'Nmy-application.example.com',
        'rr': True,
        'srv': ['4']
    }
}
```

This snippet defines the content of the oneM2M request. In our case it contains the mandatory attributes that are required to register an *Application Entity*. It is a Python dictionary that will later be converted to a JSON structure.

- **Line 3** indicates that the resource definition is a oneM2M &lt;AE> resource type.
- **Line 4** sets the *resource name* of the resource. This attribute is optional. If we wouldn't provide it in the definition then the CSE would provide a unique one.
- **Line 5** sets the **App-ID** attribute. It uniquely identifies the application class that can be used to identify the application type, its data model etc.
    The provided identifier must start with either the character "R" (for "registered type") or "N" (for "non-registered type").[^1]
- **Line 6** specifies that the AE is able to receive notifications. This information is used by the CSE to determine how to send notifications to an AE.
- **Line 7** specifies a list of *supported release versions*. This attribute is similar to the header field *X-M2M-RVI*. It is used later by the CSE when handling request to and from the AE.

[^1]: One may register application types with a global registration authority.

### Sending the Request

```python
# Send the request to the oneM2M CSE
response = requests.post(cse_url, headers=headers, json=body)
```

This request in **line 2** sends the http request to the CSE, using the header fields and the body defined above.

The response is assigned to the *response* variable.

### Handling Response Statuses

```python
# Check the response status code
if response.status_code == 201:
    print(f'AE registration successful: {response.status_code} / {response.headers["X-M2M-RSC"]}')
    print(response.json())
else:
    print(f'AE registration failed: {response.status_code} / {response.headers["X-M2M-RSC"]} {response.json()}')
```

After sending the request and receiving the response one should check the http status code to see whether the request succeeded.

- **Line 2**: After a successful AE registration the response has a http status of *201*.
- We print the http status as well as the oneM2M *Response Status Code* (in the header field "X-M2M-RSC") in **line 3**, and the response content in **line 4**. This is a &lt;AE> resource structure that is similar to the one that we sent in the request, but with some new attributes that have been added by the CSE as part of the resource creation.
- In case the CSE encountered an error while processing the request it returns a different status code and also a different *Response Status Code*. We print this and also an (optional) debug message  in **line 6**.

### Complete Code

The following is the complete runnable script. It is also available at [code/register.ae.py](code/register-ae.py).

```python
import requests

# The URL of the oneM2M CSE
cse_url = 'http://localhost:8080/cse-in'

# Set the oneM2M headers
headers = {
    'Content-Type': 'application/json;ty=2',
    'X-M2M-Origin': 'CMyApplication',
    'X-M2M-RI': '12345',
    'X-M2M-RVI': '4'
}

# Define the oneM2M body for the AE
body = {
    'm2m:ae': {
        'rn': 'CMyApplication',
        'api': 'Nmy-application.example.com',
        'rr': True,
        'srv': ['4']
    }
}

# Send the request to the oneM2M CSE
response = requests.post(cse_url, headers=headers, json=body)

# Check the response status code
if response.status_code == 201:
    print(f'AE registration successful: {response.status_code} / {response.headers["X-M2M-RSC"]}')
    print(response.json())
else:
    print(f'AE registration failed: {response.status_code} / {response.headers["X-M2M-RSC"]} {response.json()}')
```



