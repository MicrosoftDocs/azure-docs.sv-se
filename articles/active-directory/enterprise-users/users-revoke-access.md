---
title: Återkalla användar åtkomst i nödfall i Azure Active Directory | Microsoft Docs
description: Så här återkallar du all åtkomst till en användare i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578e8f5f3126542c579cd573c82b732049d407b6
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959832"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Återkalla användar åtkomst i Azure Active Directory

Scenarier som kan kräva att en administratör återkallar all åtkomst till en användare inkluderar komprometterade konton, avslutning av anställda och andra Insider hot. Beroende på miljöns komplexitet kan administratörer vidta flera steg för att säkerställa att åtkomsten återkallas. I vissa fall kan det finnas en period mellan initieringen av åtkomst åter kallelsen och när åtkomsten faktiskt återkallas.

För att minimera riskerna måste du förstå hur tokens fungerar. Det finns många typer av tokens, som hör till något av mönstren som anges i avsnitten nedan.

## <a name="access-tokens-and-refresh-tokens"></a>Åtkomsttoken och uppdateringstoken

Åtkomst-token och uppdateringstoken används ofta med tjocka klient program och används även i webbläsarbaserade program, till exempel appar på en sida.

- När användare autentiserar till Azure AD utvärderas Auktoriseringsprinciper för att avgöra om användaren kan beviljas åtkomst till en särskild resurs.  

- Om det är auktoriserat, utfärdar Azure AD en åtkomsttoken och en uppdateringstoken för resursen.  

- Åtkomsttoken som utfärdats av Azure AD senast i 1 timme. Om autentiseringsprotokollet tillåter kan appen tyst autentisera användaren genom att skicka uppdateringstoken till Azure AD när åtkomsttoken upphör att gälla.

Azure AD utvärderar sedan sina Auktoriseringsprinciper. Om användaren fortfarande har behörighet utfärdar Azure AD en ny åtkomsttoken och uppdaterar token.

Åtkomst-token kan vara ett säkerhets problem om åtkomst måste återkallas inom en tid som är kortare än token för token, som vanligt vis ligger runt en timme. Av den anledningen arbetar Microsoft aktivt med att göra en [utvärdering av kontinuerlig åtkomst](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) till Office 365-program, vilket hjälper till att säkerställa ogiltighet av åtkomsttoken i nära real tid.  

## <a name="session-tokens-cookies"></a>Token för sessioner (cookies)

De flesta webbläsarbaserade program använder tokens i stället för åtkomst-och uppdateringstoken.  

- När en användare öppnar en webbläsare och autentiserar till ett program via Azure AD får användaren två tokens. En från Azure AD och en annan från programmet.  

- När ett program utfärdar en egen sessionstoken styrs åtkomsten till programmet av programmets session. Användaren påverkas i det här läget endast av de Auktoriseringsprinciper som programmet känner till.

- Auktoriseringsprinciper för Azure AD utvärderas så ofta som programmet skickar tillbaka användaren till Azure AD. Omprövning sker vanligt vis i bakgrunden, även om frekvensen beror på hur programmet har kon figurer ATS. Det kan hända att appen aldrig skickar tillbaka användaren till Azure AD så länge sessionstoken är giltig.

- För att en sessionstoken ska återkallas måste programmet återkalla åtkomst baserat på dess egna Auktoriseringsprinciper. Azure AD kan inte omedelbart återkalla en sessionstoken som utfärdats av ett program.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Återkalla åtkomsten för en användare i hybrid miljön

För en hybrid miljö med en lokal Active Directory som är synkroniserad med Azure Active Directory rekommenderar Microsoft IT-administratörer att vidta följande åtgärder.  

### <a name="on-premises-active-directory-environment"></a>Lokal Active Directorys miljö

Som administratör i Active Directory ansluter du till ditt lokala nätverk, öppnar PowerShell och vidtar följande åtgärder:

1. Inaktivera användaren i Active Directory. Se [disable-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Återställ användarens lösen ord två gånger i Active Directory. Referera till [set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > Anledningen till att ändra en användares lösen ord två gånger är att minska risken för pass-The-hash, särskilt om det finns fördröjningar i lokal lösenordsreplikering. Om du på ett säkert sätt kan anta att det här kontot inte komprometteras kan du bara återställa lösen ordet en gång.

    > [!IMPORTANT]
    > Använd inte exempel lösen orden i följande cmdletar. Se till att ändra lösen orden till en slumpmässig sträng.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory miljö

Som administratör i Azure Active Directory öppnar du PowerShell, kör ``Connect-AzureAD`` och vidtar följande åtgärder:

1. Inaktivera användaren i Azure AD. Referera till [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. Återkalla användarens Azure AD-uppdateringstoken. Se [återkalla-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Inaktivera användarens enheter. Se [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>När åtkomsten återkallas

När administratörer har vidtagit stegen ovan kan användaren inte få nya token för program som är kopplade till Azure Active Directory. Tiden som förflutit mellan återkallning och användaren förlorar åtkomsten beror på hur programmet beviljar åtkomst:

- För **program som använder** åtkomsttoken förlorar användaren åtkomst när åtkomsttoken upphör att gälla.

- För **program som använder sessionstoken** avslutas de befintliga sessionerna så snart token upphör att gälla. Om användarens inaktiverade tillstånd är synkroniserat med programmet kan programmet automatiskt återkalla användarens befintliga sessioner om den är konfigurerad att göra det.  Hur lång tid det tar beror på hur ofta synkroniseringen mellan programmet och Azure AD är.

## <a name="best-practices"></a>Bästa praxis

- Distribuera en automatiserad etablerings-och avetablerings lösning. Att avetablera användare från program är ett effektivt sätt att återkalla åtkomst, särskilt för program som använder token för sessioner. Utveckla en process för att avetablera användare till appar som inte stöder automatisk etablering och avetablering. Se till att programmen återkallar sina egna sessionstoken och sluta godkänna Azure AD-åtkomsttoken även om de fortfarande är giltiga.

  - Använd [Azure AD SaaS app-etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning). Azure AD SaaS app-etablering körs vanligt vis automatiskt var 20-40: e minut. [Konfigurera Azure AD-etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) för att avetablera eller inaktivera inaktiverade användare i program.
  
  - För program som inte använder Azure AD SaaS app-etablering använder du [Identity Manager (MIM)](https://docs.microsoft.com/microsoft-identity-manager/mim-how-provision-users-adds) eller en lösning från tredje part för att automatisera avetablering av användare.  
  - Identifiera och utveckla en process för program som kräver manuell avetablering. Se till att administratörer snabbt kan köra nödvändiga manuella uppgifter för att avetablera användaren från dessa appar vid behov.
  
- [Hantera dina enheter och program med Microsoft Intune](https://docs.microsoft.com/mem/intune/remote-actions/device-management). Intune-hanterade [enheter kan återställas till fabriks inställningarna](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe). Om enheten är ohanterad kan du [Rensa företags data från hanterade appar](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe). Dessa processer är effektiva för att ta bort potentiellt känsliga data från slutanvändarens enheter. För att antingen processen ska kunna utlösas måste enheten dock vara ansluten till Internet. Om enheten är offline kommer enheten fortfarande att ha åtkomst till lokalt lagrade data.

> [!NOTE]
> Det går inte att återställa data på enheten efter en rensning.

- Använd [Microsoft Cloud App Security (MCAS) för att blockera hämtning av data](https://docs.microsoft.com/cloud-app-security/use-case-proxy-block-session-aad) när det är lämpligt. Om data endast kan nås online, kan organisationer övervaka sessioner och uppnå princip tillämpning i real tid.

- Aktivera [utvärdering av kontinuerlig åtkomst (CAE) i Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation). CAE gör det möjligt för administratörer att återkalla sessionstoken och åtkomsttoken för program som är CAE-kompatibla.  

## <a name="next-steps"></a>Nästa steg

- [Säkra åtkomst metoder för Azure AD-administratörer](https://docs.microsoft.com/azure/active-directory/roles/security-planning)
- [Lägga till eller uppdatera information om användar profiler](../fundamentals/active-directory-users-profile-azure-portal.md)
