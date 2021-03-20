---
title: 'Azure AD Connect: ADSyncConfig PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller referensinformation för PowerShell-modulen ADSyncConfig. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85850967"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell-referens
Följande dokumentation innehåller referensinformation för PowerShell-modulen ADSyncConfig. psm1 som ingår i Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar det konto namn och den domän som har kon figurer ATS i varje AD-anslutning

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>BESKRIVNING
Den här funktionen använder cmdleten "Get-ADSyncConnector" som finns i AAD Connect för att hämta från anslutnings parametrar en tabell som visar ett eller flera AD Connector-konton.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SAMMANFATTNING
Hämtar AD-objekt med behörighets arv inaktiverade

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Söker i AD från SearchBase-parametern och returnerar alla objekt, filtrerade efter ObjectClass-parameter, som har ACL-arvet för tillfället inaktiverat.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
Hitta objekt med inaktiverat arv i Contoso-domänen (som standard returnerar endast organizationalUnit-objekt)
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>EXEMPEL 2
Hitta User-objekt med inaktiverat arv i Contoso-domänen
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>EXEMPEL 3
Hitta alla typer av objekt med inaktiverat arv i en ORGANISATIONSENHET
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETRAR

#### <a name="-searchbase"></a>-SearchBase
SearchBase för den LDAP-fråga som kan vara en AD-domän DistinguishedName eller ett fullständigt domän namn

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Klassen för de objekt som ska genomsökas och kan vara "*" (för alla objekt klasser), "User", "Group", "container" osv. Som standard söker den här funktionen efter objekt klassen "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för grundläggande Läs behörighet.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncBasicReadPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Läs egenskaps åtkomst för alla attribut för alla underordnade dator objekt
2.
Läs egenskaps åtkomst för alla attribut för alla underordnade enhets objekt
3.
Läs egenskaps åtkomst för alla attribut för alla underordnade foreignsecurityprincipal-objekt
5.
Läs egenskaps åtkomst för alla attribut för alla underordnade användar objekt
6.
Läs egenskaps åtkomst för alla attribut för alla underordnade inetOrgPerson-objekt
7.
Läs egenskaps åtkomst för alla attribut för alla underordnade grupp objekt
8.
Läs egenskaps åtkomst för alla attribut för alla underordnade kontakt objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för Exchange Hybrid-funktionen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncExchangeHybridPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Läs-och Skriv behörighet för alla attribut för alla underordnade användar objekt
2.
Läs-och Skriv behörighet för alla attribut för alla underordnade inetOrgPerson-objekt
3.
Läs-och Skriv behörighet för alla attribut för alla underordnade grupp objekt
4.
Läs-och Skriv behörighet för alla attribut för alla underordnade kontakt objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera funktionen för offentlig Exchange-e-postmapp i Active Directory skog och domän.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncExchangeMailPublicFolderPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Läs egenskaps åtkomst för alla attribut för alla underordnade PublicFolder-objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för mS-DS-ConsistencyGuid-funktionen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncMsDsConsistencyGuidPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Läs-och skriv egenskaps åtkomst på mS-DS-ConsistencyGuid-attribut för alla underordnade användar objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för synkronisering av lösen ord för hash.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncPasswordHashSyncPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Katalog ändringar replikeras
2.
Alla katalog ändringar replikeras

Dessa behörigheter ges till alla domäner i skogen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för att skriva tillbaka lösen ord från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncPasswordWritebackPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Återställ lösen ord för underordnade användar objekt
2.
Skriv egenskaps åtkomst på attributet lockoutTime för alla underordnade användar objekt
3.
Skriv egenskaps åtkomst på attributet pwdLastSet för alla underordnade användar objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Öka behörigheter för ett AD-objekt som inte i övrigt ingår i någon AD-skyddad säkerhets grupp.
Ett typiskt exempel är AD Connect-kontot (MSOL) som skapas av AAD Connect automatiskt.
Det här kontot har replikerat behörigheter för alla domäner, men det kan vara lätt att avslöja eftersom det inte är skyddat.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncRestrictedPermissions drar av behörigheter säkerhets det angivna kontot.
Att öka behörigheter omfattar följande steg:
1. Inaktivera arv för det angivna objektet
2. Ta bort alla ACE: er för det angivna objektet, förutom åtkomst till sig själv.
Vi vill behålla standard behörigheterna när det kommer till dig själv.
3. Tilldela följande behörigheter:

   | Typ | Name | Access | Gäller för |
   |------|------|--------|------------|
   | Tillåt | SYSTEM | Fullständig kontroll | Det här objektet |
   | Tillåt | Företagsadministratörer | Fullständig kontroll | Det här objektet |
   | Tillåt | Domänadministratörer | Fullständig kontroll | Det här objektet | 
   | Tillåt | Administratörer | Fullständig kontroll | Det här objektet |
   | Tillåt | Företagets domänkontrollanter | Lista innehåll <br> Läs alla egenskaper <br> Läs behörigheter | Det här objektet |
   | Tillåt | Autentiserade användare | Lista innehåll <br> Läs alla egenskaper <br> Läs behörigheter | Det här objektet |

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto vars behörigheter måste höjas.
Detta är vanligt vis det MSOL_nnnnnnnnnn konto eller ett anpassat domän konto som har kon figurer ATS i din AD-anslutning.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
Administratörs behörighet som har de behörigheter som krävs för att begränsa behörigheterna för ADConnectorAccountDN-kontot. Detta är vanligt vis företags-eller domän administratören. Använd det fullständigt kvalificerade domän namnet för administratörs kontot för att undvika konto uppslags problem.
Exempel: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
När DisableCredentialValidation används kontrollerar inte funktionen om autentiseringsuppgifterna som anges i Credential är giltiga i AD och om det angivna kontot har behörighet att begränsa behörigheterna för ADConnectorAccountDN-kontot.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Initiera din Active Directory skog och domän för tillbakaskrivning av grupp från Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktionen Set-ADSyncUnifiedGroupWritebackPermissions ger nödvändig behörighet till kontot för AD-synkronisering, som innehåller följande:
1.
Allmän läsning/skrivning, ta bort, ta bort träd och Create\Delete-underordnad för alla grupp objekt typer och under objekt

Dessa behörigheter tillämpas på alla domäner i skogen.
Alternativt kan du ange en DistinguishedName i ADobjectDN-parametern för att ange de här behörigheterna enbart för det AD-objektet (inklusive arv till under objekt).
I det här fallet är ADobjectDN det unika namnet för den behållare som du vill länka med GroupWriteback-funktionen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>EXEMPEL 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>EXEMPEL 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Namnet på det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domänen för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName för det Active Directory konto som ska användas av Azure AD Connect Sync för att hantera objekt i katalogen.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName för målets AD-objekt för att ange behörigheter (valfritt)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Valfri parameter för att ange om AdminSDHolder-behållaren inte ska uppdateras med dessa behörigheter

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Visar vad som skulle hända om cmdleten kördes.
Cmdleten körs inte.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Uppmanar dig att bekräfta innan du kör cmdleten.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SAMMANFATTNING
Visar behörigheter för ett angivet AD-objekt.

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Den här funktionen returnerar alla AD-behörigheter som för närvarande har angetts för ett angivet AD-objekt i parametern-ADobjectDN.
ADobjectDN måste anges i ett DistinguishedName-format.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN-Beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
