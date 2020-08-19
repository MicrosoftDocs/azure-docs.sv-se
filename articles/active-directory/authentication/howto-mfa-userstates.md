---
title: Aktivera Multi-Factor Authentication per användare – Azure Active Directory
description: Lär dig hur du aktiverar Azure-Multi-Factor Authentication per användare genom att ändra användar tillstånd
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d949f4b10bc6b7b592556d78edfcf02a05ec7144
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565782"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Aktivera Azure MFA per användare för att skydda inloggningshändelser

Om du vill skydda användar inloggnings händelser i Azure AD kan du kräva Multi-Factor Authentication (MFA). Att aktivera Azure Multi-Factor Authentication att använda principer för villkorlig åtkomst är den rekommenderade metoden för att skydda användare. Villkorlig åtkomst är en Azure AD Premium P1-eller P2-funktion som gör att du kan använda regler för att kräva MFA vid behov i vissa scenarier. Information om hur du kommer igång med villkorlig åtkomst finns i [Självstudier: skydda användar inloggnings händelser med Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

För kostnads fria Azure AD-klienter utan villkorlig åtkomst kan du [använda säkerhets inställningar för att skydda användare](../fundamentals/concept-fundamentals-security-defaults.md). Användarna uppmanas att använda MFA vid behov, men du kan inte definiera egna regler för att styra beteendet.

Om det behövs kan du istället aktivera varje konto för Azure-Multi-Factor Authentication per användare. När användare Aktiver ATS individuellt utför de Multi-Factor Authentication varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _kom ihåg MFA på betrodda enheter_ är aktive rad).

