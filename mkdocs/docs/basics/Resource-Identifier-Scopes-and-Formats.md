---
title:  Resource Identifier Scopes and Formats
author: Andreas Kraft
date: 2023-08-08
tags:
  - basics
  - addressing
---
# Resource Identifier Scopes and Formats

Resource identifiers in oneM2M are used to identify and to point to resources. They are usually used in requests as well as in resource attributes. They are string of *unreserved*[^1] characters used to uniquely identify the target resource within the scope of a request to access the resources. 

[^1]: See [RFC3986](https://tools.ietf.org/html/rfc3986#section-2.3) for the definition of *unreserved* characters.

## Resource Identifier Scopes

The possible scope for a resource identifier is one of the following:

- **CSE-relative** : The resource identifier targets a resource that resides on the same CSE that is being targeted by a request or another resource.  
In that case the CSE-relative format of a resource identifier can be used to address the resource.
- **SP-relative** : The request is targeting a resource that resides on a CSE within the same M2M Service Provider domain as the Originator of the request. This means that the identifier might point to a resource that is not necessarily hosted on the same CSE as the resource targeted by a request.  
In this case an SP-relative format of a resource identifier can be used to address the resource.
- **Absolute** : The request is targeting a resource that resides on a CSE that is located in an M2M Service Provider domain that is different from the M2M Service Provider's domain of the originator of the request.  
In that case the absolute format of a resource identifier shall be used to address the resource. Note, that the absolute format of the resource identifier will always be acceptable also in other cases.

A single resource may be identified by all of the above resource identifier formats depending on the method and scopes that are summarized in the following table. There are two different methods for identifying a resource within the oneM2M resource structure with three different variants each depending on the scope of the request to access the resource. 


## How do Resource Identifiers look like

The ways how the resource identifiers are constructed in each case shall follow:

| Method | CSE-relative Scope | SP-relative Scope | Absolute Scope |
|--------|--------------------|-------------------|----------------|
| **Unstructured** | *Unstructured-CSE-relative-Resource-ID* format of the resource identifier. | *SP-relative-Resource-ID* format of the resource identifier constructed with a *Unstructured-CSE-relative-Resource-ID*. | *Absolute-Resource-ID* format of the resource identifier constructed with a *Unstructured-CSE-relative-Resource-ID*. |
| **Structured** | *Structured-CSE-relative-Resource-ID* format of the resource identifier. | *SP-relative Resource-ID* format of the resource identifier constructed with the *Structured-CSE-relative-Resource-ID*. | *Absolute-Resource-ID* format of the resource identifier constructed with the *Structured-CSE-relative-Resource-ID*. |
| **Hybrid** | *Unstructured-CSE-relative-Resource-ID* format of the resource identifier, and add the *resourceName* of a virtual resource. | *SP-relative-Resource-ID* format of the resource identifier constructed with a *Unstructured-CSE-relative-Resource-ID*, and add the *resourceName* of a virtual resource. | *Absolute-Resource-ID* format of the resource identifier constructed with a *Unstructured-CSE-relative-Resource-ID*, and add the *resourceName* of a virtual resource. |


See also [What are Structured and Unstructured Resource Addresses?](What-are-Structured-and-Unstructured-Resource-Addresses.md)


## Examples of Addressing Methods

The following examples show the different addressing methods for resources and CSE bases. Also, examples for the mapping to http URIs are given.

=== "CSE-relative"


	| Method | Examples |
	| ------ | ------------------- |
	| **Unstructured ID** | MyResourceID<br />CSEResourceID|
	| **Unstructured http** | http://ipAddress:port/MyResourceID<br />http://ipAddress:port/CSEResourceID| 
	| **Structured ID** | CSEName/MyAEName/MyResourceName<br />CSEName | 
	| **Structured http** | http://ipAddress:port/CSEName/MyAEName/MyResourceName<br />http://ipAddress:port/CSEName | 
	| **Hybrid ID** | MyResourceID/MyVirtualResourceName<br />CSEresourceID/MyVirtualResourceName | 
	| **Hybrid http** | http://ipAddress:port/MyResourceID/MyVirtualResourceName<br />http://ipAddress:port/CSEResourceID/MyVirtualResourceName | 

=== "SP-relative"

	| Method | Examples | 
	| ------ | ----------- | 
	| **Unstructured ID** | /CSEID/MyResourceID<br />/CSEID/CSEResourceID| 
	| **Unstructured http** |  http://ipAddress:port/~/CSEID/MyResourceID<br />http://ipAddress:port/~/CSEID/CSEResourceID| 
	| **Structured ID** |  /CSEID/CSEName/MyAEName/MyResourceName<br />/CSEID/CSEName | 
	| **Structured http** |  http://ipAddress:port/~/CSEID/CSEName/MyAEName/MyResourceName<br />http://ipAddress:port/~/CSEID/CSEName | 
	| **Hybrid ID** |  /CSEID/MyResourceI/MyVirtualResourceNamebr<br />/CSEID/CSEResourceID/MyVirtualResourceName | 
	| **Hybrid http** |  http://ipAddress:port/~/CSEID/MyResourceID/MyVirtualResourceName<br />http://ipAddress:port/~/CSEID/CSEResourceID/MyVirtualResourceName | 

=== "Absolute"

	| Method |  Examples |
	| ------ |  -------- |
	| **Unstructured ID** |  //SPID/CSEID/MyResourceID<br />//SPID/CSEID/CSEResourceID|
	| **Unstructured http** | http://ipAddress:port/\_/SPID/CSEID/MyResourceID<br />http://ipAddress:port/\_/SPID/CSEID/MyResourceID |
	| **Structured ID** |  //SPID/CSEID/CSEName/MyAEName/MyResourceName<br />//SPID/CSEID/CSEName |
	| **Structured http** |  http://ipAddress:port/\_/SPID/CSEID/CSEName/MyAEName/MyResourceName<br />http://ipAddress:port/\_/SPID/CSEID/CSEName |
	| **Hybrid ID** |  //SPID/CSEID/MyResourceID/MyVirtualResourceName<br />//SPID/CSEID/CSEResourceID/MyVirtualResourceName |
	| **Hybrid http** |  http://ipAddress:port/\_/SPID/CSEID/MyResourceID/MyVirtualResourceName<br />http://ipAddress:port/\_/SPID/CSEID/MyResourceID/MyVirtualResourceName |


> **Notes**
>
> - A *structured path* is constructed out of the individual resourceNames of the resources that form the path. It always includes the resourceName of the CSE.
> - A *hybrid resource address* always consists of the unstructured resource ID **plus** the resourceName of a virtual resource, for example `cnt1234/la`. This is used to address, for example, the "latest" virtual resource of a container, using an unstructured address to access the container.
> - The following sequences map between special characters in the resource identifier and the http URI:

<mark>TODO: mapping to http</mark>

- `//` &lt; - > `/~/`
- `///` &lt; - > `/_/`

---
<span style="color:grey">*by Andreas Kraft, 2023-08-08*</span>

