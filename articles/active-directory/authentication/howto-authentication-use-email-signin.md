---
title: Logga in med e-post som ett alternativt inloggnings-ID för Azure Active Directory
description: Lär dig hur du konfigurerar och gör det möjligt för användare att logga in på Azure Active Directory med sin e-postadress som ett alternativt inloggnings-ID (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255975"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Logga in för att Azure Active Directory med e-post som ett alternativt inloggnings-ID (för hands version)

> [!NOTE]
> Logga in på Azure AD med e-post som ett alternativt inloggnings-ID är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Många organisationer vill att användarna ska kunna logga in på Azure Active Directory (Azure AD) med samma autentiseringsuppgifter som den lokala katalog miljön. Med den här metoden, som kallas hybrid autentisering, behöver användarna bara komma ihåg en uppsättning autentiseringsuppgifter.

Vissa organisationer har inte flyttats till hybrid autentisering av följande anledningar:

* Som standard har Azure AD-User Principal Name (UPN) angetts till samma UPN som den lokala katalogen.
* Att ändra Azure AD UPN skapar en mis-matchning mellan lokal-och Azure AD-miljöer som kan orsaka problem med vissa program och tjänster.
* På grund av affärs-eller efterföljandekrav vill inte organisationen använda det lokala UPN för att logga in på Azure AD.

För att hjälpa till med att flytta till hybrid autentisering kan du nu konfigurera Azure AD så att användarna kan logga in med ett e-postmeddelande i din verifierade domän som ett alternativt inloggnings-ID. Om *contoso* till exempel har ändrats till *Fabrikam*, i stället för att fortsätta logga in med det äldre `balas@contoso.com` UPN, kan du nu använda e-post som ett alternativt inloggnings-ID. För att få åtkomst till ett program eller tjänster loggar användare in på Azure AD med sin tilldelade e-postadress, till exempel `balas@fabrikam.com` .

Den här artikeln visar hur du aktiverar och använder e-post som ett alternativt inloggnings-ID. Den här funktionen är tillgänglig i Azure AD Free Edition och högre.

> [!NOTE]
> Den här funktionen är endast för Azure-autentiserade Azure AD-användare.

> [!NOTE]
> Den här funktionen stöds för närvarande inte på Azure AD-anslutna Windows 10-enheter för klienter med molnbaserad autentisering. Den här funktionen kan inte användas för Hybrid Azure AD-anslutna enheter.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Översikt över inloggnings metoder för Azure AD

Om du vill logga in på Azure AD anger du ett namn som unikt identifierar sitt konto. Tidigare kunde du bara använda Azure AD UPN som inloggnings namn.

Den här metoden var perfekt för organisationer där det lokala UPN: en är användarens önskade inloggnings-e-post. Dessa organisationer ställer in Azure AD UPN till exakt samma värde som det lokala UPN, och användarna får en enhetlig inloggnings upplevelse.

I vissa organisationer används dock inte det lokala UPN-namnet som inloggnings namn. I lokala miljöer konfigurerar du den lokala AD DS för att tillåta inloggning med ett alternativt inloggnings-ID. Att ange Azure AD UPN till samma värde som det lokala UPN: t är inte ett alternativ eftersom Azure AD sedan kräver att användare loggar in med det värdet.

Den typiska lösningen på det här problemet var att ange Azure AD UPN till den e-postadress som användaren förväntar sig att logga in med. Den här metoden fungerar, men resulterar i olika UPN mellan lokala AD och i Azure AD, och den här konfigurationen är inte kompatibel med alla Microsoft 365 arbets belastningar.

En annan metod är att synkronisera Azure AD och lokala UPN: er till samma värde och sedan konfigurera Azure AD så att användarna kan logga in på Azure AD med en verifierad e-postadress. För att tillhandahålla den här funktionen definierar du en eller flera e-postadresser i användarens *proxyAddresses* -attribut i den lokala katalogen. *ProxyAddresses* synkroniseras sedan automatiskt till Azure AD med hjälp av Azure AD Connect.

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Logga in på Azure AD med e-post som ett alternativt inloggnings-ID finns i Azure AD Frees versionen och högre.

I det aktuella förhands gransknings läget gäller följande begränsningar när en användare loggar in med ett icke-UPN-e-postmeddelande som ett alternativt inloggnings-ID:

* Användare kan se sitt UPN, även när det är inloggat med e-post som inte är UPN. Följande exempel beteende kan visas:
    * Användaren uppmanas att logga in med UPN när den hänvisas till Azure AD-inloggning med `login_hint=<non-UPN email>` .
    * När en användare loggar in med ett icke-UPN-e-postmeddelande och anger ett felaktigt lösen ord ändras sidan *"Ange ditt lösen ord"* så att UPN-filen visas.
    * På vissa Microsoft-webbplatser och appar, till exempel [https://portal.azure.com](https://portal.azure.com) och Microsoft Office, visas **konto hanterarens** kontroll normalt i det övre högra hörnet i det övre högra hörnet, och användarens UPN visas istället för e-postmeddelandet som inte är UPN används för att logga in.

* Vissa flöden är för närvarande inte kompatibla med e-postmeddelandet som inte är UPN, till exempel följande:
    * Identitets skydd matchar för närvarande inte e-postalternativa inloggnings-ID med *läckta autentiseringsuppgifter för identifiering av autentiseringsuppgifter* . Den här identifieringen av risker använder UPN för att matcha autentiseringsuppgifter som har läckts. Mer information finns i [Azure AD Identity Protection identifiering av risker och reparationer][identity-protection].
    * B2B-inbjudningar som skickas till ett alternativt inloggnings-ID, stöds inte fullt ut. När du har accepterat en inbjudan som skickats till ett e-postmeddelande som ett alternativt inloggnings-ID, kan du logga in med det alternativa e-postmeddelandet kanske inte fungerar för användaren på den klient organisation

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synkronisera inloggnings-e-postadresser till Azure AD

Traditionell Active Directory Domain Services-autentisering (AD DS) eller Active Directory Federation Services (AD FS) (AD FS) sker direkt i nätverket och hanteras av din AD DS-infrastruktur. Med hybrid autentisering kan användarna i stället logga in direkt till Azure AD.

För att stödja den här metoden för Hybrid autentisering, synkroniserar du din lokala AD DS-miljö till Azure AD med hjälp av [Azure AD Connect][azure-ad-connect] och konfigurerar den för att använda PHS (Password hash Sync) eller Pass-Through autentisering (PTA).

I båda konfigurations alternativen skickar användaren sitt användar namn och lösen ord till Azure AD, som validerar autentiseringsuppgifterna och utfärdar en biljett. När användarna loggar in på Azure AD tar det bort behovet av att din organisation ska vara värd för och hantera en AD FS-infrastruktur.

![Diagram över Azure AD hybrid identitet med hash-synkronisering av lösen ord](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram över Azure AD hybrid identitet med direktautentisering](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Ett av användarattribut som synkroniseras automatiskt av Azure AD Connect är *proxyAddresses*. Om användarna har en e-postadress som definierats i AD DS-lokal AD DS som en del av *proxyAddresses* -attributet, synkroniseras den automatiskt till Azure AD. Den här e-postadressen kan sedan användas direkt i inloggnings processen för Azure AD som ett alternativt inloggnings-ID.

> [!IMPORTANT]
> Endast e-postmeddelanden i verifierade domäner för klient organisationen synkroniseras med Azure AD. Varje Azure AD-klient har en eller flera verifierade domäner som du har beprövat ägarskap för och som är unikt kopplade till klient organisationen.
>
> Mer information finns i [lägga till och verifiera ett anpassat domän namn i Azure AD][verify-domain].

Mer information finns i [Välj rätt autentiseringsmetod för din Azure AD hybrid Identity-lösning][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Aktivera användar inloggning med en e-postadress

När användare med attributet *proxyAddresses* som används synkroniseras till Azure AD med hjälp av Azure AD Connect, måste du aktivera funktionen för användare för att logga in med e-post som ett alternativt inloggnings-ID för din klient. Den här funktionen visar att Azure AD-inloggnings servrarna inte bara kontrollerar inloggnings namnet mot UPN-värden, utan även mot *proxyAddresses* -värden för e-postadressen.

Under för hands versionen kan du för närvarande endast aktivera inloggning med e-post som en alternativ inloggnings-ID-funktion med hjälp av PowerShell. Du måste ha *klient administratörs* behörighet för att utföra följande steg:

1. Öppna en PowerShell-session som administratör och installera sedan *AzureADPreview* -modulen med cmdleten [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Om du uppmanas att göra det väljer du **Y** för att installera NuGet eller installera från en obetrodd lagrings plats.

1. Logga in på din Azure AD-klient som *innehavaradministratör* med cmdleten [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Kommandot returnerar information om ditt konto, din miljö och klient-ID.

1. Kontrol lera om *HomeRealmDiscoveryPolicy* -principen redan finns i din klient med hjälp av cmdleten [Get-AzureADPolicy][Get-AzureADPolicy] på följande sätt:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Om det inte finns någon princip som är konfigurerad för tillfället returnerar kommandot ingenting. Om en princip returneras hoppar du över det här steget och går vidare till nästa steg för att uppdatera en befintlig princip.

    Om du vill lägga till *HomeRealmDiscoveryPolicy* -principen till klienten använder du cmdleten [New-AzureADPolicy][New-AzureADPolicy] och anger attributet *AlternateIdLogin* till *"Enabled": true* , vilket visas i följande exempel:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    När principen har skapats returnerar kommandot princip-ID: t, som visas i följande exempel på utdata:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Om det redan finns en konfigurerad princip kontrollerar du om attributet *AlternateIdLogin*   har Aktiver ATS, som du ser i följande exempel princip utdata:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Om principen finns men attributet *AlternateIdLogin* som inte finns eller är aktiverat, eller om det finns andra attribut på den princip som du vill behålla, uppdaterar du den befintliga principen med cmdleten [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > När du uppdaterar principen kontrollerar du att du inkluderar alla gamla inställningar och det nya *AlternateIdLogin* -attributet.

    I följande exempel lägger till attributet *AlternateIdLogin* och bevarar det *AllowCloudPasswordValidation* -attribut som redan har angetts:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Bekräfta att den uppdaterade principen visar ändringarna och att attributet *AlternateIdLogin* nu är aktiverat:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

När principen har tillämpats kan det ta upp till en timme att sprida och användare kan logga in med sina alternativa inloggnings-ID.

## <a name="test-user-sign-in-with-email"></a>Testa användar inloggning med e-post

Om du vill testa att användarna kan logga in med e-post kan du bläddra till [https://myprofile.microsoft.com][my-profile] och logga in med ett användar konto baserat på deras e-postadress, till exempel `balas@fabrikam.com` , inte deras UPN, till exempel `balas@contoso.com` . Inloggnings upplevelsen bör se ut och kännas likadan som med en UPN-baserad inloggnings händelse.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Aktivera mellanlagrad distribution för att testa användar inloggning med en e-postadress  

Med [mellanlagrad][staged-rollout] distribution kan klient organisations administratörer aktivera funktioner för vissa grupper. Det rekommenderas att innehavaradministratörer använder mellanlagrad distribution för att testa användar inloggning med en e-postadress. När administratörer är redo att distribuera den här funktionen till hela klienten bör de använda en princip för identifiering av start sfär.  


Du måste ha *klient administratörs* behörighet för att utföra följande steg:

1. Öppna en PowerShell-session som administratör och installera sedan *AzureADPreview* -modulen med cmdleten [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Om du uppmanas att göra det väljer du **Y** för att installera NuGet eller installera från en obetrodd lagrings plats.

2. Logga in på din Azure AD-klient som *innehavaradministratör* med cmdleten [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Kommandot returnerar information om ditt konto, din miljö och klient-ID.

3. Visa en lista över alla befintliga stegvisa distributions principer med följande cmdlet:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Om det inte finns några befintliga stegvisa distributions principer för den här funktionen skapar du en ny stegvis distributions princip och noterar princip-ID:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. Hitta directoryObject-ID: t för gruppen som ska läggas till i den stegvisa distributions principen. Observera värdet som returneras för parametern *ID* , eftersom det kommer att användas i nästa steg.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Lägg till gruppen i principen för stegvis distribution enligt följande exempel. Ersätt värdet i parametern *-ID* med det värde som returnerades för princip-ID i steg 4 och Ersätt värdet i parametern *-RefObjectId* med det *ID* som anges i steg 5. Det kan ta upp till en timme innan användarna i gruppen kan använda sina proxyservrar för att logga in.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID&quot; -RefObjectId &quot;GROUP_OBJECT_ID"
   ```
   
För nya medlemmar som läggs till i gruppen kan det ta upp till 24 timmar innan de kan använda sina proxyadresser för att logga in.

### <a name="removing-groups"></a>Tar bort grupper

Om du vill ta bort en grupp från en stegvis distributions princip kör du följande kommando:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID&quot; -ObjectId &quot;GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Ta bort principer

Om du vill ta bort en princip för stegvis distribution måste du först inaktivera principen och sedan ta bort den från systemet:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Felsöka

Om användarna har problem med inloggnings händelser med hjälp av e-postadressen kan du läsa följande fel söknings steg:

1. Se till att användar kontot har sina e-postadresser för attributet *proxyAddresses* i AD DS-miljön för lokal.
1. Kontrol lera att Azure AD Connect har kon figurer ATS och synkroniserar användar konton från AD DS-lokal i Azure AD.
1. Bekräfta att Azure AD *HomeRealmDiscoveryPolicy* -principen har attributet *AlternateIdLogin* inställt på *"Enabled": true*:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hybrid identitet, till exempel Azure AD App proxy eller Azure AD Domain Services, se [Azure AD hybrid identitet för åtkomst och hantering av lokal arbets belastningar][hybrid-overview].

För ytterligare information om hybrid identitets åtgärder, se [hur synkronisering av lösen ord för hash-synkronisering][phs-overview] eller [vidarekoppling][pta-overview] fungerar.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
