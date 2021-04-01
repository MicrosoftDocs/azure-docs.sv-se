---
title: Certifikatbaserad autentisering – Azure Active Directory
description: Lär dig hur du konfigurerar certifikatbaserad autentisering i din miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3ba84bb3ee38981217e72f8372a836b03647083d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861348"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Komma igång med certifikatbaserad autentisering i Azure Active Directory

Med certifikatbaserad autentisering kan du autentiseras genom att Azure Active Directory med ett klient certifikat på en Windows-, Android-eller iOS-enhet när du ansluter ditt Exchange Online-konto till:

- Microsoft Mobile-program som Microsoft Outlook och Microsoft Word
- Exchange ActiveSync-klienter (EAS)

Genom att konfigurera den här funktionen slipper du ange en kombination av användar namn och lösen ord i vissa e-post-och Microsoft Office program på din mobila enhet.

Det här avsnittet:

- Innehåller anvisningar om hur du konfigurerar och använder certifikatbaserad autentisering för användare av klienter i Office 365 Enterprise, Business, Education och amerikanska myndigheters planer. Den här funktionen är tillgänglig i för hands versionen i Office 365 Kina, USA: s försvars myndigheter och amerikanska federala myndigheter.
- Förutsätter att du redan har en [PKI (Public Key Infrastructure)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) och [AD FS](../hybrid/how-to-connect-fed-whatis.md) konfigurerat.

## <a name="requirements"></a>Krav

Följande uttryck måste vara uppfyllda för att du ska kunna konfigurera certifikatbaserad autentisering:

- Certifikatbaserad autentisering (CBA) stöds bara för federerade miljöer för webb läsar program, inbyggda klienter som använder moderna autentisering (ADAL) eller MSAL-bibliotek. Det enda undantaget är Exchange Active Sync (EAS) för Exchange Online (EXO), som kan användas för federerade och hanterade konton.
- Rot certifikat utfärdaren och alla mellanliggande certifikat utfärdare måste konfigureras i Azure Active Directory.
- Varje certifikat utfärdare måste ha en lista över återkallade certifikat (CRL) som kan refereras via en Internet-riktad URL.
- Du måste ha minst en certifikat utfärdare som kon figurer ATS i Azure Active Directory. Du kan hitta relaterade steg i avsnittet [Konfigurera certifikat utfärdare](#step-2-configure-the-certificate-authorities) .
- För Exchange ActiveSync-klienter måste klient certifikatet ha användarens dirigerbart e-postadress i Exchange Online antingen i huvud namnet eller RFC822 namn-värdet i fältet Alternativt namn på certifikat mottagare. Azure Active Directory mappar RFC822-värdet till attributet proxy i katalogen.
- Klient enheten måste ha åtkomst till minst en certifikat utfärdare som utfärdar klient certifikat.
- Ett klient certifikat för klientautentisering måste ha utfärdats till klienten.

>[!IMPORTANT]
>Den maximala storleken för en lista över återkallade certifikat för att Azure Active Directory hämta och cachelagra är 20 MB och den tid det tar att hämta listan över återkallade certifikat får inte överstiga 10 sekunder.  Om Azure Active Directory inte kan hämta en lista över återkallade certifikat, kommer certifikatbaserade autentiseringar som använder certifikat som utfärdats av motsvarande CA att Miss Miss Metod tips för att se till att CRL-filer är inom storleks begränsningarna är att behålla certifikat livs längderna inom rimliga gränser och rensa ut certifikat som har upphört att gälla.

## <a name="step-1-select-your-device-platform"></a>Steg 1: Välj din enhets plattform

Som ett första steg för den enhets plattform som du bryr dig om måste du läsa följande:

- Stöd för Office Mobile-program
- De särskilda implementerings kraven

Relaterad information finns för följande enhets plattformar:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Steg 2: Konfigurera certifikat utfärdare

Om du vill konfigurera certifikat utfärdare i Azure Active Directory, för varje certifikat utfärdare, laddar du upp följande:

* Den offentliga delen av certifikatet i *. cer* -format
* URL: er som riktas mot Internet där listor över återkallade certifikat (CRL) finns

Schemat för en certifikat utfärdare ser ut så här:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

För-konfigurationen kan du använda [Azure Active Directory PowerShell version 2](/powershell/azure/active-directory/install-adv2):

1. Starta Windows PowerShell med administratörs behörighet.
2. Installera Azure AD-modulen version [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) eller högre.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Som ett första konfigurations steg måste du upprätta en anslutning till din klient organisation. Så snart en anslutning till din klient organisation finns kan du granska, lägga till, ta bort och ändra betrodda certifikat utfärdare som definieras i din katalog.

### <a name="connect"></a>Ansluta

Använd cmdleten [Connect-AzureAD](/powershell/module/azuread/connect-azuread) för att upprätta en anslutning till klienten:

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Hämta

Om du vill hämta betrodda certifikat utfärdare som har definierats i din katalog använder du cmdleten [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority) .

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Lägg till

Om du vill skapa en betrodd certifikat utfärdare använder du cmdleten [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority) och anger attributet **crlDistributionPoint** till ett korrekt värde:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Ta bort

Om du vill ta bort en betrodd certifikat utfärdare använder du cmdleten [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Ändra

Om du vill ändra en betrodd certifikat utfärdare använder du cmdleten [set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Steg 3: Konfigurera åter kallelse

Om du vill återkalla ett klient certifikat hämtar Azure Active Directory listan över återkallade certifikat (CRL) från de URL: er som laddats upp som en del av certifikat utfärdarens information och cachelagrar den. Den senaste publicerings tids stämplingen (egenskapen **effektiv datum** ) i listan över återkallade certifikat används för att se till att CRL fortfarande är giltig. CRL: en refereras regelbundet för att återkalla åtkomsten till certifikat som ingår i listan.

Om det krävs ett omedelbart återkallning (till exempel om en användare förlorar en enhet) kan användarens autentiseringstoken bli ogiltig. Om du vill ogiltig verifiera token anger du fältet **StsRefreshTokenValidFrom** för den aktuella användaren med hjälp av Windows PowerShell. Du måste uppdatera fältet **StsRefreshTokenValidFrom** för varje användare som du vill återkalla åtkomsten för.

För att säkerställa att åter kallelsen kvarstår måste du ange det **effektiva datumet** för CRL: en till ett datum efter värdet som anges av **StsRefreshTokenValidFrom** och se till att certifikatet i fråga är i listan över återkallade certifikat.

Följande steg beskriver processen för att uppdatera och ogiltig verifiera autentiseringstoken genom att ange fältet **StsRefreshTokenValidFrom** .

**Så här konfigurerar du åter kallelse:**

1. Anslut med autentiseringsuppgifter för admin till MSOL-tjänsten:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Hämta det aktuella StsRefreshTokensValidFrom-värdet för en användare:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Konfigurera ett nytt StsRefreshTokensValidFrom-värde för användaren som är lika med den aktuella tidsstämpeln:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

Det datum som du anger måste vara i framtiden. Om datumet inte är i framtiden anges inte egenskapen **StsRefreshTokensValidFrom** . Om datumet är i framtiden ställs **StsRefreshTokensValidFrom** in på den aktuella tiden (inte det datum som anges med kommandot Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Steg 4: testa konfigurationen

### <a name="testing-your-certificate"></a>Testa ditt certifikat

Som ett första konfigurations test bör du försöka logga in till [Outlook Web Access](https://outlook.office365.com) eller [SharePoint Online](https://microsoft.sharepoint.com) med din **enhets webbläsare**.

Om din inloggning lyckas, vet du att:

- Användar certifikatet har etablerats till din test enhet
- AD FS har kon figurer ATS korrekt

### <a name="testing-office-mobile-applications"></a>Testa Office Mobile-program

**Så här testar du certifikatbaserad autentisering i ditt mobila Office-program:**

1. Installera ett Office-mobilt program (till exempel OneDrive) på test enheten.
3. Starta programmet.
4. Ange ditt användar namn och välj sedan det användar certifikat som du vill använda.

Du måste vara inloggad.

### <a name="testing-exchange-activesync-client-applications"></a>Testa Exchange ActiveSync-klientprogram

För att få åtkomst till Exchange ActiveSync (EAS) via certifikatbaserad autentisering måste en EAS-profil som innehåller klient certifikatet vara tillgänglig för programmet.

EAS-profilen måste innehålla följande information:

- Användar certifikatet som ska användas för autentisering

- EAS-slutpunkt (till exempel outlook.office365.com)

En EAS-profil kan konfigureras och placeras på enheten genom användning av hantering av mobila enheter (MDM) som Intune eller genom att manuellt placera certifikatet i EAS-profilen på enheten.

### <a name="testing-eas-client-applications-on-android"></a>Testa EAS-klientprogram på Android

**Så här testar du certifikatautentisering:**

1. Konfigurera en EAS-profil i programmet som uppfyller kraven i föregående avsnitt.
2. Öppna programmet och verifiera att e-postsynkroniseringen är synkroniserad.

## <a name="next-steps"></a>Nästa steg

[Ytterligare information om certifikatbaserad autentisering på Android-enheter.](active-directory-certificate-based-authentication-android.md)

[Ytterligare information om certifikatbaserad autentisering på iOS-enheter.](active-directory-certificate-based-authentication-ios.md)
