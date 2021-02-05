---
title: Bjud in interna användare till B2B-samarbete – Azure AD
description: Om du har interna användar konton för partner, distributörer, leverantörer, leverantörer och andra gäster kan du ändra till Azure AD B2B-samarbete genom att bjuda in dem att logga in med sina egna externa autentiseringsuppgifter eller inloggningar. Använd antingen PowerShell eller Microsoft Graph Inbjudnings-API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576438"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Bjud in interna användare till B2B-samarbete

Innan Azure AD B2B-samarbetet är tillgängligt kan organisationer samar beta med distributörer, leverantörer, leverantörer och andra gäst användare genom att ställa in interna autentiseringsuppgifter för dem. Om du har interna gäst användare som dessa kan du bjuda in dem att använda B2B-samarbete i stället. Dessa B2B-gäst användare kommer att kunna använda sina egna identiteter och autentiseringsuppgifter för att logga in, och du behöver inte underhålla lösen ord eller hantera livs cykel för konton.

Genom att skicka en inbjudan till ett befintligt internt konto kan du behålla användarens objekt-ID, UPN, grupp medlemskap och app-tilldelningar. Du behöver inte manuellt ta bort och bjuda in användaren på nytt eller omtilldela resurser. Om du vill bjuda in användaren använder du Inbjudnings-API: et för att skicka både det interna användarobjektet och gäst användarens e-postadress tillsammans med inbjudan. När användaren accepterar inbjudan, ändrar B2B-tjänsten det befintliga interna användar objektet till en B2B-användare. Användaren måste logga in på moln resurser tjänster med sina B2B-autentiseringsuppgifter.

## <a name="things-to-consider"></a>Saker att tänka på

- **Åtkomst till lokala resurser**: när användaren har bjudits in till B2B-samarbete kan de fortfarande använda sina interna autentiseringsuppgifter för att komma åt lokala resurser. Du kan förhindra detta genom att återställa eller ändra lösen ordet för det interna kontot. Undantaget är [e-postautentisering med eng ång slö sen ord](one-time-passcode.md). om användarens autentiseringsmetod ändras till eng ång slö sen ord, kommer de inte längre att kunna använda sina interna autentiseringsuppgifter.

- **Fakturering**: den här funktionen ändrar inte UserType för användaren, så den växlar inte automatiskt användarens fakturerings modell till [externa identiteter månads aktiv användare (MAU)](external-identities-pricing.md). Om du vill aktivera MAU-prissättningen för användaren ändrar du UserType för användaren till `guest` . Observera också att din Azure AD-klient måste vara [länkad till en Azure-prenumeration](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) för att aktivera Mau fakturering.

- **Inbjudan är enkelriktad**: du kan bjuda in interna användare att använda B2B-samarbete, men du kan inte ta bort B2B-autentiseringsuppgifterna när de väl har lagts till. Om du vill ändra tillbaka användaren till en intern användare måste du ta bort objektet och skapa ett nytt.

- **Team**: när användaren har åtkomst till team som använder sina externa autentiseringsuppgifter, kommer deras klient inte att vara tillgänglig från början i teamets klient väljare. Användaren har åtkomst till teamen med hjälp av en URL som innehåller klient kontexten, till exempel: `https://team.microsoft.com/?tenantId=<TenantId>` . Därefter blir klient organisationen tillgänglig i team väljaren.

- **Lokala synkroniserade användare**: för användar konton som synkroniseras mellan lokalt och molnet är den lokala katalogen fortfarande källan till utfärdaren när de har bjudits in för att använda B2B-samarbete. Alla ändringar du gör i det lokala kontot synkroniseras med moln kontot, inklusive inaktive ring eller borttagning av kontot. Därför kan du inte hindra användaren från att logga in på sitt lokala konto samtidigt som deras moln konto behålls genom att helt enkelt ta bort det lokala kontot. I stället kan du ange lösen ordet för det lokala kontot till ett slumpmässigt GUID eller andra okända värden.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Så här bjuder du in interna användare till B2B-samarbete

Du kan använda PowerShell eller Inbjudnings-API: et för att skicka en B2B-inbjudan till den interna användaren. Kontrol lera att e-postadressen som du vill använda för inbjudan har angetts som extern e-postadress för det interna användarobjektet.

- Du måste använda e-postadressen i egenskapen User. mail för inbjudan.
- Domänen i användarens e-postegenskap måste matcha det konto som de använder för att logga in. Annars kan vissa tjänster, t. ex. team, inte autentisera användaren.

Som standard skickar inbjudan användaren ett e-postmeddelande om att de vet att de har bjudits in, men du kan ignorera det här e-postmeddelandet och skicka egna i stället.

> [!NOTE]
> Om du vill skicka ett eget e-postmeddelande eller annan kommunikation kan du använda `New-AzureADMSInvitation` med `-SendInvitationMessage:$false` för att bjuda in användare i bakgrunden och sedan skicka ditt eget e-postmeddelande till den konverterade användaren. Se [Azure AD B2B COLLABORATION API och anpassning](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Använd PowerShell för att skicka en B2B-inbjudan

Du behöver Azure AD PowerShell-modulens version 2.0.2.130 eller senare. Använd följande kommando för att uppdatera till den senaste AzureAD PowerShell-modulen och Bjud in den interna användaren till B2B-samarbete:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Använd Inbjudnings-API: et för att skicka en B2B-inbjudan

Exemplet nedan visar hur du anropar API: et för inbjudan för att bjuda in en intern användare som en B2B-användare.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Svaret på API: et är samma svar som du får när du bjuder in en ny gäst användare till katalogen.
## <a name="next-steps"></a>Nästa steg

- [Inlösen av B2B-samarbets inbjudningar](redemption-experience.md)
