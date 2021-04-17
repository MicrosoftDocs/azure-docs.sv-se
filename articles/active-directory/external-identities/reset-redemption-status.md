---
title: Återställa en gästanvändares inlösningsstatus – Azure AD
description: Lär dig hur du återställer inlösningsstatusen för en Azure Active Directory B2B-gästanvändare i Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531871"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Återställa inlösningsstatus för en gästanvändare (förhandsversion)

När en gästanvändare har löst in din inbjudan för B2B-samarbete kan det finnas tillfällen när du behöver uppdatera deras inloggningsinformation, till exempel när:

- Användaren vill logga in med en annan e-post- och identitetsprovider
- Kontot för användaren i deras hemklientorganisation har tagits bort och skapats på nytt
- Användaren har flyttat till ett annat företag, men de behöver fortfarande samma åtkomst till dina resurser
- Användarens ansvarsområden har vidarebefordrats till en annan användare

För att hantera dessa scenarier tidigare var du tvungen att manuellt ta bort gästanvändarens konto från din katalog och ge användaren en ny inbjudan. Nu kan du använda PowerShell eller API:et för Microsoft Graph-inbjudan för att återställa användarens inlösningsstatus och ombjuda användaren samtidigt som användarens objekt-ID, gruppmedlemskap och apptilldelningar behålls. När användaren löser in den nya inbjudan ändras inte ANVÄNDARENs UPN, men användarens inloggningsnamn ändras till det nya e-postmeddelandet. Användaren kan därefter logga in med det nya e-postmeddelandet eller ett e-postmeddelande som du har lagt till `otherMails` i egenskapen för användarobjektet.

## <a name="reset-the-email-address-used-for-sign-in"></a>Återställa e-postadressen som används för inloggning

Om en användare vill logga in med ett annat e-postmeddelande:

1. Kontrollera att den nya e-postadressen har lagts till `mail` i egenskapen eller för `otherMails` användarobjektet. 
2.  Ersätt e-postadressen i `InvitedUserEmailAddress` egenskapen med den nya e-postadressen.
3. Använd någon av metoderna nedan för att återställa användarens inlösningsstatus.

> [!NOTE]
>Under den offentliga förhandsversionen har vi två rekommendationer:
>- När du återställer användarens e-postadress till en ny adress rekommenderar vi att du anger `mail` egenskapen. På så sätt kan användaren lösa in inbjudan genom att logga in i din katalog utöver att använda inlösningslänken i inbjudan.
>- När du återställer statusen för en B2B-gästanvändare måste du göra det i användarkontexten. Appbaserade anrop stöds inte för närvarande.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Använda PowerShell för att återställa inlösningsstatus

Installera den senaste AzureADPreview PowerShell-modulen och skapa en ny inbjudan med den `InvitedUserEmailAddress` nya e-postadressen och `ResetRedemption` ange till `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Använda Microsoft Graph-API för att återställa inlösningsstatus

Med hjälp [Microsoft Graph-API:et](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)för inbjudan `resetRedemption` anger du egenskapen till och anger den nya `true` e-postadressen i `invitedUserEmailAddress` egenskapen .

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Nästa steg

- [Lägga Azure Active Directory B2B-samarbetsanvändare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Egenskaper för en Azure AD B2B-gästanvändare](user-properties.md)
