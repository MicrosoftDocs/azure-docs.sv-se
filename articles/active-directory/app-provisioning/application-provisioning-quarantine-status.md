---
title: Status för program etablering för karantän | Microsoft Docs
description: När du har konfigurerat ett program för automatisk användar etablering, lär du dig vad en etablerings status för karantän innebär och hur du avmarkerar den.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: aa0df4e18e89ecf366da0ddf79e5241bca99bcc1
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915274"
---
# <a name="application-provisioning-in-quarantine-status"></a>Program etablering i karantän status

Azure AD Provisioning-tjänsten övervakar hälso tillståndet för din konfiguration. Den placerar också appar utan hälso tillstånd i karantän. Om de flesta, eller alla, av anropen som görs mot mål systemet inte går att utföra, markeras etablerings jobbet som i karantän. Ett exempel på ett fel är ett fel som togs emot på grund av ogiltiga autentiseringsuppgifter för admin.

I karantän:
- Frekvensen för stegvisa cykler minskas gradvis till en gång per dag.
- Etablerings jobbet tas bort från karantän när alla fel har åtgärd ATS och nästa synkronisering startar. 
- Om etablerings jobbet finns kvar i karantän i mer än fyra veckor, är etablerings jobbet inaktiverat (slutar att köras).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hur gör jag för att vet du om mitt program finns i karantän?

Det finns tre sätt att kontrol lera om ett program finns i karantän:
  
