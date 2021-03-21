---
title: Tilldela administratörs roller för Azure AD med Microsoft Graph API | Microsoft Docs
description: Tilldela och ta bort administratörs roller för Azure AD med Graph API i Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67e77c2dd08386279beeb06ff9081fa2669afa73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487842"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Tilldela anpassade administratörs roller med hjälp av Microsoft Graph API i Azure Active Directory 

Du kan automatisera hur du tilldelar roller till användar konton med hjälp av Microsoft Graph API. Den här artikeln beskriver åtgärder för att skicka, hämta och ta bort på roleAssignments.

## <a name="required-permissions"></a>Behörigheter som krävs

Anslut till din Azure AD-organisation med en global administratör eller ett administratörs konto med privilegierad roll för att tilldela eller ta bort roller.

## <a name="post-operations-on-roleassignment"></a>PUBLICERA åtgärder på RoleAssignment

### <a name="example-1-create-a-role-assignment-between-a-user-and-a-role-definition"></a>Exempel 1: skapa en roll tilldelning mellan en användare och en roll definition

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Brödtext

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  // Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Svarsåtgärder

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-2-create-a-role-assignment-where-the-principal-or-role-definition-does-not-exist"></a>Exempel 2: skapa en roll tilldelning där huvud-eller roll definitionen inte finns

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Brödtext

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  //Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Svarsåtgärder

``` HTTP
HTTP/1.1 404 Not Found
```
### <a name="example-3-create-a-role-assignment-on-a-single-resource-scope"></a>Exempel 3: skapa en roll tilldelning i ett enda resurs omfång

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Brödtext

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"e9b2b976-1dea-4229-a078-b08abd6c4f84",    //role template ID of a custom role
    "directoryScopeId":"/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"  //object ID of an application
}
```

Svarsåtgärder

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-4-create-an-administrative-unit-scoped-role-assignment-on-a-built-in-role-definition-which-is-not-supported"></a>Exempel 4: skapa en roll tilldelning som omfattas av en administrativ enhet i en inbyggd roll definition som inte stöds

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Brödtext

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"29232cdf-9323-42fd-ade2-1d097af3e4de",    //role template ID of Exchange Administrator
    "directoryScopeId":"/administrativeUnits/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"    //object ID of an administrative unit
}
```

Svarsåtgärder

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "message":"The given built-in role is not supported to be assigned to a single resource scope."
        }
    }
}
```

Endast en delmängd av inbyggda roller är aktive rad för det administrativa enhets omfånget. I [den här dokumentationen](admin-units-assign-roles.md) hittar du en lista över inbyggda roller som stöds över en administrativ enhet.

## <a name="get-operations-on-roleassignment"></a>Hämta åtgärder på RoleAssignment

### <a name="example-5-get-role-assignments-for-a-given-principal"></a>Exempel 5: Hämta roll tilldelningar för en specifik huvud konto

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId+eq+'<object-id-of-principal>'
```

Svarsåtgärder

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/"  
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
        ]
}
```

### <a name="example-6-get-role-assignments-for-a-given-role-definition"></a>Exempel 6: Hämta roll tilldelningar för en specifik roll definition.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=roleDefinitionId+eq+'<object-id-or-template-id-of-role-definition>'
```

Svarsåtgärder

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
     ]
}
```

### <a name="example-7-get-a-role-assignment-by-id"></a>Exempel 7: Hämta en roll tilldelning efter ID.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svarsåtgärder

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "directoryScopeId":"/"
}
```

### <a name="example-8-get-role-assignments-for-a-given-scope"></a>Exempel 8: Hämta roll tilldelningar för ett angivet omfång


GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=directoryScopeId+eq+'/d23998b1-8853-4c87-b95f-be97d6c6b610'
```

Svarsåtgärder

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            }
        ]
}
```

## <a name="delete-operations-on-roleassignment"></a>TA bort åtgärder på RoleAssignment

### <a name="example-9-delete-a-role-assignment-between-a-user-and-a-role-definition"></a>Exempel 9: ta bort en roll tilldelning mellan en användare och en roll definition.

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svarsåtgärder
``` HTTP
HTTP/1.1 204 No Content
```

### <a name="example-10-delete-a-role-assignment-that-no-longer-exists"></a>Exempel 10: ta bort en roll tilldelning som inte längre finns

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svarsåtgärder

``` HTTP
HTTP/1.1 404 Not Found
```

### <a name="example-11-delete-a-role-assignment-between-self-and-global-administrator-role-definition"></a>Exempel 11: ta bort en roll tilldelning mellan roll definitionen egen och global administratör

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svarsåtgärder

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Removing self from Global Administrator built-in role is not allowed"},
            "values":null
        }
    }
}
```

Vi hindrar användarna från att ta bort sin egen globala administratörs roll för att undvika ett scenario där en klient organisation har noll globala administratörer. Borttagning av andra roller som tilldelas till sig själv tillåts.

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md)
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md)