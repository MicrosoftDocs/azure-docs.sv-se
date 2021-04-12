---
title: PowerShell V2-exempel för hantering av grupper – Azure AD | Microsoft Docs
description: Den här sidan innehåller PowerShell-exempel som hjälper dig att hantera dina grupper i Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, grupper, grupp hantering
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860651"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory version 2-cmdletar för grupp hantering

> [!div class="op_single_selector"]
> - [Azure-portalen](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Den här artikeln innehåller exempel på hur du använder PowerShell för att hantera grupper i Azure Active Directory (Azure AD).  Du får också information om hur du konfigurerar med Azure AD PowerShell-modulen. Först måste du [Ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Använd följande kommandon för att installera Azure AD PowerShell-modulen:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Kontrol lera att modulen är redo att användas genom att använda följande kommando:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Nu kan du börja använda cmdlet: arna i modulen. En fullständig beskrivning av cmdletarna i Azure AD-modulen finns i referens dokumentationen för online för [Azure Active Directory PowerShell version 2](/powershell/azure/active-directory/install-adv2).

> [!NOTE]
> Azure AD PowerShell-cmdletarna fungerar inte med den nya PowerShell 7 eftersom den baseras på .net Core. Vi är medvetna om och det här håller på att bli uppdaterad. Från och med nu rekommenderar vi att du använder Windows PowerShell 5. x-modulen som ska användas för Azure AD PowerShell-åtgärder. 


## <a name="connect-to-the-directory"></a>Anslut till katalogen

Innan du kan börja hantera grupper med Azure AD PowerShell-cmdlets måste du ansluta PowerShell-sessionen till den katalog som du vill hantera. Ange följande kommando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Cmdlet: en efterfrågar de autentiseringsuppgifter som du vill använda för att få åtkomst till din katalog. I det här exemplet använder vi karen@drumkit.onmicrosoft.com för att få åtkomst till demonstrations katalogen. Cmdleten returnerar en bekräftelse för att visa att sessionen är ansluten till din katalog:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Nu kan du börja använda AzureAD-cmdletar för att hantera grupper i din katalog.

## <a name="retrieve-groups"></a>Hämta grupper

Använd Get-AzureADGroups-cmdleten om du vill hämta befintliga grupper från katalogen. 

Om du vill hämta alla grupper i katalogen använder du cmdleten utan parametrar:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Cmdleten returnerar alla grupper i den anslutna katalogen.

Du kan använda-objectID-parametern för att hämta en enskild grupp för vilken du anger gruppens objectID:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Cmdleten returnerar nu gruppen vars objectID matchar värdet för den parameter som du angav:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Du kan söka efter en speciell grupp med parametern-filter. Den här parametern använder en ODATA filter-sats och returnerar alla grupper som matchar filtret, som i följande exempel:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Azure AD PowerShell-cmdlets implementerar OData-frågan standard. Mer information finns i **$filter** i [OData system Query-alternativ med OData-slutpunkten](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Skapa grupper

Använd New-AzureADGroup-cmdleten om du vill skapa en ny grupp i din katalog. Denna cmdlet skapar en ny säkerhets grupp med namnet "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Uppdaterings grupper

Använd Set-AzureADGroup-cmdleten om du vill uppdatera en befintlig grupp. I det här exemplet ändrar vi egenskapen DisplayName för gruppen "Intune-administratörer". Först ska vi hitta gruppen med hjälp av Get-AzureADGroup-cmdlet och filtrera med attributet DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Sedan ändrar vi egenskapen Description till det nya värdet "Intune enhets administratörer":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Om vi nu hittar gruppen igen, ser vi att egenskapen Description har uppdaterats så att den motsvarar det nya värdet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Ta bort grupper

Om du vill ta bort grupper från katalogen använder du Remove-AzureADGroup cmdleten på följande sätt:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Hantera gruppmedlemskap

### <a name="add-members"></a>Lägg till medlemmar

Använd Add-AzureADGroupMember-cmdleten om du vill lägga till nya medlemmar i en grupp. Detta kommando lägger till en medlem i gruppen Intune-administratörer som vi använde i föregående exempel:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametern-ObjectId är det ObjectID för gruppen som vi vill lägga till en medlem i och parametern-RefObjectId är ObjectID för den användare som vi vill lägga till som en medlem i gruppen.

### <a name="get-members"></a>Hämta medlemmar

Om du vill hämta befintliga medlemmar i en grupp använder du Get-AzureADGroupMember cmdlet, som i det här exemplet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Ta bort medlemmar

Om du vill ta bort den medlem som vi tidigare lade till i gruppen använder du Remove-AzureADGroupMember-cmdleten som visas här:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verifiera medlemmar

Om du vill verifiera grupp medlemskapet för en användare använder du cmdleten Select-AzureADGroupIdsUserIsMemberOf. Den här cmdleten tar som parameter till ObjectId för den användare som ska kontrol lera grupp medlemskapet, och en lista över grupper för vilka du vill kontrol lera medlemskap. Listan över grupper måste anges i form av en komplex variabel av typen "Microsoft. Open. AzureAD. Model. GroupIdsForMembershipCheck", så vi måste först skapa en variabel med den typen:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Nu ger vi värden för groupIds att checka in attributet "GroupIds" för den här komplexa variabeln:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Om vi nu vill kontrol lera grupp medlemskapet för en användare med ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea mot grupperna i $g bör vi använda:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Det returnerade värdet är en lista över grupper som den här användaren är medlem i. Du kan också använda den här metoden för att kontrol lera kontakter, grupper eller tjänst huvud namn för en specifik lista med grupper, med Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf eller Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Inaktivera grupp skapande av användare

Du kan förhindra att användare som inte är administratörer kan skapa säkerhets grupper. Standard beteendet i Microsoft Online Directory Services (MSODS) är att tillåta användare som inte är administratörer att skapa grupper, oavsett om SSGM (Self-Service Group Management) också är aktiverat eller inte. Inställningen SSGM styr bara beteendet i åtkomst panelen för Mina appar.

Så här inaktiverar du grupp skapande för icke-administratörer:

1. Verifiera att användare som inte är administratörer får skapa grupper:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Om den returneras `UsersPermissionToCreateGroupsEnabled : True` kan användare som inte är administratörer skapa grupper. Så här inaktiverar du den här funktionen:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Hantera ägare av grupper

Använd Add-AzureADGroupOwner-cmdleten om du vill lägga till ägare i en grupp:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametern-ObjectId är det ObjectID för gruppen dit vi vill lägga till en ägare, och RefObjectId är ObjectID för den användare eller det tjänst huvud namn som vi vill lägga till som ägare till gruppen.

Om du vill hämta ägare av en grupp använder du Get-AzureADGroupOwner-cmdlet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Cmdleten returnerar listan över ägare (användare och tjänstens huvud namn) för den angivna gruppen:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Använd Remove-AzureADGroupOwner-cmdleten om du vill ta bort en ägare från en grupp:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Reserverade alias

När en grupp skapas kan slutanvändaren ange ett e-smek namn eller alias som ska användas som en del av gruppens e-postadress för vissa slut punkter. Grupper med följande e-postalias med hög privilegier kan bara skapas av en global Azure AD-administratör. 
  
* missbruk
* administratör
* administratör
* hostmaster
* majordomo
* Master
* skogen
* skydda
* security
* SSL-admin
* uppmana

## <a name="group-writeback-to-on-premises-preview"></a>Tillbakaskrivning av grupp till lokal (för hands version)

Idag hanteras många grupper fortfarande i lokala Active Directory. Om du vill besvara begär Anden om synkronisering av moln grupper tillbaka till lokala, kan du nu använda funktionen tillbakaskrivning i Microsoft 365 grupper för Azure AD för för hands version.

Microsoft 365 grupper skapas och hanteras i molnet. Med funktionen tillbakaskrivning kan du skriva tillbaka Microsoft 365 grupper som distributions grupper till en Active Directory skog med Exchange installerat. Användare med lokala Exchange-postlådor kan sedan skicka och ta emot e-postmeddelanden från dessa grupper. Funktionen för tillbakaskrivning av grupp stöder inte Azure AD-säkerhetsgrupper eller distributions grupper.

Mer information finns i dokumentationen för [tjänsten Azure AD Connect Sync](../hybrid/how-to-connect-syncservice-features.md).

Tillbakaskrivning av Microsoft 365s grupp är en offentlig förhands gransknings funktion i Azure Active Directory (Azure AD) och är tillgänglig med en betald Azure AD-licens plan. För lite juridisk information om för hands versionerna, se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="next-steps"></a>Nästa steg

Du kan hitta mer Azure Active Directory PowerShell-dokumentation på [Azure Active Directory-cmdletar](/powershell/azure/active-directory/install-adv2).

* [Hantera åtkomst till resurser med Azure Active Directory grupper](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
