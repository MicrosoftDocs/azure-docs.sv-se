---
title: Principer för lösen ords återställning via självbetjäning – Azure Active Directory
description: Läs mer om de Azure Active Directory olika alternativen för lösen ords återställning med självbetjänings principer för lösen ords återställning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918051"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Lösen ords principer och konto begränsningar i Azure Active Directory

I Azure Active Directory (Azure AD) finns det en lösen ords princip som definierar inställningar som lösen ords komplexitet, längd eller ålder. Det finns också en princip som definierar acceptabla tecken och längd för användar namn.

När lösen ords återställning via självbetjäning (SSPR) används för att ändra eller återställa ett lösen ord i Azure AD, kontrol leras lösen ords principen. Om lösen ordet inte uppfyller princip kraven uppmanas användaren att försöka igen. Azure-administratörer har vissa begränsningar för att använda SSPR som skiljer sig från vanliga användar konton.

I den här artikeln beskrivs inställningarna för lösen ords principer och komplexitets krav som är associerade med användar konton i din Azure AD-klient och hur du kan använda PowerShell för att kontrol lera eller ange inställningar för lösen ords förfallo datum.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Principer för användar namn

Varje konto som loggar in på Azure AD måste ha ett unikt värde för User Principal Name (UPN) som är kopplat till sitt konto. I hybrid miljöer med en lokal Active Directory Domain Services (AD DS)-miljö som är synkroniserad med Azure AD med hjälp av Azure AD Connect är Azure AD UPN som standard inställt på lokal UPN.

Följande tabell beskriver de användar namns principer som gäller för både lokala AD DS-konton som synkroniseras med Azure AD och för molnbaserade användar konton som skapas direkt i Azure AD:

| Egenskap | UserPrincipalName-krav |
| --- | --- |
| Tillåtna tecken |<ul> <li>A – Ö</li> <li>a-ö</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tecken tillåts inte |<ul> <li>Alla "- \@ \" tecknen som inte skiljer användar namnet från domänen.</li> <li>Får inte innehålla ett punkt tecken "." omedelbart före \@ \" symbolen "</li></ul> |
| Längd begränsningar |<ul> <li>Den totala längden får inte överskrida 113 tecken</li><li>Det kan finnas upp till 64 tecken före \@ \" symbolen</li><li>Det kan finnas upp till 48 tecken efter \@ \" symbolen</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Lösen ords principer för Azure AD

En lösen ords princip tillämpas på alla användar konton som skapas och hanteras direkt i Azure AD. Vissa av dessa inställningar för lösen ords principer kan inte ändras, men du kan [Konfigurera anpassade förbjudna lösen ord för lösen ords skydd i Azure AD](tutorial-configure-custom-password-protection.md) eller parametrar för konto utelåsning.

Som standard är ett konto utelåst efter 10 misslyckade inloggnings försök med fel lösen ord. Användaren är låst i en minut. Ytterligare felaktiga inloggnings försök låser användaren för att öka tiden. [Smart utelåsning](howto-password-smart-lockout.md) spårar de tre senaste Felaktiga hasharna för lösen ord för att undvika ökning av utelåsnings räknaren för samma lösen ord. Om någon anger samma Felaktiga lösen ord flera gånger, kommer det här beteendet inte att orsaka att kontot låser sig. Du kan definiera tröskeln och varaktighet för smart utelåsning.

Lösen ords principen för Azure AD gäller inte för användar konton som synkroniseras från en lokal AD DS-miljö med hjälp av Azure AD Connect, om du inte aktiverar *EnforceCloudPasswordPolicyForPasswordSyncedUsers*.

Följande alternativ för lösen ords princip för Azure AD definieras. Om inget annat anges kan du inte ändra de här inställningarna:

| Egenskap | Krav |
| --- | --- |
| Tillåtna tecken |<ul><li>A – Ö</li><li>a-ö</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \` ~ " ( ) ;</li> <li>tomt utrymme</li></ul> |
| Tecken tillåts inte | Unicode-tecken. |
| Lösen ords begränsningar |<ul><li>Minst 8 tecken och högst 256 tecken.</li><li>Kräver tre av fyra av följande:<ul><li>Gemener.</li><li>Versaler.</li><li>Tal (0-9).</li><li>Symboler (se tidigare lösen ords begränsningar).</li></ul></li></ul> |
| Giltighets tid för lösen ord (högsta ålder för lösen ord) |<ul><li>Standardvärde: **90** dagar.</li><li>Värdet kan konfigureras med hjälp av `Set-MsolPasswordPolicy` cmdleten från Azure Active Directory-modulen för Windows PowerShell.</li></ul> |
| Meddelande om förfallo datum för lösen ord (när användare får ett meddelande om förfallo datum för lösen ord) |<ul><li>Standardvärde: **14** dagar (innan lösen ordet upphör att gälla).</li><li>Värdet kan konfigureras med hjälp av `Set-MsolPasswordPolicy` cmdleten.</li></ul> |
| Lösen ordets giltighets tid (Tillåt att lösen ord aldrig upphör att gälla) |<ul><li>Standardvärde: **falskt** (indikerar att lösen ordet har ett förfallo datum).</li><li>Värdet kan konfigureras för enskilda användar konton med hjälp av `Set-MsolUser` cmdleten.</li></ul> |
| Ändrings historik för lösen ord | Det sista lösen ordet *kan inte* användas igen när användaren ändrar ett lösen ord. |
| Historik för lösen ords återställning | Det senaste lösen ordet *kan* användas igen när användaren återställer ett bortglömt lösen ord. |

## <a name="administrator-reset-policy-differences"></a>Skillnader i återställningsprincip för administratörer

Som standard är administratörs konton aktiverade för lösen ords återställning via självbetjäning och en stark standard princip för lösen ords återställning med *två grindar* tillämpas. Den här principen kan skilja sig från den som du har definierat för användarna, och den här principen kan inte ändras. Du bör alltid testa funktionen för återställning av lösen ord som en användare utan tilldelade Azure Administrator-roller.

Med en princip med två-grind funktioner kan administratörer inte använda säkerhets frågor.

Principen för två-grind kräver två delar av autentiseringsdata, till exempel en e-postadress, en Authenticator-app eller ett telefonnummer. En princip för två grindar gäller i följande fall:

* Alla följande roller för Azure-administratörer påverkas:
  * Support administratör
  * Tjänstsupportadministratör
  * Faktureringsadministratör
  * Support för partner 1
  * Support för partner – nivå 2
  * Exchange-administratör
  * Mailbox-administratör
  * Skype for Business-administratör
  * Användaradministratör
  * Katalog skrivare
  * Global administratör eller företags administratör
  * SharePoint-administratör
  * Administratör för efterlevnad
  * Program administratör
  * Säkerhetsadministratör
  * Privilegie rad roll administratör
  * Intune-administratör
  * Lokal administratör för Azure AD-ansluten enhet
  * Administratör för tjänsten Application Proxy
  * Dynamics 365-administratör
  * Power BI-tjänstadministratör
  * Administratör för autentisering
  * Lösenordsadministratör
  * Administratör för privilegie rad autentisering

* Om 30 dagar har förflutit i en utvärderings prenumeration; eller
* En anpassad domän har kon figurer ATS för din Azure AD-klient, till exempel *contoso.com*; eller
* Azure AD Connect synkroniserar identiteter från din lokala katalog

Du kan inaktivera användningen av SSPR för administratörs konton med hjälp av PowerShell-cmdleten [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) . `-SelfServePasswordResetEnabled $False`Parametern inaktiverar SSPR för administratörer.

### <a name="exceptions"></a>Undantag

En princip för en enskild grind kräver en typ av autentiseringsinformation, till exempel en e-postadress eller ett telefonnummer. En princip för en-grind gäller i följande fall:

* Den ligger inom de första 30 dagarna i en utvärderings prenumeration. eller
* En anpassad domän har inte kon figurer ATS för din Azure AD-klient så använder standard **. onmicrosoft.com*. Standard domänen **. onmicrosoft.com* rekommenderas inte för produktions användning. särskilt
* Azure AD Connect synkroniserar inte identiteter

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Principer för förfallo datum för lösen ord

En *Global administratör* eller *användar administratör* kan använda [Microsoft Azure AD-modul för Windows PowerShell](/powershell/module/Azuread/) för att ange att användar lösen ord inte upphör att gälla.

Du kan också använda PowerShell-cmdletar för att ta bort konfigurationen som aldrig upphör att gälla eller se vilka användar lösen ord som aldrig upphör att gälla.

Den här vägledningen gäller för andra leverantörer, till exempel Intune och Microsoft 365, som också förlitar sig på Azure AD för identitets-och katalog tjänster. Lösen ordets giltighets tid är den enda delen av principen som kan ändras.

> [!NOTE]
> Endast lösen ord för användar konton som inte har synkroniserats via Azure AD Connect kan konfigureras så att de inte upphör att gälla. Mer information om Active Directory-synkronisering finns i [ansluta AD med Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ange eller kontrollera lösenordsprinciper med hjälp av PowerShell

Kom igång genom att [Ladda ned och installera Azure AD PowerShell-modulen](/powershell/module/Azuread/) och [ansluta den till din Azure AD-klient](/powershell/module/azuread/connect-azuread#examples).

När modulen har installerats använder du följande steg för att slutföra varje aktivitet vid behov.

### <a name="check-the-expiration-policy-for-a-password"></a>Kontrol lera förfallo principen för ett lösen ord

1. Öppna en PowerShell-prompt och [Anslut till din Azure AD-klient](/powershell/module/azuread/connect-azuread#examples) med ett *globalt administratörs* -eller *användar administratörs* konto.
1. Kör något av följande kommandon för antingen en enskild användare eller för alla användare:

   * Kör följande cmdlet för att se om en enskild användares lösen ord är inställda på att aldrig upphöra att gälla. Ersätt `<user ID>` med användar-ID: t för den användare som du vill kontrol lera, t. ex. *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Om du vill se att **lösen ordet aldrig upphör att gälla** för alla användare kör du följande cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Ange ett lösen ord som upphör att gälla

1. Öppna en PowerShell-prompt och [Anslut till din Azure AD-klient](/powershell/module/azuread/connect-azuread#examples) med ett *globalt administratörs* -eller *användar administratörs* konto.
1. Kör något av följande kommandon för antingen en enskild användare eller för alla användare:

   * Om du vill ange ett lösen ord för en användare så att lösen ordet upphör att gälla kör du följande cmdlet. Ersätt `<user ID>` med användar-ID: t för den användare som du vill kontrol lera, t. ex. *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Använd följande cmdlet för att ange lösen orden för alla användare i organisationen så att de upphör att gälla:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Ange att ett lösen ord aldrig upphör att gälla

1. Öppna en PowerShell-prompt och [Anslut till din Azure AD-klient](/powershell/module/azuread/connect-azuread#examples) med ett *globalt administratörs* -eller *användar administratörs* konto.
1. Kör något av följande kommandon för antingen en enskild användare eller för alla användare:

   * Kör följande cmdlet för att ange att lösen ordet för en användare aldrig upphör att gälla. Ersätt `<user ID>` med användar-ID: t för den användare som du vill kontrol lera, t. ex. *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Om du vill ange lösen orden för alla användare i en organisation som aldrig upphör att gälla kör du följande cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Lösen orden har angetts till `-PasswordPolicies DisablePasswordExpiration` fortfarande ålder baserat på `pwdLastSet` attributet. `pwdLastSet`Om du ändrar förfallo datum till, och om du ändrar förfallo datum för `-PasswordPolicies None` , kräver alla lösen ord som har en `pwdLastSet` äldre än 90 dagar användaren att ändra dem nästa gång de loggar in. Den här ändringen kan påverka ett stort antal användare.

## <a name="next-steps"></a>Nästa steg

För att komma igång med SSPR, se [Självstudier: gör det möjligt för användare att låsa upp kontot eller återställa lösen ord med hjälp av Azure Active Directory självbetjäning för återställning av lösen ord](tutorial-enable-sspr.md).

Om du eller användare har problem med SSPR kan du läsa mer i [Felsöka lösen ords återställning via självbetjäning](./troubleshoot-sspr.md)
