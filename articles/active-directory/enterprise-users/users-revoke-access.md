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
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826ca9fc20d8bbcf9a5f90ccc895b9f9867a6be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860583"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Återkalla användar åtkomst i Azure Active Directory

Bland scenarier som kan kräva att en administratör återkallar all åtkomst till en användare inkluderar komprometterade konton, avslutning av anställda och andra Insider hot. Beroende på miljöns komplexitet kan administratörer vidta flera steg för att säkerställa att åtkomsten återkallas. I vissa fall kan det finnas en period mellan initieringen av åtkomst återkallning och när åtkomsten faktiskt återkallas.

För att minimera riskerna måste du förstå hur tokens fungerar. Det finns många typer av tokens, som hör till något av mönstren som anges i avsnitten nedan.

## <a name="access-tokens-and-refresh-tokens"></a>Åtkomsttoken och uppdateringstoken

Åtkomst-token och uppdateringstoken används ofta med tjocka klient program och används även i webbläsarbaserade program, till exempel appar på en sida.

- När användare autentiserar till Azure AD utvärderas Auktoriseringsprinciper för att avgöra om användaren kan beviljas åtkomst till en särskild resurs.  

- Om det är auktoriserat, utfärdar Azure AD en åtkomsttoken och en uppdateringstoken för resursen.  

- Åtkomsttoken som utfärdats av Azure AD senast i 1 timme. Om autentiseringsprotokollet tillåter kan appen tyst autentisera användaren genom att skicka uppdateringstoken till Azure AD när åtkomsttoken upphör att gälla.

Azure AD utvärderar sedan sina Auktoriseringsprinciper. Om användaren fortfarande har behörighet utfärdar Azure AD en ny åtkomsttoken och en uppdaterad token.

Åtkomst-token kan vara ett säkerhets problem om åtkomst måste återkallas inom en tid som är kortare än token för token, som vanligt vis ligger runt en timme. Av den anledningen arbetar Microsoft aktivt för att göra en [utvärdering av kontinuerlig åtkomst](../conditional-access/concept-continuous-access-evaluation.md) för att Microsoft 365 program, vilket hjälper till att säkerställa invaliditet av åtkomsttoken i nära real tid.  

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

1. Inaktivera användaren i Active Directory. Se [disable-ADAccount](/powershell/module/addsadministration/disable-adaccount).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Återställ användarens lösen ord två gånger i Active Directory. Referera till [set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword).

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

1. Inaktivera användaren i Azure AD. Referera till [set-AzureADUser](/powershell/module/azuread/Set-AzureADUser).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Återkalla användarens Azure AD-uppdateringstoken. Se [återkalla-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Inaktivera användarens enheter. Se [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Valfria steg

- [Rensa företags data från Intune-hanterade program](/mem/intune/apps/apps-selective-wipe).

- [Rensa företagsägda enheter återställer enheten till fabriks inställningarna](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Det går inte att återställa data på enheten efter en rensning.

## <a name="when-access-is-revoked"></a>När åtkomsten återkallas

När administratörer har vidtagit stegen ovan kan användaren inte få nya token för program som är kopplade till Azure Active Directory. Tiden som förflutit mellan återkallning och användaren förlorar åtkomsten beror på hur programmet beviljar åtkomst:

- För **program som använder** åtkomsttoken förlorar användaren åtkomst när åtkomsttoken upphör att gälla.

- För **program som använder sessionstoken** avslutas de befintliga sessionerna så snart token upphör att gälla. Om användarens inaktiverade tillstånd är synkroniserat med programmet kan programmet automatiskt återkalla användarens befintliga sessioner om den är konfigurerad att göra det.  Hur lång tid det tar beror på hur ofta synkroniseringen mellan programmet och Azure AD är.

## <a name="next-steps"></a>Nästa steg

- [Säkra åtkomst metoder för Azure AD-administratörer](../roles/security-planning.md)
- [Lägga till eller uppdatera information om användar profiler](../fundamentals/active-directory-users-profile-azure-portal.md)
