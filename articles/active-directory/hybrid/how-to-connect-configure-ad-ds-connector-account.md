---
title: 'Azure AD Connect: Konfigurera konto behörigheter för AD DS-anslutning | Microsoft Docs'
description: Det här dokumentet beskriver hur du konfigurerar AD DS-anslutningsprogrammet med den nya ADSyncConfig PowerShell-modulen
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349947"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurera behörigheter för AD DS Connector 

PowerShell-modulen med namnet [ADSyncConfig. psm1](reference-connect-adsyncconfig.md) introducerades med build 1.1.880.0 (släpptes i augusti 2018) som innehåller en samling cmdlets som hjälper dig att konfigurera rätt Active Directory-behörigheter för din Azure AD Connect-distribution. 

## <a name="overview"></a>Översikt 
Följande PowerShell-cmdletar kan användas för att konfigurera Active Directory behörigheter för AD DS-anslutnings kontot, för varje funktion som du väljer att aktivera i Azure AD Connect. För att förhindra eventuella problem bör du förbereda Active Directory behörigheter i förväg när du vill installera Azure AD Connect med ett anpassat domän konto för att ansluta till din skog. Den här ADSyncConfig-modulen kan också användas för att konfigurera behörigheter när Azure AD Connect har distribuerats.

![Översikt över AD DS-konto](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

För Azure AD Connect Express installation skapas ett automatiskt genererat konto (MSOL_nnnnnnnnnn) i Active Directory med alla nödvändiga behörigheter, så du behöver inte använda den här ADSyncConfig-modulen om du inte har blockerat behörighets arv för organisationsenheter eller särskilda Active Directory-objekt som du vill synkronisera till Azure AD. 
 
### <a name="permissions-summary"></a>Sammanfattning av behörigheter 
Följande tabell innehåller en sammanfattning av de behörigheter som krävs för AD-objekt: 

| Funktion | Behörigheter |
| --- | --- |
| ms-DS-ConsistencyGuid-funktion |Läs-och Skriv behörighet till attributet ms-DS-ConsistencyGuid dokumenterat i [design koncept – med MS-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Hash-synkronisering av lösen ord |<li>Replikera katalog ändringar</li>  <li>Replikera katalog ändringar alla |
| Exchange hybrid distribution |Läs-och Skriv behörighet till de attribut som dokumenteras i [Exchange hybrid tillbakaskrivning](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Offentlig Exchange-e-postmapp |Läs behörighet till attributen som dokumenteras i den [offentliga Exchange-e-postmappen](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för offentliga mappar. | 
| Tillbakaskrivning av lösenord |Läs-och Skriv behörighet till de attribut som dokumenteras i [komma igång med lösen ords hantering](../authentication/tutorial-enable-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Läs-och skriv behörigheter till enhets objekt och behållare som dokumenteras i [tillbakaskrivning av enhet](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Läsa, skapa, uppdatera och ta bort grupp objekt för synkroniserade **Office 365-grupper**.|

## <a name="using-the-adsyncconfig-powershell-module"></a>Använda ADSyncConfig PowerShell-modulen 
ADSyncConfig-modulen kräver [verktyg för fjärrserveradministration (RSAT) för AD DS](/windows-server/remote/remote-server-administration-tools) eftersom den är beroende av AD DS PowerShell-modulen och verktyg. Om du vill installera RSAT för AD DS öppnar du ett Windows PowerShell-fönster med "kör som administratör" och kör: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurera](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Du kan också kopiera filen **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** till en domänkontrollant som redan har RSAT för AD DS installerad och använda den här PowerShell-modulen därifrån.

För att börja använda ADSyncConfig måste du läsa in modulen i Windows PowerShell-fönstret: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Om du vill kontrol lera alla cmdletar som ingår i den här modulen kan du skriva:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Markera](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Varje cmdlet har samma parametrar för att mata in AD DS Connector-kontot och en AdminSDHolder-växel. Om du vill ange ditt AD DS-anslutningsfel kan du ange kontots namn och domän, eller bara kontots unika namn (DN).

Exempel:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Eller

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Se till att ersätta `<ADAccountName>` `<ADDomainName>` och `<ADAccountDN>` med lämpliga värden för din miljö.

Om du inte vill ändra behörigheter för AdminSDHolder-behållaren använder du växeln `-SkipAdminSdHolders` . 

Som standard försöker alla cmdlets för set-behörigheter att ange AD DS-behörigheter i roten för varje domän i skogen, vilket innebär att den användare som kör PowerShell-sessionen kräver domän administratörs behörighet på varje domän i skogen.  På grund av detta krav rekommenderar vi att du använder en företags administratör från skogs roten. Om din Azure AD Connect-distribution har flera AD DS-kopplingar måste du köra samma cmdlet på varje skog som har en AD DS-anslutning. 

Du kan också ange behörigheter för ett enskilt OU-eller AD DS-objekt med hjälp av parametern `-ADobjectDN` följt av DN för det mål objekt där du vill ange behörigheter. När du använder en mål-ADobjectDN, anger cmdleten bara behörigheter för det här objektet och inte i domän roten eller AdminSDHolder-behållaren. Den här parametern kan vara användbar när du har vissa organisationsenheter eller AD DS-objekt med behörighets arv inaktiverat (se hitta AD DS-objekt med behörighets arv inaktiverat) 

Undantag till dessa vanliga parametrar är den `Set-ADSyncRestrictedPermissions` cmdlet som används för att ange behörigheterna för själva AD DS-anslutningsprogrammet och `Set-ADSyncPasswordHashSyncPermissions` cmdleten, eftersom de behörigheter som krävs för Lösenordssynkronisering bara ställs in i domän roten, och denna cmdlet inkluderar därför inte `-ObjectDN` `-SkipAdminSdHolders` parametrarna eller.

### <a name="determine-your-ad-ds-connector-account"></a>Bestäm ditt AD DS Connector-konto 
Om Azure AD Connect redan är installerat och du vill kontrol lera vad är AD DS-anslutningsprogrammet som används av Azure AD Connect, kan du köra cmdleten: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Hitta AD DS-objekt med behörighets arv inaktiverade 
Om du vill kontrol lera om det finns något AD DS-objekt med behörighets arv inaktiverat kan du köra: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Som standard söker denna cmdlet bara efter organisationsenheter med inaktiverat arv, men du kan ange andra AD DS-objekt klasser i `-ObjectClass` parametern eller använda "*" för alla objekt klasser, enligt följande: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Visa AD DS-behörigheter för ett objekt 
Du kan använda cmdleten nedan om du vill visa en lista över behörigheter som för närvarande är inställda på ett Active Directory-objekt genom att ange dess DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurera behörigheter för AD DS Connector 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurera grundläggande Read-Only behörigheter 
Om du vill ange grundläggande Läs behörighet för AD DS Connector-kontot när du inte använder någon Azure AD Connect funktion kör du: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Denna cmdlet kommer att ange följande behörigheter: 
 

|Typ |Name |Access |Gäller för| 
|-----|-----|-----|-----|
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade enhets objekt| 
|Tillåt |AD DS-anslutnings konto|Läsa alla egenskaper |Objekt för underordnade InetOrgPerson| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade dator objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade foreignSecurityPrincipal-objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade grupp objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade användar objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade kontakt objekt| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurera MS-DS-konsekvens-GUID-behörigheter 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder attributet ms-DS-konsekvens-GUID som käll ankare (kallas även "Låt Azure hantera käll fäst punkten för mig") kör du: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

eller 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt|AD DS-anslutnings konto|Läsa/skriva egenskap|Underordnade användar objekt|

### <a name="permissions-for-password-hash-synchronization"></a>Behörigheter för lösen ords-hash-synkronisering 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder Lösenordssynkronisering, kör du: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


eller 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutnings konto |Katalog ändringar replikeras |Endast det här objektet (domän roten)| 
|Tillåt |AD DS-anslutnings konto |Alla katalog ändringar replikeras |Endast det här objektet (domän roten)| 
  
### <a name="permissions-for-password-writeback"></a>Behörigheter för tillbakaskrivning av lösen ord 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder tillbakaskrivning av lösen ord kör du: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutnings konto |Återställ lösenord |Underordnade användar objekt| 
|Tillåt |AD DS-anslutnings konto |LockoutTime för Skriv egenskap |Underordnade användar objekt| 
|Tillåt |AD DS-anslutnings konto |PwdLastSet för Skriv egenskap |Underordnade användar objekt| 

### <a name="permissions-for-group-writeback"></a>Behörigheter för tillbakaskrivning av grupp 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder grupp-tillbakaskrivning kör du: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
eller 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutnings konto |Allmän läsning/skrivning |Alla attribut för objekt typ grupp och under objekt| 
|Tillåt |AD DS-anslutnings konto |Skapa/ta bort underordnat objekt |Alla attribut för objekt typ grupp och under objekt| 
|Tillåt |AD DS-anslutnings konto |Ta bort/ta bort träd objekt|Alla attribut för objekt typ grupp och under objekt|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Behörigheter för Exchange hybrid distribution 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder Exchange hybrid distribution kör du: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter:  
 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutnings konto |Läsa/skriva alla egenskaper |Underordnade användar objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa/skriva alla egenskaper |Objekt för underordnade InetOrgPerson| 
|Tillåt |AD DS-anslutnings konto |Läsa/skriva alla egenskaper |Underordnade grupp objekt| 
|Tillåt |AD DS-anslutnings konto |Läsa/skriva alla egenskaper |Underordnade kontakt objekt| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Behörigheter för offentliga Exchange Mail-mappar (för hands version) 
Om du vill ange behörigheter för AD DS-anslutningsprogrammet när du använder funktionen för delade Exchange-mappar kör du: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutnings konto |Läsa alla egenskaper |Underordnade PublicFolder-objekt| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Begränsa behörigheter för AD DS-anslutnings kontot 
Det här PowerShell-skriptet höjer behörigheter för det AD Connector-konto som angetts som en parameter. Att öka behörigheter omfattar följande steg: 

- Inaktivera arv för det angivna objektet 
- Ta bort alla ACE: er för det aktuella objektet, förutom de åtkomst till sig själv som vi vill behålla standard behörigheterna när det kommer till dig själv. 
 
  Parametern-ADConnectorAccountDN är det AD-konto vars behörigheter måste höjas. Detta är vanligt vis det MSOL_nnnnnnnnnnnn domän konto som har kon figurer ATS i AD DS-anslutningen (se ta reda på ditt AD DS Connector-konto). Parametern-Credential är nödvändig för att ange det administratörs konto som har de behörigheter som krävs för att begränsa Active Directory behörigheter för målets AD-objekt. Detta är vanligt vis företags-eller domän administratören.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Exempel: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Name |Access |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |SYSTEM |Fullständig kontroll |Det här objektet 
|Tillåt |Företagsadministratörer |Fullständig kontroll |Det här objektet 
|Tillåt |Domänadministratörer |Fullständig kontroll |Det här objektet 
|Tillåt |Administratörer |Fullständig kontroll |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Lista innehåll |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Läs alla egenskaper |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Läs behörigheter |Det här objektet 
|Tillåt |Autentiserade användare |Lista innehåll |Det här objektet 
|Tillåt |Autentiserade användare |Läs alla egenskaper |Det här objektet 
|Tillåt |Autentiserade användare |Läs behörigheter |Det här objektet 

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabb installation](how-to-connect-install-express.md)
- [Anpassad installation](how-to-connect-install-custom.md)
- [Referens för ADSyncConfig](reference-connect-adsyncconfig.md)