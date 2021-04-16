---
title: Hantera inaktiva användarkonton i Azure AD | Microsoft Docs
description: Lär dig mer om att identifiera och hantera användarkonton i Azure AD som har blivit föråldrade
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535108"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Gör så här: Hantera inaktiva användarkonton i Azure AD

I stora miljöer tas användarkonton inte alltid bort när anställda lämnar en organisation. Som IT-administratör vill du identifiera och hantera dessa föråldrade användarkonton eftersom de utgör en säkerhetsrisk.

Den här artikeln beskriver en metod för att hantera föråldrade användarkonton i Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Vad är inaktiva användarkonton?

Inaktiva konton är användarkonton som inte längre krävs av medlemmar i din organisation för att få åtkomst till dina resurser. En viktig identifierare för inaktiva konton är att de inte har använts *på ett tag* för att logga in i din miljö. Eftersom inaktiva konton är knutna till inloggningsaktiviteten kan du använda tidsstämpeln för den senaste inloggningen som lyckades för att identifiera dem. 

Utmaningen med den här metoden är att definiera *vad en stund* innebär när det gäller din miljö. Användare kanske till exempel inte loggar in i en miljö *på ett tag* eftersom de är på semester. När du definierar vad delta för inaktiva användarkonton är måste du ta med alla legitima skäl till att inte logga in i din miljö. I många organisationer är delta för inaktiva användarkonton mellan 90 och 180 dagar. 

Den senaste lyckade inloggningen ger potentiella insikter om en användares fortsatt behov av åtkomst till resurser.  Det kan hjälpa dig att avgöra om gruppmedlemskap eller appåtkomst fortfarande behövs eller kan tas bort. För extern användarhantering kan du förstå om en extern användare fortfarande är aktiv i klientorganisationen eller bör rensas. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Så här identifierar du inaktiva användarkonton

Du identifierar inaktiva konton genom att utvärdera **egenskapen lastSignInDateTime** som exponeras av **resurstypen signInActivity** för **Microsoft Graph-API:et.** Med den här egenskapen kan du implementera en lösning för följande scenarier:

- **Användare efter namn:** I det här scenariot söker du efter en specifik användare efter namn, vilket gör att du kan utvärdera lastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Användare efter datum:** I det här scenariot begär du en lista över användare med lastSignInDateTime före ett angivet datum: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Du kan behöva generera en rapport med det senaste inloggningsdatumet för alla användare, i så fall kan du använda följande scenario.
> **Datum och tid för senaste** inloggning för alla användare: I det här scenariot begär du en lista över alla användare och den senaste lastSignInDateTime för varje respektive användare: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Vad du behöver veta

I det här avsnittet visas vad du behöver veta om egenskapen lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Hur kommer jag åt den här egenskapen?

Egenskapen **lastSignInDateTime** exponeras av [resurstypen signInActivity för](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) [Microsoft Graph REST API](/graph/overview#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Är egenskapen lastSignInDateTime tillgänglig via Get-AzureAdUser cmdlet?

Nej.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Vilken version av Azure AD behöver jag för att få åtkomst till egenskapen?

Du kan komma åt den här egenskapen i alla utgåvor av Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Vilken behörighet behöver jag för att läsa egenskapen?

Om du vill läsa den här egenskapen måste du bevilja följande rättigheter: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>När uppdaterar Azure AD egenskapen?

Varje interaktiv inloggning som lyckades resulterar i en uppdatering av det underliggande datalagret. Lyckade inloggningar visas vanligtvis i den relaterade inloggningsrapporten inom 10 minuter.
 

### <a name="what-does-a-blank-property-value-mean"></a>Vad betyder ett tomt egenskapsvärde?

Om du vill generera en lastSignInDateTime-tidsstämpel behöver du en lyckad inloggning. Eftersom egenskapen lastSignInDateTime är en ny funktion kan värdet för egenskapen lastSignInDateTime vara tomt om:

- Den senaste lyckade inloggningen för en användare ägde rum före april 2020.
- Det berörda användarkontot användes aldrig för en lyckad inloggning.

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](/graph/api/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](/graph/api/resources/signin)
