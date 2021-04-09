---
title: Hoppa över borttagning av omfattnings användare
description: Lär dig hur du åsidosätter standard beteendet för inaktive ring av omfångs användare.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: a6cbabe35b223020528d1cf48aa9e0ef9b9f7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256127"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Hoppa över borttagning av användar konton som ingår i omfånget

Som standard tar Azure AD Provisioning-motorn bort eller inaktiverar användare som omfattas av omfånget. Men för vissa scenarier som arbets dag till AD-användare inkommande etablering, kan det här beteendet vara förväntat och du kanske vill åsidosätta det här standard beteendet.  

Den här artikeln beskriver hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att ange flaggan ***SkipOutOfScopeDeletions*** som styr bearbetningen av konton som omfattas av omfånget. 
* Om ***SkipOutOfScopeDeletions*** är inställt på 0 (falskt) inaktive ras de konton som ingår i omfånget i målet.
* Om ***SkipOutOfScopeDeletions** _ har värdet 1 (sant) inaktive ras inte konton som ligger utanför definitions området i målet. Den här flaggan anges på _Provisioning app *-nivå och kan konfigureras med hjälp av Graph API. 

Eftersom den här konfigurationen ofta används med *arbets dagen för att Active Directory användar etablerings* appen, innehåller följande steg skärm bilder av programmet för arbets dagar. Konfigurationen kan dock också användas med *alla andra appar*, till exempel ServiceNow, Salesforce och Dropbox.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta etablerings App Serviceens huvud-ID (objekt-ID)

1. Starta [Azure Portal](https://portal.azure.com)och navigera till avsnittet Egenskaper i ditt etablerings program. T. ex. om du vill exportera en *arbets dag till AD User Provisioning Application* -mappning navigerar du till avsnittet Egenskaper i appen. 
1. I avsnittet Egenskaper i din etablerings app kopierar du det GUID-värde som är associerat med fältet *objekt-ID* . Det här värdet kallas även **ServicePrincipalId** för din app och används i Graph-åtgärder.

   ![Workday App Service huvud-ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "logga in med Microsoft" och logga in med Azure AD global admin-eller app admin-autentiseringsuppgifter.

    ![Graph-inloggning](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Vid lyckad inloggning visas användar konto informationen i den vänstra rutan.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Steg 3: Hämta befintliga autentiseringsuppgifter för appar och anslutnings information

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] med **servicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Hämta jobb fråga](./media/skip-out-of-scope-deletions/skip-03.png)

Kopiera svaret till en textfil. Det ser ut som den JSON-text som visas nedan, med värden som är markerade med gul information för din distribution. Lägg till raderna som marker ATS i grönt till slutet och uppdatera arbets dagarnas anslutnings lösen ord markerade i blått. 

   ![Hämta jobb svar](./media/skip-out-of-scope-deletions/skip-04.png)

Här är JSON-blocket som ska läggas till i mappningen. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Steg 4: uppdatera hemligheter-slutpunkten med flaggan SkipOutOfScopeDeletions

I Graph Explorer kör du kommandot nedan för att uppdatera hemligheter-slutpunkten med flaggan ***SkipOutOfScopeDeletions*** . 

I URL: en nedan ersätter du [servicePrincipalId] med **servicePrincipalId** som extraherades från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopiera den uppdaterade texten från steg 3 till "begär ande texten" och ange huvudet "Content-Type" till "Application/JSON" i "begärandehuvuden". 

   ![SKICKA begäran](./media/skip-out-of-scope-deletions/skip-05.png)

Klicka på Kör fråga. 

Du bör få utdata som "lyckades – status kod 204". 

   ![Skicka svar](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Steg 5: kontrol lera att användare utanför räckvidden inte är inaktiverade

Du kan testa den här flaggan i förväntat beteende genom att uppdatera dina omfångs regler för att hoppa över en särskild användare. I exemplet nedan utesluter vi medarbetaren med ID 21173 (som tidigare var i området) genom att lägga till en ny omfattnings regel: 

   ![Skärm bild som visar avsnittet "Lägg till omfattnings filter" med en exempel användare markerad.](./media/skip-out-of-scope-deletions/skip-07.png)

I nästa etablerings cykel kommer Azure AD Provisioning-tjänsten att identifiera att användaren 21173 har slut på omfattning och om egenskapen SkipOutOfScopeDeletions har Aktiver ATS visas ett meddelande i synkroniseringsregeln som visas nedan: 

   ![Omfångsdefinierande exempel](./media/skip-out-of-scope-deletions/skip-08.png)


