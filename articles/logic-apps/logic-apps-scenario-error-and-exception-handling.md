---
title: Undantags hantering & fel loggnings scenario
description: Verkligt användnings fall och scenario för avancerad undantags hantering och fel loggning i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000359"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Undantagshantering och felloggning för logikappar

I det här scenariot beskrivs hur du kan utöka en Logic app för att bättre stödja undantags hantering. Vi har använt ett real tid för att besvara frågan: "Azure Logic Apps stöd för undantag och fel hantering?"

> [!NOTE]
> Det aktuella Azure Logic Apps schemat innehåller en standardmall för åtgärds svar. Den här mallen innehåller både interna validerings-och fel svar som returneras från en API-app.

## <a name="scenario-and-use-case-overview"></a>Översikt över scenarier och användnings fall

Här är historien som användnings fall för det här scenariot: 

En välkänd sjukvårds organisation som gör det möjligt för oss att utveckla en Azure-lösning som skapar en patient Portal med hjälp av Microsoft Dynamics CRM Online. De behövde skicka poster för Mötes poster mellan online-portalen för Dynamics CRM Online och Salesforce. Vi har bett att använda [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) -standarden för alla patient poster.

Projektet hade två viktiga krav:  

* En metod för att logga poster som skickas från Dynamics CRM Online-portalen
* Ett sätt att visa eventuella fel som inträffat i arbets flödet

