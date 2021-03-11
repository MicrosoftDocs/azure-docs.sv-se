---
title: Återställa en gäst användares inlösnings status – Azure AD
description: Lär dig hur du återställer inlösnings statusen för en inbjudan för en Azure Active Directory B2B-gäst användare i externa Azure AD-identiteter.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea13444a6bd18bd67f05d93a38af70b3b7a2368
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556323"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Återställ inlösen status för en gäst användare

När en gäst användare har löst din inbjudan om B2B-samarbete kan det finnas tillfällen då du behöver uppdatera sin inloggnings information, till exempel när:

- Användaren vill logga in med en annan e-postadress och identitets leverantör
- Kontot för användaren i sin hem klient har tagits bort och återskapas
- Användaren har flyttat till ett annat företag, men de behöver fortfarande samma åtkomst till dina resurser
- Användarens ansvars områden har skickats till en annan användare

Om du vill hantera de här scenarierna tidigare var du tvungen att manuellt ta bort gäst användarens konto från katalogen och ombjuda användaren. Nu kan du använda PowerShell eller Microsoft Graph Inbjudnings-API för att återställa användarens inlösnings status och ombjuda användaren samtidigt som användarens objekt-ID, grupp medlemskap och app-tilldelningar behålls. När användaren löser in den nya inbjudan ändras inte UPN-namnet för användaren, men användarens inloggnings namn ändras till det nya e-postmeddelandet. Användaren kan sedan logga in med det nya e-postmeddelandet eller ett e-postmeddelande som du har lagt till i `otherMails` egenskapen för objektet användare.

## <a name="use-powershell-to-reset-redemption-status"></a>Använd PowerShell för att återställa inlösen status

Installera den senaste AzureADPreview PowerShell-modulen och skapa en ny inbjudan med `InvitedUserEMailAddress` inställt på den nya e-postadressen och `ResetRedemption` Ange till `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Använd Microsoft Graph-API för att återställa inlösen status

Använd [Microsoft Graph Inbjudnings-API: et](/graph/api/resources/invitation)och ange `resetRedemption` egenskapen till `true` och ange den nya e-postadressen i `invitedUserEmailAddress` egenskapen.

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

- [Lägga till Azure Active Directory B2B-samarbets användare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Egenskaper för en Azure AD B2B-gäst användare](user-properties.md)
