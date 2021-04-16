---
title: Så här konfigurerar du molnsynkronisering programmässigt med MS Graph API
description: Det här avsnittet beskriver hur du aktiverar inkommande synkronisering med bara Graph API
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530265"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Så här konfigurerar du molnsynkronisering programmässigt med MS Graph API

I följande dokument beskrivs hur du replikerar en synkroniseringsprofil från grunden med endast MSGraph-API:er.  
Strukturen för hur du gör detta består av följande steg.  De är:

- [Så här konfigurerar du molnsynkronisering programmässigt med MS Graph API](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Grundläggande konfiguration](#basic-setup)
    - [Aktivera klientflaggor](#enable-tenant-flags)
  - [Skapa tjänsthuvudnamn](#create-service-principals)
  - [Skapa synkroniseringsjobb](#create-sync-job)
  - [Uppdatera måldomänen](#update-targeted-domain)
  - [Aktivera synkronisering av lösenordshashar på konfigurationsbladet](#enable-sync-password-hashes-on-configuration-blade)
  - [Oavsiktliga borttagningar](#accidental-deletes)
    - [Aktivera och ange tröskelvärdet](#enabling-and-setting-the-threshold)
    - [Tillåta borttagningar](#allowing-deletes)
  - [Starta synkroniseringsjobb](#start-sync-job)
  - [Granska status](#review-status)
  - [Nästa steg](#next-steps)

Använd [](/powershell/module/msonline/) dessa Microsoft Azure Active Directory-modul för Windows PowerShell kommandon för att aktivera synkronisering för en produktionsklient, vilket är ett krav för att kunna anropa administrationswebbtjänsten för den klientorganisationen.

## <a name="basic-setup"></a>Grundläggande konfiguration

### <a name="enable-tenant-flags"></a>Aktivera klientflaggor

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Det första av dessa två kommandon kräver Azure Active Directory autentiseringsuppgifter. Dessa kommandon identifierar implicit klientorganisationen och aktiverar den för synkronisering.

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
Därefter måste vi skapa [AD2AAD-programmet/tjänstens huvudnamn](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)

Du måste använda det här program-ID:t 1a4721b3-e57f-4451-ae87-ef078703ec94. displayName är AD-domän-URL:en, om den används i portalen (till exempel contoso.com), men den kan få ett annat namn.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Skapa synkroniseringsjobb
Utdata från kommandot ovan returnerar objectId för tjänstens huvudnamn som skapades. I det här exemplet är objectId 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Använd Microsoft Graph för att lägga till ett synkroniseringsjobb till tjänstens huvudnamn.  

Dokumentation för att skapa ett synkroniseringsjobb finns [här.](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)

Om du inte antecknade ID:t ovan kan du hitta tjänstens huvudnamn genom att köra följande MS Graph-anrop. Du behöver behörigheterna Directory.Read.All för att göra anropet:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Leta sedan efter appnamnet i utdata.

Kör följande två kommandon för att skapa två jobb: ett för användare/gruppetablering och ett för synkronisering av lösenordshashar. Det är samma begäran två gånger men med olika mall-ID:er.


Anropa följande två begäranden:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Du behöver två anrop om du vill skapa båda.

Exempel på returvärde (för etablering):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Uppdatera måldomänen
För den här klientorganisationen är objektidentifieraren och programidentifieraren för tjänstens huvudnamn följande:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-000000000000 DisplayName: testApp

Vi kommer att behöva uppdatera domänen som den här konfigurationen är riktad mot, så uppdatera hemligheterna för den här domänen.

Kontrollera att domännamnet du använder är samma URL som du har angett för den lokala domänkontrollanten

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Lägg till följande nyckel/värde-par i värdematrisen nedan baserat på vad du försöker göra:
 - Aktivera både PHS och synkronisera klientflaggor { nyckel: "AppKey", värde: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Aktivera endast synkronisering av klientflagga (aktivera inte PHS) { nyckel: "AppKey", värde: "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

Det förväntade svaret är ... HTTP 204/Inget innehåll

Här är det markerade värdet "Domän" namnet på lokal Active Directory domän som poster ska etableras till Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Aktivera synkronisering av lösenordshashar på konfigurationsbladet

 I det här avsnittet går vi in på hur du aktiverar synkronisering av lösenordshashar för en viss konfiguration. Detta skiljer sig från AppKey-hemligheten som aktiverar funktionsflaggan på klientnivå – detta gäller endast för en enskild domän/konfiguration. Du måste ange programnyckeln till PHS-nyckeln för att detta ska fungera från slut till slut.

1. Hämta schemat (varning om att det är ganska stort) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Ta den här attributmappningen för CredentialData:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Hitta följande objektmappningar med följande namn i schemat
 - Etablera Active Directory-användare
 - Etablera Active Directory inetOrgPersons

 Objektmappningar finns i schema.synchronizationRules[0].objectMappings (för tillfället kan du anta att det bara finns en synkroniseringsregel)

4. Ta CredentialData-mappningen från steg (2) och infoga den i objektmappningarna i steg (3)

 Objektmappningen ser ut ungefär så här:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Kopiera/klistra in mappningen från steget **Create AD2AADProvisioning and AD2AADPasswordHash (Skapa AD2AADProvisioning och AD2AADPasswordHash)** ovan i matrisen attributeMappings. 

 Ordningen på elementen i den här matrisen spelar ingen roll (backend sorteras åt dig). Var försiktig med att lägga till den här attributmappningen om namnet redan finns i matrisen (t.ex. om det redan finns ett objekt i attributeMappings som har targetAttributeName CredentialData) – du kan få konfliktfel, eller så kan de redan existerande och nya mappningarna kombineras (vanligtvis inte önskat resultat). Backend dedupliceras inte åt dig. 

 Kom ihåg att göra detta för både användare och inetOrgpersons

5. Spara schemat som du har skapat 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Lägg till schemat i begärandetexten. 

## <a name="accidental-deletes"></a>Oavsiktliga borttagningar
Det här avsnittet visar hur du programmässigt aktiverar/inaktiverar och använder [oavsiktliga borttagningar](how-to-accidental-deletes.md) programmatiskt.


### <a name="enabling-and-setting-the-threshold"></a>Aktivera och ange tröskelvärdet
Det finns två per jobbinställningar som du kan använda, de är:

 - DeleteThresholdEnabled – Aktiverar oavsiktligt borttagningsskydd för jobbet när det är inställt på "true". Ange till "true" som standard.
 - DeleteThresholdValue – Definierar det maximala antalet borttagningar som tillåts i varje körning av jobbet när skydd mot oavsiktliga borttagningar är aktiverat. Värdet är inställt på 500 som standard.  Så om värdet är inställt på 500 är det maximala antalet tillåtna borttagningar 499 i varje körning.

Tröskelvärdesinställningarna för borttagning är en del av `SyncNotificationSettings` och kan ändras via grafen. 

Vi måste uppdatera SyncNotificationSettings som den här konfigurationen är riktad mot, så uppdatera hemligheterna.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Lägg till följande nyckel/värde-par i värdematrisen nedan baserat på vad du försöker göra:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

Inställningen "Aktiverad" i exemplet ovan är för att aktivera/inaktivera e-postmeddelanden när jobbet har satts i karantän.


För närvarande stöder vi inte PATCH-begäranden för hemligheter, så du måste lägga till alla värden i brödtexten i PUT-begäran (som i exemplet ovan) för att bevara de andra värdena.

Befintliga värden för alla hemligheter kan hämtas med hjälp av 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Tillåt borttagningar
Om du vill tillåta att borttagningarna flödar igenom efter att jobbet har förts i karantän måste du utfärda en omstart med bara "ForceDeletes" som omfång. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Starta synkroniseringsjobb
Jobbet kan hämtas igen via följande kommando:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentation för att hämta jobb finns [här.](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true) 
 
Starta jobbet genom att utfärda den här begäran med objectId för tjänstens huvudnamn som skapades i det första steget och jobbidentifieraren som returnerades från begäran som skapade jobbet.

Dokumentation om hur du startar ett jobb finns [här](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Det förväntade svaret är ... HTTP 204/Inget innehåll.

Andra kommandon för att kontrollera jobbet dokumenteras [här](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true).
 
Om du vill starta om ett jobb använder man ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Granska status
Hämta dina jobbstatusar via ...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Titta under statusavsnittet i returobjektet för relevant information

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect molnsynkronisering?](what-is-cloud-sync.md)
- [Transformeringar](how-to-transformation.md)
- [Synkroniserings-API för Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
