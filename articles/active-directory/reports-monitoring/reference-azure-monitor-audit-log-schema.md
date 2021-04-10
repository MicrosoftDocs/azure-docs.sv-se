---
title: Tolka Azure Active Directory Gransknings logg schema i Azure Monitor | Microsoft Docs
description: Beskriv Azure AD audit log-schemat för användning i Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d32e0b939731d64f3ef2495ab606b5dc0780e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592287"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Tolka schemat för gransknings loggar för Azure AD i Azure Monitor (för hands version)

Den här artikeln beskriver Gransknings logg schema för Azure Active Directory (Azure AD) i Azure Monitor. Varje enskild loggpost lagras som text och formateras som en JSON-BLOB, som du ser i följande två exempel: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Fält-och egenskaps beskrivningar

| Fältnamn | Beskrivning |
|------------|-------------|
| time       | Datum och tid (UTC). |
| operationName | Åtgärdens namn. |
| operationVersion | Den REST API version som begärs av klienten. |
| category | För närvarande är *granskning* det enda värde som stöds. |
| tenantId | Klient-GUID som är associerat med loggarna. |
| resultType | Resultatet av åtgärden. Resultatet kan vara *lyckat* eller *misslyckat*. |
| resultSignature |  Det här fältet är omappat och du kan ignorera det på ett säkert sätt. | 
| resultDescription | En ytterligare beskrivning av resultatet, där det är tillgängligt. | 
| durationMs |  Det här fältet är omappat och du kan ignorera det på ett säkert sätt. |
| callerIpAddress | IP-adressen för klienten som gjorde begäran. | 
| correlationId | Ett valfritt GUID som skickas av klienten. Det kan bidra till att korrelera åtgärder på klient sidan med åtgärder på Server sidan och det är användbart när du spårar loggar som omfattar tjänster. |
| identity | Identiteten från den token som angavs när du gjorde begäran. Identiteten kan vara ett användar konto, ett system konto eller ett huvud namn för tjänsten. |
| nivå | Meddelande typen. För gransknings loggar är nivån alltid *information*. |
| location | Platsen för data centret. |
| properties | Visar en lista över de egenskaper som stöds och som är relaterade till en Gransknings logg. Mer information finns i nästa tabell. | 

<br>

| Egenskapsnamn | Beskrivning |
|---------------|-------------|
| AuditEventCategory | Typ av gransknings händelse. Det kan vara *användar hantering*, *program hantering* eller någon annan typ.|
| Identitets typ | Typen kan vara *program* eller *användare*. |
| Åtgärdstyp | Typen kan vara *Lägg till*, *Uppdatera* och *ta bort*. eller *annat*. |
| Mål resurs typ | Anger den mål resurs typ som åtgärden utfördes på. Typen kan vara *program*, *användare*, *roll*, *princip* | 
| Mål resurs namn | Namnet på mål resursen. Det kan vara ett program namn, ett rollnamn, ett User Principal Name eller ett huvud namn för tjänsten. |
| additionalTargets | Visar alla ytterligare egenskaper för vissa åtgärder. För en uppdaterings åtgärd visas till exempel de gamla värdena och de nya värdena under *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Azure Diagnostics-loggar](../../azure-monitor/essentials/platform-logs-overview.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)