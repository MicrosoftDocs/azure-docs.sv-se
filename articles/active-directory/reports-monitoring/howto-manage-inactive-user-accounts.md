---
title: Hantera inaktiva användar konton i Azure AD | Microsoft Docs
description: Lär dig mer om att identifiera och hantera användar konton i Azure AD som har blivit föråldrade
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
ms.openlocfilehash: 8fb517f8c50ad2c32f23542e60069a0e0a496a2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660672"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Gör så här: hantera inaktiva användar konton i Azure AD

I stora miljöer raderas inte användar konton alltid när anställda lämnar en organisation. Som IT-administratör vill du identifiera och hantera dessa föråldrade användar konton eftersom de utgör en säkerhets risk.

I den här artikeln beskrivs en metod för att hantera inaktuella användar konton i Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Vad är inaktiva användar konton?

Inaktiva konton är användar konton som inte behövs längre av medlemmar i organisationen för att få åtkomst till dina resurser. En nyckel identifierare för inaktiva konton är att de inte har använts *för* att logga in i din miljö. Eftersom inaktiva konton är knutna till inloggnings aktiviteten kan du använda tidsstämpeln för den senaste inloggningen som lyckades för att identifiera dem. 

Utmaningen med den här metoden är att definiera vad *som är ett tag* i din miljö. Till exempel kanske användarna inte loggar in i en miljö *en stund*, eftersom de är på semester. När du definierar vad din delta för inaktiva användar konton är, måste du väga alla legitima orsaker till att inte logga in på din miljö. I många organisationer är delta för inaktiva användar konton mellan 90 och 180 dagar. 

Den senaste lyckade inloggningen ger potentiella insikter om en användares fortsatta behov av åtkomst till resurser.  Det kan hjälpa till att avgöra om grupp medlemskap eller app-åtkomst fortfarande behövs eller kan tas bort. För hantering av externa användare kan du förstå om en extern användare fortfarande är aktiv i klienten eller ska rensas. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Identifiera inaktiva användar konton

Du identifierar inaktiva konton genom att utvärdera egenskapen **lastSignInDateTime** som exponeras av resurs typen **signInActivity** för **Microsoft Graph** -API: et. Med den här egenskapen kan du implementera en lösning för följande scenarier:

- **Användare efter namn**: i det här scenariot söker du efter en speciell användare efter namn, vilket gör att du kan utvärdera lastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Användare efter datum**: i det här scenariot begär du en lista med användare med en lastSignInDateTime före ett angivet datum: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Vad du behöver veta

I det här avsnittet visas vad du behöver veta om egenskapen lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Hur får jag åtkomst till den här egenskapen?

Egenskapen **lastSignInDateTime** visas av [resurs typen signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta) för [Microsoft Graph REST API](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Är egenskapen lastSignInDateTime tillgänglig via Get-AzureAdUser cmdleten?

Nej.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Vilken version av Azure AD behöver jag för att få åtkomst till egenskapen?

Du kan komma åt den här egenskapen i alla utgåvor av Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Vilken behörighet behöver jag läsa egenskapen?

Om du vill läsa den här egenskapen måste du bevilja följande rättigheter: 

- AuditLogs. Read. all
- Organisationen. Read. all  


### <a name="when-does-azure-ad-update-the-property"></a>När uppdaterar Azure AD egenskapen?

Varje interaktiv inloggning som lyckades resulterar i en uppdatering av det underliggande data lagret. Normalt visas lyckade inloggningar i den relaterade inloggnings rapporten inom 10 minuter.
 

### <a name="what-does-a-blank-property-value-mean"></a>Vad betyder ett tomt egenskaps värde?

Om du vill generera en lastSignInDateTime-tidsstämpel behöver du en lyckad inloggning. Eftersom egenskapen lastSignInDateTime är en ny funktion kan värdet för egenskapen lastSignInDateTime vara tomt om:

- Den senaste lyckade inloggningen av en användare ägde rum före 2020 april.
- Det berörda användar kontot användes aldrig för en lyckad inloggning.

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Rapport-API-referens för inloggnings aktivitet](/graph/api/resources/signin?view=graph-rest-beta)