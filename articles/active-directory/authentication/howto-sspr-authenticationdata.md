---
title: Fyll i kontakt informationen i förväg för lösen ords återställning via självbetjäning – Azure Active Directory
description: Lär dig att fylla i kontakt information i förväg för användare av Azure Active Directory självbetjäning för återställning av lösen ord (SSPR) så att de kan använda funktionen utan att slutföra en registrerings process.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861161"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Förkonfigurera kontakt information för användarautentisering för Azure Active Directory lösen ords återställning via självbetjäning (SSPR)

För att kunna använda Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) måste autentiserings kontakt information för en användare finnas. Vissa organisationer har användare som själva registrerar sina autentiseringsdata. Andra organisationer föredrar att synkronisera från autentiseringsdata som redan finns i Active Directory Domain Services (AD DS). Dessa synkroniserade data görs tillgängliga för Azure AD och SSPR utan användar interaktion. När användarna behöver ändra eller återställa sina lösen ord kan de göra det även om de inte har registrerat sin kontakt information tidigare.

Du kan förkonfigurera kontakt information för autentisering om du uppfyller följande krav:

* Du har formaterat data korrekt i din lokala katalog.
* Du har konfigurerat [Azure AD Connect](../hybrid/how-to-connect-install-express.md) för din Azure AD-klient.

Telefonnummer måste vara i formatet *+ CountryCode telefonnummer*, till exempel *+ 1 4251234567*.

> [!NOTE]
> Det måste finnas ett blank steg mellan lands koden och telefonnumret.
>
> Lösen ords återställning stöder inte telefon tillägg. Även i formatet *+ 1 4251234567X12345* tas tilläggen bort innan anropet placeras.

## <a name="fields-populated"></a>Ifyllda fält

Om du använder standardinställningarna i Azure AD Connect görs följande mappningar för att fylla i kontakt information för autentisering för SSPR:

| Lokalt Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Arbetstelefon |
| mobil                       | Mobiltelefon |

När en användare har verifierat mobiltelefon numret fylls *telefon* fältet under **information om kontakt information** i Azure AD också i det numret.

## <a name="authentication-contact-info"></a>Information om autentisering av kontakt

På sidan **autentiseringsmetoder** för en Azure AD-användare i Azure Portal kan en global administratör manuellt ange kontakt information för autentisering. Du kan granska befintliga metoder under avsnittet *användbara autentiseringsmetoder* eller **+ Lägg till autentiseringsmetoder**, som du ser i följande exempel skärm bild:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Hantera autentiseringsmetoder från Azure Portal":::

Följande överväganden gäller för den här autentiseringens kontakt information:

* Om *telefon* fältet är ifyllt och *mobil telefonen* är aktiverat i SSPR-principen, ser användaren det numret på registrerings sidan för lösen ords återställning och under arbets flödet för lösen ords återställning.
* Om *e-* postfältet är ifyllt och *e-postmeddelandet* är aktiverat i SSPR-principen ser användaren den e-postadressen på registrerings sidan för lösen ords återställning och under arbets flödet för lösen ords återställning.

## <a name="security-questions-and-answers"></a>Säkerhets frågor och svar

Säkerhets frågorna och svaren lagras på ett säkert sätt i din Azure AD-klient och är bara tillgängliga för användare via [registrerings portalen för SSPR](https://aka.ms/ssprsetup). Administratörer kan inte se, ange eller ändra innehållet i en annan användares frågor och svar.

## <a name="what-happens-when-a-user-registers"></a>Vad händer när en användare registrerar sig

När en användare registrerar, anger registrerings sidan följande fält:

* **Telefon för autentisering**
* **E-postautentisering**
* **Säkerhets frågor och svar**

Om du har angett ett värde för *mobil telefon* eller *alternativt e-postmeddelande* kan användarna omedelbart använda dessa värden för att återställa sina lösen ord, även om de inte har registrerats för tjänsten.

Användarna kan också se dessa värden när de registrerar sig för första gången och kan ändra dem om de vill. När de har registrerats sparas de här värdena i fälten för *autentiserings* *-och e-postadresserna* .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ange och läsa autentiseringsdata via PowerShell

Följande fält kan ställas in via PowerShell:

* *Alternativ e-postadress*
* *Mobiltelefon*
* *Arbetstelefon*
    * Kan bara anges om du inte synkroniserar med en lokal katalog.

> [!IMPORTANT]
> Det finns en känd brist på paritet i kommando funktioner mellan PowerShell v1 och PowerShell V2. [Microsoft Graph REST API (beta) för autentiseringsmetoder](/graph/api/resources/authenticationmethods-overview) är det aktuella teknik fokus som ger modern interaktion.

### <a name="use-powershell-version-1"></a>Använd PowerShell version 1

Kom igång genom att [Ladda ned och installera Azure AD PowerShell-modulen](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). När du har installerat det använder du följande steg för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ange autentiseringsdata med PowerShell version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Läs data för autentisering med PowerShell version 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Läs e-postalternativen för autentisering via telefon och autentisering

Om du vill läsa **e-postmeddelandet** för **autentisering via telefon** och autentisering när du använder PowerShell version 1, använder du följande kommandon:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Använd PowerShell version 2

Kom igång genom att [Ladda ned och installera Azure AD version 2 PowerShell-modulen](/powershell/module/azuread/).

Kör följande kommandon för att snabbt installera från de senaste versionerna av PowerShell som stöder `Install-Module` . Den första raden kontrollerar om modulen redan är installerad:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

När modulen har installerats använder du följande steg för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ange autentiseringsdata med PowerShell version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Läs autentiseringsdata med PowerShell version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nästa steg

När du har angett autentiserings kontakt information i förväg för användare, slutför du följande självstudie för att aktivera självbetjäning för återställning av lösen ord:

> [!div class="nextstepaction"]
> [Aktivera återställning av lösen ord för självbetjäning i Azure AD](tutorial-enable-sspr.md)