- I Azure Portal navigerar du till **Azure Active Directory**  >    >  &lt; *program namns* &gt;  >  **etablering** för företags program och granskar förlopps indikatorn för ett karantän meddelande.   

  ![Status fältet för etablering visar karantän status](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- I Azure Portal navigerar du till **Azure Active Directory**  >  **gransknings loggar** > filtrera på **aktivitet: karantän** och granska karantän historiken. Vyn i förlopps indikatorn enligt beskrivningen ovan visar om etableringen för närvarande finns i karantän. Gransknings loggarna visar karantän historiken för ett program. 

- Använd Microsoft Graph begäran [Hämta synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) för att program mässigt Hämta status för etablerings jobbet:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Gå igenom din e-post. När ett program placeras i karantän skickas ett e-postmeddelande med en Time-avisering. Om karantäns orsaken ändras skickas en uppdaterad e-postadress som visar den nya orsaken till karantänen. Om du inte ser något e-postmeddelande:

  - Kontrol lera att du har angett en giltig **e-postadress för aviseringar** i etablerings konfigurationen för programmet.
  - Se till att det inte finns någon skräp filtrering på Inkorgen för e-postavisering.
  - Se till att du inte har avslutat prenumerationen på e-postmeddelanden.
  - Sök efter e-postmeddelanden från `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Varför är mitt program i karantän?

|Beskrivning|Rekommenderad åtgärd|
|---|---|
|**Problem med scim-kompatibilitet:** Ett HTTP/404-svar som inte hittades returnerades i stället för det förväntade HTTP/200 OK-svaret. I det här fallet har Azure AD Provisioning-tjänsten gjort en begäran till mål programmet och fått ett oväntat svar.|Kontrol lera avsnittet admin credentials. Se om programmet kräver att du anger klient-URL: en och att webb adressen är korrekt. Om du inte ser något problem kontaktar du programutvecklaren för att säkerställa att deras tjänster är SCIM-kompatibla. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Ogiltiga autentiseringsuppgifter:** Vid försök att auktorisera åtkomst till mål programmet fick vi ett svar från mål programmet som anger att de angivna autentiseringsuppgifterna är ogiltiga.|Gå till avsnittet admin credentials i etablerings konfigurationens gränssnitt och auktorisera åtkomsten igen med giltiga autentiseringsuppgifter. Om programmet finns i galleriet läser du själv studie kursen om program konfiguration för längre nödvändiga steg.|
|**Dubbla roller:** Roller som importeras från vissa program som Salesforce och Zendesk måste vara unika. |Navigera till applikations [manifestet](../develop/reference-app-manifest.md) i Azure Portal och ta bort den duplicerade rollen.|

 En Microsoft Graph begäran om att hämta status för etablerings jobbet visar följande orsak för karantänen:
- `EncounteredQuarantineException` indikerar att ogiltiga autentiseringsuppgifter har angetts. Etablerings tjänsten kan inte upprätta en anslutning mellan käll systemet och mål systemet.
- `EncounteredEscrowProportionThreshold` anger att etableringen överskred tröskelvärdet för depositions. Det här tillståndet inträffar när mer än 40% av etablerings händelserna misslyckades. Mer information finns i depositions Threshold details nedan.
- `QuarantineOnDemand` innebär att vi har identifierat ett problem med ditt program och har ställt in det manuellt på karantän.

**Tröskelvärden för depositions**

Om tröskelvärdet för proportionella depositions är uppfyllt, så hamnar etablerings jobbet i karantän. Den här logiken kan ändras, men fungerar på ungefär samma sätt som beskrivs nedan: 

Ett jobb kan försättas i karantän, oavsett antal fel för problem som autentiseringsuppgifter för administratörer eller SCIM. I allmänhet är 5 000-felen minst för att börja utvärdera om det finns för många problem i karantänen. Ett jobb med 4 000-felen skulle till exempel inte hamna i karantän. Men ett jobb med 5 000-avbrott utlöser en utvärdering. En utvärdering använder följande kriterier:  
- Om mer än 40% av etablerings händelserna Miss lyckas, eller om det finns fler än 40 000-felen, så hamnar etablerings jobbet i karantän. Referens felen räknas inte som en del av tröskelvärdet för 40% tröskelvärde eller 40 000. Till exempel Miss lyckas att uppdatera en hanterare eller en grupp medlem är ett referens haveri.
- Ett jobb där 45 000 användare inte kunde allokeras skulle leda till karantän eftersom det överskrider tröskelvärdet på 40 000.
- Ett jobb där 30 000-användare misslyckades med etableringen och 5 000 lyckades leda till karantän eftersom det överskrider tröskelvärdet för 40% och 5 000 minst.
- Ett jobb med 20 000-felen och 100 000 lyckades skulle inte gå in i karantän eftersom det berg inte överskrider tröskelvärdet på 40% eller 40 000 misslyckades.  
- Det finns ett absolut tröskelvärde på 60 000-felen som konton för både referens-och icke-referens haverier. Till exempel gick det inte att tillhandahålla 40 000 användare och 21 000 Manager-uppdateringar misslyckades. Summan är 61 000-felaktiga och överskrider gränsen på 60 000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hur gör jag för att hämta mitt program från karantänen?

Först löser du problemet som gjorde att programmet placerades i karantän.

- Kontrol lera programmets etablerings inställningar för att kontrol lera att du har [angett giltiga autentiseringsuppgifter för admin](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD måste upprätta ett förtroende med mål programmet. Kontrol lera att du har angett giltiga autentiseringsuppgifter och att ditt konto har de behörigheter som krävs.

- Granska [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att ytterligare undersöka vilka fel som orsakar karantän och åtgärda felet. Gå till **Azure Active Directory** &gt; **Enterprise Apps** &gt; **-etablerings loggar (för hands version)** i avsnittet **aktivitet** .

När du har löst problemet startar du om etablerings jobbet. Vissa ändringar av programmets etablerings inställningar, till exempel attribut mappningar eller omfångs filter, startar automatiskt om etablering. Förlopps indikatorn på programmets **etablerings** sida anger när etableringen senast startades. Om du behöver starta om etablerings jobbet manuellt kan du använda någon av följande metoder:  

- Använd Azure Portal för att starta om etablerings jobbet. På programmets **etablerings** sida under **Inställningar** väljer du **Rensa tillstånd och startar om synkronisering** och ställer in **etablerings status** på **på**. Den här åtgärden startar om etablerings tjänsten helt, vilket kan ta lite tid. En fullständig första cykel körs igen, vilket rensar escrows, tar bort appen från karantänen och tar bort alla vattenstämplar.

- Använd Microsoft Graph för att [starta om etablerings jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Du har fullständig kontroll över vad du vill starta om. Du kan välja att rensa escrows (om du vill starta om depositions-räknaren som påförs i karantäns status), rensa karantän (för att ta bort programmet från karantän) eller rensa vattenstämplar. Använd följande begäran:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Ersätt "{ID}" med värdet för program-ID: t och Ersätt "{jobId}" med [ID: t för synkroniseringsschemat](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
