---
title: 'Använda Azure blockchain Workbench REST API: er'
description: Scenarier för hur du använder för hands versionen av Azure blockchain Workbench REST API
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004848"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Använda Azure blockchain Workbench Preview REST API

Azure blockchain Workbench Preview REST API ger utvecklare och informations anställda ett sätt att bygga omfattande integreringar för blockchain-program. I den här artikeln beskrivs flera olika scenarier för hur du använder Workbench REST API. Anta till exempel att du vill skapa en anpassad blockchain-klient som tillåter att inloggade användare kan visa och interagera med sina tilldelade blockchain-program. Klienten kan använda blockchain Workbench API för att visa kontrakt instanser och vidta åtgärder för smarta kontrakt.

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API-slutpunkt

Blockchain Workbench-API: er nås via en slut punkt för din distribution. Hämta API-slutpunktens URL för din distribution:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det vänstra navigerings fönstret väljer du **resurs grupper**.
1. Välj resurs gruppens namn din distribuerade blockchain Workbench.
1. Välj kolumn rubriken **typ** för att sortera listan alfabetiskt efter typ.
1. Det finns två resurser av typen **App Service**. Välj resurs av typen **App Service** *med* suffixet "-API".
1. I App Service **Översikt** kopierar du **URL** -värdet, som representerar API-slutpunktens URL till din distribuerade blockchain Workbench.

    ![URL för App Service API-slutpunkt](media/use-api/app-service-api.png)

## <a name="authentication"></a>Autentisering

Begär anden till blockchain Workbench REST API skyddas med Azure Active Directory (Azure AD).

