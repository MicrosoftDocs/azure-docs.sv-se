---
title: Program mässigt konfigurera molnbaserad synkronisering med MS Graph API
description: I det här avsnittet beskrivs hur du aktiverar inkommande synkronisering med bara Graph API
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
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593171"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Program mässigt konfigurera molnbaserad synkronisering med MS Graph API

I följande dokument beskrivs hur du replikerar en Synkroniseringsregel från grunden med endast MSGraph-API: er.  
Strukturen för hur du gör detta består av följande steg.  De är:

- [Grundläggande konfiguration](#basic-setup)
- [Skapa tjänstens huvud namn](#create-service-principals)
- [Skapa synkroniseringsjobb](#create-sync-job)
- [Uppdatera riktad domän](#update-targeted-domain)
- [Aktivera synkronisering av lösen ords-hashar](#enable-sync-password-hashes-on-configuration-blade)
- [Oavsiktliga borttagningar](#accidental-deletes)
- [Starta synkroniseringsjobb](#start-sync-job)
- [Gransknings status](#review-status)

Använd dessa [Microsoft Azure Active Directory-modul för Windows PowerShell](/powershell/module/msonline/) -kommandon för att aktivera synkronisering för en produktions klient, ett krav för att kunna anropa administrations webb tjänsten för den klienten.

## <a name="basic-setup"></a>Grundläggande konfiguration

### <a name="enable-tenant-flags"></a>Aktivera klient flaggor

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Den första av de två kommandona kräver Azure Active Directory autentiseringsuppgifter. Dessa cmdletarna identifierar klienten implicit och aktiverar den för synkronisering.

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
Sedan måste vi skapa [AD2AAD-program/tjänstens huvud namn](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Du måste använda detta program-ID 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName är AD-domänens URL, om den används i portalen (till exempel contoso.com), men den kan ha ett annat namn.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Skapa synkroniseringsjobb
Utdata från kommandot ovan returnerar objectId för det tjänst huvud namn som skapades. I det här exemplet är objectId 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Använd Microsoft Graph för att lägga till en synchronizationJob till tjänstens huvud namn.  

Dokumentation om hur du skapar ett synkroniseringsjobb finns [här](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta).

Om du inte har loggat in med ID: t ovan kan du hitta tjänstens huvud namn genom att köra följande MS Graph-anrop. Du behöver Directory. Read. alla behörigheter för att utföra det anropet:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Leta sedan efter appens namn i utdata.

Kör följande två kommandon för att skapa två jobb: ett för etablering av användare/grupp och en för synkronisering av lösen ords-hash. Det är samma begäran två gånger, men med olika mall-ID: n.


Anropa följande två begär Anden:

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

Exempel retur värde (för etablering):

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

## <a name="update-targeted-domain"></a>Uppdatera riktad domän
För den här klienten är objekt identifieraren och program identifieraren för tjänstens huvud namn följande:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Vi kommer att behöva uppdatera den domän som den här konfigurationen är riktad till, så uppdatera hemligheterna för den här domänen.

Kontrol lera att domän namnet du använder är samma URL som du har angett för lokal-domänkontrollanten

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Lägg till följande nyckel/värde-par i nedanstående värde mat ris baserat på vad du försöker göra:
 - Aktivera både PHS och Sync-innehavaradministratör {Key: "AppKey", värde: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Aktivera endast flaggan för synkronisering av klient organisation (Aktivera inte PHS) {Key: "AppKey", värde: "{" appKeyScenario ":" AD2AADProvisioning "}"}
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

Förväntat svar är... HTTP 204/inget innehåll

Här är det markerade "domän"-värdet namnet på den lokala Active Directory domän från vilken poster ska tillhandahållas till Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Aktivera synkronisering av lösen ords hashar på konfigurations bladet

 Det här avsnittet beskriver hur du aktiverar synkronisering av lösen ords-hashar för en viss konfiguration. Detta skiljer sig från den AppKey-hemlighet som aktiverar funktions flaggan på klient nivå. Detta gäller endast för en domän/konfig. Du måste ange program nyckeln till den PHS som ska användas för att sluta till slutet.

1. Ta schemat (varningen är ganska stor) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Ta den här CredentialData:
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
3. Hitta följande objekt mappningar med följande namn i schemat
 - Etablera Active Directory användare
 - Etablera Active Directory-inetOrgPerson

 Objekt mappningar finns i schemat. synchronizationRules [0]. objectMappings (för närvarande kan du anta att det bara finns en Synkroniseringsregel)

4. Ta CredentialData-mappningen från steg (2) och infoga den i objekt mappningarna i steg (3)

 Objekt mappningen ser ut ungefär så här:
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
 Kopiera/klistra in mappningen från steget **skapa AD2AADProvisioning och AD2AADPasswordHash-jobb** ovan till attributeMappings-matrisen. 

 Element ordningen i den här matrisen spelar ingen roll (Server delen sorteras efter dig). Var försiktig med att lägga till denna attributmappning om namnet redan finns i matrisen (t. ex. om det redan finns ett objekt i attributeMappings som har targetAttributeName-CredentialData) – du kan få konflikter eller befintliga och nya mappningar kan kombineras tillsammans (vanligt vis inte önskat resultat). Server delen kan inte dedupliceras åt dig. 

 Kom ihåg att göra detta för både användare och inetOrgperson

5. Spara schemat som du har skapat 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Lägg till schemat i begär ande texten. 

## <a name="accidental-deletes"></a>Oavsiktliga borttagningar
Det här avsnittet beskriver hur du program mässigt aktiverar/inaktiverar och använder [oavsiktliga borttagningar](how-to-accidental-deletes.md) program mässigt.


### <a name="enabling-and-setting-the-threshold"></a>Aktivera och ange tröskeln
Det finns två inställningar för varje jobb som du kan använda:

 - DeleteThresholdEnabled – möjliggör oavsiktlig borttagning av jobbet när det är inställt på "true". Ange till true som standard.
 - DeleteThresholdValue – definierar det maximala antalet borttagningar som ska tillåtas vid varje körning av jobbet när förebyggande borttagning av misstag har Aktiver ATS. Värdet är inställt på 500 som standard.  Så om värdet är inställt på 500 kommer det högsta antalet tillåtna rader att vara 499 i varje körning.

Inställningarna för tröskelvärde för borttagning är en del av `SyncNotificationSettings` och kan ändras via Graph. 

Vi kommer att behöva uppdatera SyncNotificationSettings som den här konfigurationen är riktad till, så uppdatera hemligheterna.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Lägg till följande nyckel/värde-par i nedanstående värde mat ris baserat på vad du försöker göra:

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

Inställningen "aktive rad" i exemplet ovan är att aktivera/inaktivera e-postmeddelanden när jobbet är i karantän.


Vi stöder för närvarande inte PATCH-begäranden för hemligheter, så du måste lägga till alla värden i begärans brödtext (som i exemplet ovan) för att bevara de andra värdena.

De befintliga värdena för alla hemligheter kan hämtas med hjälp av 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Tillåter borttagning
Om du vill tillåta borttagningarna att flöda genom när jobbet har satts i karantän måste du utfärda en omstart med bara "ForceDeletes" som definitions område. 

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

Dokumentation för att hämta jobb hittar du [här](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta). 
 
Starta jobbet genom att utfärda denna begäran med hjälp av objectId för tjänstens huvud namn som skapades i det första steget och jobb identifieraren som returnerades från begäran som skapade jobbet.

Dokumentation om hur du startar ett jobb hittar du [här](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Förväntat svar är... HTTP 204/inget innehåll.

Andra kommandon för att kontrol lera jobbet dokumenteras [här](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
För att starta om ett jobb använder det...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Gransknings status
Hämta jobb status via...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Titta under avsnittet status för det returnerade objektet för relevant information

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Transformeringar](how-to-transformation.md)
- [API för Azure AD-synkronisering](/graph/api/resources/synchronization-overview?view=graph-rest-beta)