Ändring av användar tillstånd rekommenderas inte om inte dina Azure AD-licenser inkluderar villkorlig åtkomst och du inte vill använda säkerhets inställningar. Mer information om olika sätt att aktivera MFA finns i [funktioner och licenser för Azure Multi-Factor Authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Den här artikeln beskriver hur du visar och ändrar status för Azure-Multi-Factor Authentication per användare. Om du använder villkorlig åtkomst eller säkerhets inställningar kan du inte granska eller aktivera användar konton med de här stegen.
>
> Att aktivera Azure Multi-Factor Authentication via en princip för villkorlig åtkomst ändrar inte användarens tillstånd. Är inte alarmed om användarna ser inaktiverade. Villkorlig åtkomst ändrar inte tillstånd.
>
> **Aktivera eller Använd inte Azure-Multi-Factor Authentication per användare om du använder principer för villkorlig åtkomst.**

## <a name="azure-multi-factor-authentication-user-states"></a>Användar tillstånd för Azure-Multi-Factor Authentication

En användares tillstånd visar om en administratör har registrerat dem i Azure-Multi-Factor Authentication per användare. Användar konton i Azure Multi-Factor Authentication har följande tre distinkta tillstånd:

| Stat | Beskrivning | Äldre autentisering påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering påverkas |
|:---:| --- |:---:|:--:|:--:|
| Inaktiverad | Standard läget för en användare som inte har registrerats i Azure-Multi-Factor Authentication per användare. | Nej | Nej | Nej |
| Enabled | Användaren är registrerad i Azure-Multi-Factor Authentication per användare, men kan fortfarande använda sitt lösen ord för äldre autentisering. Om användaren ännu inte har registrerat MFA-autentiseringsmetoderna får användaren en uppfråga om att registrera sig nästa gången de loggar in med modern autentisering (till exempel via en webbläsare). | Nej. Äldre autentisering fortsätter att fungera tills registrerings processen har slutförts. | Ja. När sessionen har gått ut krävs Azure Multi-Factor Authentication registrering.| Ja. När åtkomsttoken har upphört att gälla krävs Azure Multi-Factor Authentication registrering. |
| Enforced | Användaren registreras per användare i Azure Multi-Factor Authentication. Om användaren ännu inte har registrerat autentiseringsmetoder får han eller hon ett meddelande om att registrera sig nästa gången de loggar in med modern autentisering (till exempel via en webbläsare). Användare som slutför registreringen i det *aktiverade* läget flyttas automatiskt till *tvingande* tillstånd. | Ja. Appar kräver applösenord. | Ja. Azure Multi-Factor Authentication krävs vid inloggning. | Ja. Azure Multi-Factor Authentication krävs vid inloggning. |

Alla användare börjar vara *inaktiverade*. När du registrerar användare i Azure-Multi-Factor Authentication per användare, ändras deras status till *aktive rad*. När aktiverade användare loggar in och slutför registrerings processen ändras deras status till *tvingande*. Administratörer kan flytta användare mellan tillstånd, inklusive från *Tvingad* till *aktive rad* eller *inaktive*rad.

> [!NOTE]
> Om MFA per användare aktive ras på nytt för en användare och användaren inte registreras igen, kommer deras MFA-tillstånd inte att övergå från *aktive rad* till *tvingande* i MFA management UI. Administratören måste flytta användaren direkt till *tvingande*.

## <a name="view-the-status-for-a-user"></a>Visa status för en användare

Om du vill visa och hantera användar tillstånd slutför du följande steg för att komma åt sidan Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Sök efter och välj *Azure Active Directory*och välj sedan **användare**  >  **alla användare**.
1. Välj **Multi-Factor Authentication**. Du kan behöva bläddra till höger för att se det här meny alternativet. Välj skärm bilden nedan om du vill se hela Azure Portals fönster och meny plats: [ ![ Välj Multi-Factor Authentication från fönstret användare i Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. En ny sida öppnas som visar användar statusen, som du ser i följande exempel.
   ![Skärm bild som visar information om exempel på användar tillstånd för Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Ändra status för en användare

Utför följande steg för att ändra Azure-Multi-Factor Authentication per användare för en användare:

1. Använd föregående steg för att [Visa statusen för en användare](#view-the-status-for-a-user) att komma till sidan Azure Multi-Factor Authentication **användare** .
1. Hitta den användare som du vill aktivera för Azure-Multi-Factor Authentication per användare. Du kan behöva ändra vyn överst till **användare**.
   ![Välj användaren att ändra status för från fliken användare](./media/howto-mfa-userstates/enable1.png)
1. Markera kryss rutan bredvid namnen på de användare som du vill ändra status för.
1. Välj **Aktivera** eller **inaktivera**på höger sida under **snabb steg**. I följande exempel har användaren *John Smith* en kontroll bredvid sitt namn och aktive ras för användning: ![ Aktivera vald användare genom att klicka på Aktivera på snabb menyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till att *tillämpas* när de registrerar sig för Azure Multi-Factor Authentication. Ändra inte användar tillstånd manuellt så att det *tillämpas* om inte användaren redan är registrerad eller om den är acceptabel för att användaren ska kunna uppleva avbrott i anslutningar till äldre autentiseringsprotokoll.

1. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användarna ska du meddela dem via e-post. Berätta för användarna att en uppmaning visas för att be dem att registrera sig nästa gången de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering måste de också skapa applösenord. Mer information finns i [användar handboken för Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) för att hjälpa dem att komma igång.

## <a name="change-state-using-powershell"></a>Ändra tillstånd med PowerShell

Om du vill ändra användar tillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/)ändrar du `$st.State` parametern för ett användar konto. Det finns tre möjliga tillstånd för ett användar konto:

* *Aktiverad*
* *Enforced*
* *Disabled* (Inaktiverat)  

I allmänhet ska du inte flytta användare direkt till *tvingande* tillstånd om de inte redan har registrerats för MFA. Om du gör det slutar äldre autentiserings-appar att fungera eftersom användaren inte har gått igenom Azure Multi-Factor Authentication registrering och fått ett [applösenord](howto-mfa-app-passwords.md). I vissa fall kan det här problemet vara önskvärt, men påverkar användar upplevelsen tills användaren registrerar sig.

Kom igång genom att installera *MSOnline* -modulen med hjälp av [install-module](/powershell/module/powershellget/install-module) enligt följande:

```PowerShell
Install-Module MSOnline
```

Anslut sedan med [Connect-MSOLService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Följande exempel på PowerShell-skript aktiverar MFA för en enskild användare med namnet *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Att använda PowerShell är ett användbart alternativ när du behöver massredigera användare. Följande skript upprepas genom en lista över användare och aktiverar MFA på sina konton. Definiera de användar konton som har angetts på den första raden `$users` enligt följande:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Om du vill inaktivera MFA hämtar följande exempel en användare med [Get-MsolUser](/powershell/module/msonline/get-msoluser)och tar sedan bort alla *StrongAuthenticationRequirements* som definierats för den definierade användaren med hjälp av [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Du kan också direkt inaktivera MFA för en användare som använder [set-MsolUser](/powershell/module/msonline/set-msoluser) på följande sätt:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Konvertera användare från per användare MFA till villkorlig åtkomst

Följande PowerShell kan hjälpa dig att göra konverteringen till baserade Azure-Multi-Factor Authentication med villkorlig åtkomst.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Om MFA återaktiveras på en användare och användaren inte registreras på nytt, kommer deras MFA-tillstånd inte att övergå från *aktive rad* till *tvingande* i MFA management UI. I så fall måste administratören flytta användaren direkt till *tvingande*.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar inställningar för Azure-Multi-Factor Authentication finns i  [Konfigurera inställningar för azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Information om hur du hanterar användar inställningar för Azure Multi-Factor Authentication finns i [hantera användar inställningar med azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Mer information om varför en användare uppmanas att göra MFA finns i [Azure Multi-Factor Authentication-rapporter](howto-mfa-reporting.md).
