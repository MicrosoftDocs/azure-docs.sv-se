---
title: Inkommande synkronisering för moln etablering med MS Graph API
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
ms.openlocfilehash: 5c6ed106462d7dcac3f31a2ab157e5b634391006
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093459"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Inkommande synkronisering för moln etablering med MS Graph API

I följande dokument beskrivs hur du replikerar en Synkroniseringsregel från grunden med endast MSGraph-API: er.  
Strukturen för hur du gör detta består av följande steg.  De är:

- [Grundläggande konfiguration](#basic-setup)
- [Skapa tjänstens huvud namn](#create-service-principals)
- [Skapa synkroniseringsjobb](#create-sync-job)
- [Uppdatera riktad domän](#update-targeted-domain)
- [Starta synkroniseringsjobb](#start-sync-job)
- [Gransknings status](#review-status)

Använd dessa [Microsoft Azure Active Directory-modul för Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) -kommandon för att aktivera synkronisering för en produktions klient, ett krav för att kunna anropa administrations webb tjänsten för den klienten.

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

Dokumentation om hur du skapar ett synkroniseringsjobb finns [här](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

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
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

Förväntat svar är... HTTP 204/inget innehåll

Här är det markerade "domän"-värdet namnet på den lokala Active Directory domän från vilken poster ska tillhandahållas till Azure Active Directory.

## <a name="start-sync-job"></a>Starta synkroniseringsjobb
Jobbet kan hämtas igen via följande kommando:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentation för att hämta jobb hittar du [här](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Starta jobbet genom att utfärda denna begäran med hjälp av objectId för tjänstens huvud namn som skapades i det första steget och jobb identifieraren som returnerades från begäran som skapade jobbet.

Dokumentation om hur du startar ett jobb hittar du [här](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Förväntat svar är... HTTP 204/inget innehåll.

Andra kommandon för att kontrol lera jobbet dokumenteras [här](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
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

- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
- [Transformeringar](how-to-transformation.md)
- [API för Azure AD-synkronisering](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