Om du vill göra en autentiserad begäran till REST-API: erna kräver klient koden autentisering med giltiga autentiseringsuppgifter innan du kan anropa API: et. Autentiseringen koordineras mellan de olika aktörerna av Azure AD och ger klienten en [åtkomsttoken](../../active-directory/develop/developer-glossary.md#access-token) som bevis på autentiseringen. Token skickas sedan i HTTP-Authorization-huvudet för REST API begär Anden. Mer information om Azure AD-autentisering finns i [Azure Active Directory för utvecklare](../../active-directory/develop/index.yml).

Exempel på hur du autentiserar finns i [REST API exempel](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) .

## <a name="using-postman"></a>Använda Postman

Om du vill testa eller experimentera med Workbench-API: er kan du använda [Postman](https://www.postman.com) för att skapa API-anrop till din distribution. [Hämta en exempel Postman-samling av Workbench API-begäranden](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) från GitHub. I README-filen finns mer information om hur du autentiserar och använder exempel-API-begäranden.

## <a name="create-an-application"></a>Skapa ett program

Du kan använda två API-anrop för att skapa ett blockchain Workbench-program. Den här metoden kan endast utföras av användare som är Workbench-administratörer.

Använd [program post-API: et](/rest/api/azure-blockchain-workbench/applications/applicationspost) för att ladda upp programmets JSON-fil och hämta ett program-ID.

### <a name="applications-post-request"></a>Ansökan om program POST

Använd parametern **appFile** för att skicka konfigurations filen som en del av begär ande texten.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Program efter svar

Det skapade program-ID: t returneras i svaret. Du behöver program-ID: t för att associera konfigurations filen med kod filen när du anropar nästa API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Kontrakt kod POST förfrågan

Använd [program kontrakt koden post-API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) genom att skicka program-ID: t för att ladda upp programmets solide Code-fil. Nytto lasten kan vara en enda Solidity-fil eller en zippad fil som innehåller täckande filer.

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| ApplicationId | Returnera värdet från program POST-API: et. |
| {ledgerId} | Index för redovisningen. Värdet är vanligt vis 1. Du kan också kontrol lera [redovisnings tabellen](data-sql-management-studio.md) för värdet. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Svar på kontrakts kod POST

Om det lyckas innehåller svaret det skapade kontrakt kod-ID: t från [tabellen ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Tilldela roller till användare

Använd [program roll tilldelningarna post-API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) genom att skicka in program-ID, användar-ID och program roll-ID för att skapa en mappning mellan användare och roll i det angivna blockchain-programmet. Den här metoden kan endast utföras av användare som är Workbench-administratörer.

### <a name="role-assignments-post-request"></a>POST-begäran för roll tilldelningar

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| ApplicationId | Returnera värdet från program POST-API: et. |
| UserID | Användar-ID-värde från [tabellen användare](data-sql-management-studio.md). |
| {applicationRoleId} | ID-värde för program roll kopplat till program-ID från [tabellen ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Roll tilldelningar efter svar

Om det lyckas innehåller svaret det skapade roll tilldelnings-ID: t från [tabellen RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Lista program

Använd [programmen Hämta API](/rest/api/azure-blockchain-workbench/applications/applicationsget) för att hämta alla blockchain Workbench-program för användaren. I det här exemplet har den inloggade användaren åtkomst till två program:

- [Tillgångsöverföring](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Kyld transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Begäran om hämtning av program

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Svar för hämtning av program

Svaret visar en lista över alla blockchain-program som en användare har åtkomst till i blockchain Workbench. Blockchain Workbench-administratörer får alla blockchain-program. Icke-Workbench-administratörer får alla blockchain-program som de har minst en associerad program roll eller en tillhör ande roll för smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Lista arbetsflöden för ett program

Använd [program arbets flöden Hämta API](/rest/api/azure-blockchain-workbench/applications/workflowsget) för att visa en lista över alla arbets flöden för ett angivet blockchain-program som en användare har åtkomst till i blockchain Workbench. Alla blockkedjeprogram har ett eller flera arbetsflöden och varje arbetsflöde har noll eller fler instanser för smarta kontrakt. För ett blockchain-klientprogram som bara har ett arbets flöde rekommenderar vi att du hoppar över användar upplevelsen som gör att användarna kan välja lämpligt arbets flöde.

### <a name="application-workflows-request"></a>Begäran om program arbets flöden

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Svar på program arbets flöden

Blockchain Workbench-administratörer får alla blockchain-arbetsflöden. Icke-Workbench-administratörer får alla arbets flöden för vilka de har minst en associerad program roll eller associerats med en roll för en smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Skapa en kontrakt instans

Använd [kontrakt v2 post-API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) för att skapa en ny smart kontrakts instans för ett arbets flöde. Användare kan bara skapa en ny smart kontrakts instans om användaren är associerad med en program roll, vilket kan initiera en smart kontrakts instans för arbets flödet.

> [!NOTE]
> I det här exemplet används version 2 av API: t. Version 2 kontrakts-API: er ger mer detaljerad information om de associerade ProvisioningStatus-fälten.

### <a name="contracts-post-request"></a>Kontrakt POST förfrågan

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| WorkflowId | Värdet för arbets flödes-ID är kontraktets ConstructorID från [arbets flödes tabellen](data-sql-management-studio.md). |
| {contractCodeId} | Kontrakts kod-ID-värde från [tabellen ContractCode](data-sql-management-studio.md). Korrelera program-ID och ID för den avtals instans som du vill skapa. |
| ConnectionId | Anslutnings-ID-värde från [anslutnings tabellen](data-sql-management-studio.md). |

Ange värden med följande information för begär ande texten:

| Parameter | Värde |
|-----------|-------|
| workflowFunctionID | ID från [tabellen WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Namn värde par för parametrar som skickas till konstruktorn. Använd värdet workflowFunctionParameterID från tabellen [WorkflowFunctionParameter](data-sql-management-studio.md) för varje parameter. |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Kontrakt efter svar

Om det lyckas returnerar roll tilldelnings-API: et ContractActionID från [tabellen ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lista smarta kontrakt-instanser för ett arbetsflöde

Använd [kontrakt Hämta API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) för att visa alla smarta kontrakt instanser för ett arbets flöde. Eller så kan du ge användarna en djup inblick i alla visade smarta kontrakt instanser.

### <a name="contracts-request"></a>Kontrakts förfrågan

Ta till exempel i det här fallet en användare som vill interagera med någon av instanserna för det smarta kontrakt som ska utföras.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Kontrakts svar

Svaret visar alla smarta kontrakt instanser för det angivna arbets flödet. Workbench-administratörer får alla smarta kontrakt instanser. Icke-Workbench-administratörer får varje smart kontrakts instans för vilken de har minst en associerad program roll eller associeras med en roll för en smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Lista tillgängliga åtgärder för ett kontrakt

Använd [kontrakt åtgärd Hämta API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) för att visa tillgängliga användar åtgärder enligt kontraktets status. 

### <a name="contract-action-request"></a>Kontrakts åtgärds förfrågan

I det här exemplet tittar användaren på alla tillgängliga åtgärder för ett nytt smart kontrakt som de har skapat.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Kontrakt åtgärds svar

Svaret visar alla åtgärder som en användare kan utföra utifrån det aktuella tillståndet för den angivna instansen för det smarta kontraktet.

* Ändra: Låter användaren ändra beskrivning och pris för en tillgång.
* Avsluta: tillåter att användaren avslutar kontraktet för till gången.

Användarna får alla tillämpliga åtgärder om användaren har en associerad programroll eller är associerad med en smart kontrakt-instansroll för det aktuella tillståndet för den angivna smarta kontrakt-instansen.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Utföra en åtgärd för ett kontrakt

Använd [kontrakt åtgärd post-API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) för att vidta åtgärder för den angivna smarta kontrakts instansen.

### <a name="contract-action-post-request"></a>POST förfrågan för kontrakt åtgärd

I det här fallet bör du tänka på scenariot där en användare vill ändra en till gångs beskrivning och pris.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Användare kan bara utföra åtgärden utifrån det aktuella tillståndet för de specificerade smarta instanserna för det smarta kontraktet och användarens associerade programroll eller rollen för instansen för det smarta kontraktet.

### <a name="contract-action-post-response"></a>Kontrakt åtgärd efter svar

Om posten lyckas, returneras ett HTTP 200 OK-svar utan svarstext.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nästa steg

Referensinformation om blockchain Workbench-API: er finns i [referens för Azure blockchain workbench REST API](/rest/api/azure-blockchain-workbench).
