---
tags: [Getting Started]
---

# Using JSON and XML headers

The Whispir API has been designed and built to support both JSON and XML.

`Content-Type` and `Accept` headers are needed to successfully execute your calls.

Whispir’s API has implemented a versioning structure that allows application clients to choose which version of the API they would like to retrieve their responses from.

## List of Whispir’s VSMTs

The following table lists the available mime types that are accepted through Whispir’s API.

> **IMPORTANT**: The MIME Type is always singular: `message-v1`, not `messages-v1`. When not specified the API defaults to an XML answer.

| Mime type             | Headers                                                                                                     |
| --------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Workspace**         | **JSON:** application/vnd.whispir.workspace-v1+json <br />**XML:** application/vnd.whispir.workspace-v1+xml |
| **Message**           | application/vnd.whispir.message-v1+json <br />application/vnd.whispir.message-v1+xml                        |
| **Message Status** | application/vnd.whispir.messagestatus-v1+json <br /> application/vnd.whispir.messagestatus-v1+xml           |
| **Message Responses** | application/vnd.whispir.messageresponse-v1+json <br /> application/vnd.whispir.messageresponse-v1+xml           |
| **Response Rule**     | application/vnd.whispir.responserule-v1+json <br />application/vnd.whispir.responserule-v1+xml        |
| **Template**          | application/vnd.whispir.template-v1+json <br />application/vnd.whispir.template-v1+xml                      |
| **Contact**           | application/vnd.whispir.contact-v1+json <br />application/vnd.whispir.contact-v1+xml                        |
| **Distribution List** | application/vnd.whispir.distributionlist-v1+json <br />application/vnd.whispir.distributionlist-v1+xml      |
| **Scenario**          | application/vnd.whispir.scenario-v1+json <br />application/vnd.whispir.scenario-v1+xml                      |
| **Event**             | application/vnd.whispir.event-v1+json <br />application/vnd.whispir.event-v1+xml                            |
| **Callback**          | application/vnd.whispir.api-callback-v1+json <br />application/vnd.whispir.api-callback-v1+xml              |
| **Asset**             | application/vnd.whispir.asset-v1+json <br />application/vnd.whispir.asset-v1+xml                            |
| **Custom List**       | application/vnd.whispir.customlist-v1+json <br />application/vnd.whispir.customlist-v1+xml                  |
| **Activity Log**      | application/vnd.whispir.activity-v1+json <br />application/vnd.whispir.activity-v1+xml                      |
| **User**              | Japplication/vnd.whispir.user-v1+json <br />application/vnd.whispir.user-v1+xml                             |
| **Resource**          | application/vnd.whispir.resource-v1+json <br />application/vnd.whispir.resource-v1+xml                      |
| **Import**            | application/vnd.whispir.importcontact-v1+json <br />application/vnd.whispir.importcontact-v1+xml            |

## Without VSMT

This implementation of an API in this manner works correctly, but conceptually it's incorrect. The issue with this design is that the request is only asking for an XML representation of some resource called a contact; it's not specifically asking for the XML version of a contact resource as defined by Whispir’s API.

Any XML representation of a resource could be passed back (for example, a cat or a house), and the client would need to inspect the response to determine whether it's a contact or not through its own means.

```JSON
GET /workspaces/123/contacts?firstName=Neil
Accept: application/json
 
{
    "contact": {
        "name": "Neil Armstrong"
    }
}
```

```XML
GET /workspaces/123/contacts?firstName=Neil
Accept: application/xml
 
<contact>
   <name>Neil Armstrong</name>
</contact>
```

## With VSMT

By using VSMT, Whispir can define and make available the various content types for resources prior to the requests being made. This allows the application clients to specify the resource that they would like to receive from the API, and Whispir will only return content of that specific type.

For example, by using this method, the client is specifically asking for a resource representation of a contact that is defined by Whispir’s API. There is no confusion about the representation that will be returned, and the client doesn’t need to worry about validation as Whispir will only ever return a valid contact as a response to this request.

VSMT as well allows the client the ability to choose the language their resource representation should be returned in.

```JSON
GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact+json
 
{
    "contact": {
        "name": "Neil Armstrong"
    }
}
```

```XML
GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact+xml
 
<contact>
   <name>Neil Armstrong</name>
</contact>
```

## With VSMT (including versioning)

This method of using VSMT also allows the resource representations to be updated, re-written and maintained without any notification required to application clients. It is achieved by adding a version element to the defined content types.

By versioning the application MIME types, application clients can request the resource representation that their application is built on. For example, ‘contact-v1’, or ‘workspace-v2’.

Whispir can create new representations of these documents, and the application clients will not be affected by these changes.

The v2 version of this resource representation can co-exist with the v1 version, and application clients don't need to worry about their existence.

## Deprecation of versions

As the version numbers grow and new features are introduced into the resource representations, it's inevitable that older versions will be deprecated and no longer supported.

This process of deprecation will be facilitated using HTTP status codes `301` and `415`.

```JSON
With VSMT (V1)
Sample request with VSMT and Versioning (V1)

GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact-v1+json
 
{
    "contact": {
        "name": "Neil Armstrong"
    }
}

With VSMT (V2)
GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact-v2+json
 
{
    "contact": {
        "name": "Neil Armstrong"
        "contactMobile": "61400400400"
        "contactEmail": "neil.armstrong@space.com"
    }
}
```

```XML
With VSMT (V1)
Sample request with VSMT and Versioning (V1)

GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact-v1+xml
 
<contact>
   <name>Neil Armstrong</name>
</contact>

With VSMT (V2)
GET /workspaces/123/contacts?firstName=Neil
Accept: application/vnd.whispir.contact-v2+xml
 
<contact>
   <name>Neil Armstrong</name>
   <contactMobile>61400400400</contactMobile>
   <contactEmail>neil.armstrong@space.com</contactEmail>
</contact>
```