> [!TIP]
> En övergripande video om det här projektet finns i [integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integrations användar grupp").

## <a name="how-we-solved-the-problem"></a>Hur vi löste problemet

Vi valde [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") som en lagrings plats för logg-och fel posterna (Cosmos DB refererar till poster som dokument). Eftersom Azure Logic Apps har en standardmall för alla svar behöver vi inte skapa ett anpassat schema. Vi kunde skapa en API-app för att **Infoga** och **fråga** efter både fel-och logg poster. Vi kan också definiera ett schema för var och en i API-appen.  

Ett annat krav var att rensa poster efter ett visst datum. Cosmos DB har en egenskap som heter [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL), vilket gör att vi kan ange ett **Time to Live** värde för varje post eller samling. Den här funktionen eliminerar behovet av att manuellt ta bort poster i Cosmos DB.

> [!IMPORTANT]
> För att slutföra den här självstudien måste du skapa en Cosmos DB-databas och två samlingar (loggning och fel).

## <a name="create-the-logic-app"></a>Skapa logikappen

Det första steget är att skapa Logic app och öppna appen i Logic App Designer. I det här exemplet använder vi logiska appar över-underordnad. Vi antar att vi redan har skapat den överordnade och kommer att skapa en underordnad Logic-app.

Vi kommer att logga den post som kommer från Dynamics CRM Online, så vi börjar överst. Vi måste använda en **begär** ande utlösare eftersom den överordnade Logic-appen utlöser det underordnade objektet.

### <a name="logic-app-trigger"></a>Logic app-utlösare

Vi använder en **begäran** -utlösare som visas i följande exempel:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Steg

Vi måste logga källan (förfrågan) för patient posten från Dynamics CRM Online-portalen.

1. Vi måste hämta en ny post för avtalade tider från Dynamics CRM Online.

   Utlösaren som kommer från CRM förser oss med **CRM-PatentId**, **post typen**, **ny eller uppdaterad post** (nytt eller uppdatera booleskt värde) och **SalesforceId**. **SalesforceId** kan vara null eftersom det endast används för en uppdatering.
   CRM-posten hämtas med hjälp av CRM- **PatientID** och **post typen**.

2. Därefter måste vi lägga till Azure Cosmos DB vår **InsertLogEntry** -åtgärd för SQL API-app som visas här i Logic App Designer.

   **Infoga loggpost**

   ![Skärm bild från Logic app designer som visar konfigurations inställningarna för InsertLogEntry.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Infoga fel post**

   ![Skärm bild från Logic app designer som visar konfigurations inställningarna för CreateErrorRecord.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sök efter åtgärden för att skapa post**

   ![Skärm bild av CreateErrorRecord-skärmen i Logic app designer som visar fälten för att skapa en fel post.](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Käll kod för Logic app

> [!NOTE]
> Följande exempel är exempel. Eftersom den här självstudien baseras på en implementering nu i produktion, kanske värdet för en **källnod** inte visar egenskaper som är relaterade till schemaläggning av en avtalad tid. > 

### <a name="logging"></a>Loggning

I följande kod exempel kod exempel visas hur du hanterar loggning.

#### <a name="log-entry"></a>Loggpost

Här är käll koden för Logic app för att infoga en loggpost.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Logg förfrågan

Här är meddelandet om logg begär Ande som publicerats i API-appen.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Logg svar

Här är logg svars meddelandet från API-appen.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Nu ska vi titta på fel hanterings stegen.

### <a name="error-handling"></a>Felhantering

Följande kod exempel kod exempel visar hur du kan implementera fel hantering.

#### <a name="create-error-record"></a>Skapa fel post

Här är Logic app-källkoden för att skapa en fel post.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Infognings fel i Cosmos DB--Request

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Infognings fel i Cosmos DB--Response

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Fel svar för Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Returnera svaret tillbaka till överordnad Logic-app

När du får svaret kan du skicka tillbaka svaret till den överordnade Logic-appen.

#### <a name="return-success-response-to-parent-logic-app"></a>Returnera lyckade svar till överordnad Logic-app

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Returnera fel svar till överordnad Logic app

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB databas och Portal

Vår lösning lade till funktioner med [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Fel hanterings Portal

Om du vill visa felen kan du skapa en MVC-webbapp för att visa fel posterna från Cosmos DB. Åtgärderna **lista**, **information**, **Redigera** och **ta bort** ingår i den aktuella versionen.

> [!NOTE]
> Redigera åtgärd: Cosmos DB ersätter hela dokumentet. De poster som visas i vyerna **lista** och **Detaljer** är exempel. De är inte faktiska poster för den avtalade tiden.

Följande är exempel på information om MVC-appar som skapats med den tidigare beskrivna metoden.

#### <a name="error-management-list"></a>Lista över fel hantering
![fellista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Detaljerad vy för fel hantering
![Information om fel](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Logg hanterings Portal

För att visa loggarna skapade vi även en MVC-webbapp. Följande är exempel på information om MVC-appar som skapats med den tidigare beskrivna metoden.

#### <a name="sample-log-detail-view"></a>Detaljvy över exempel logg
![Vyn logg information](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Information om API-app

#### <a name="logic-apps-exception-management-api"></a>API för Logic Apps undantags hantering

Vår API-app med öppen källkod Azure Logic Apps undantags hantering innehåller funktioner som beskrivs här – det finns två styrenheter:

* **ErrorController** infogar en felpost (dokument) i en Azure Cosmos DB-samling.
* **LogController** Infogar en loggpost (dokument) i en Azure Cosmos DB-samling.

> [!TIP]
> Båda styrenheterna använder `async Task<dynamic>` åtgärder, vilket gör det möjligt att lösa åtgärder vid körning, så att vi kan skapa Azure Cosmos DB-schemat i bröd texten i åtgärden. 
> 

Varje dokument i Azure Cosmos DB måste ha ett unikt ID. Vi använder `PatientId` och lägger till en tidstämpel som konverteras till ett Unix-tidsstämpel-värde (Double). Vi trunkerar värdet för att ta bort bråk talet.

Du kan visa käll koden för vårt fel kontrolls-API från [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Vi anropar API: et från en Logic app med hjälp av följande syntax:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Uttrycket i föregående kod exempel kontrollerar om *Create_NewPatientRecord* statusen **misslyckades**.

## <a name="summary"></a>Sammanfattning

* Du kan enkelt implementera loggning och fel hantering i en Logic app.
* Du kan använda Azure Cosmos DB som lagrings plats för logg-och fel poster (dokument).
* Du kan använda MVC för att skapa en portal för att Visa logg-och fel poster.

### <a name="source-code"></a>Källkod

Käll koden för API-programmet för Logic Apps undantags hantering finns i den här [GitHub-lagringsplatsen](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API för undantags hantering i Logic app").

## <a name="next-steps"></a>Nästa steg

* [Visa fler exempel och scenarier för Logic-appar](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Övervaka logikappar](../logic-apps/monitor-logic-apps.md)
* [Automatisera distributionen av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
