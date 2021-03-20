---
title: 'Azure AD Connect: ADSyncTools PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller referensinformation för PowerShell-modulen ADSyncTools. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672276"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell-referens
Följande dokumentation innehåller referensinformation för PowerShell-modulen ADSyncTools. psm1 som ingår i Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Installera ADSyncTools PowerShell-modulen
Gör följande för att installera ADSyncTools PowerShell-modulen:

1.  Öppna Windows PowerShell med administratörs behörighet
2.  Skriv eller kopiera och klistra in följande: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Tryck på RETUR.
4.  Kontrol lera att modulen har installerats genom att ange eller kopiera och klistra in följande "
    ```powershell
    Get-module AdSyncTools
    ```
5.  Nu bör du se information om modulen.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Rensa mS-DS-ConsistencyGuid från AD-användare

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Rensa värdet i mS-DS-ConsistencyGuid för mål-AD-användaren

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Mål användare i AD att ange

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-database"></a>– Databas
{{Fill Database Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instans
{{Fill instance Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill Password Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill server-beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{{Fill UserName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Exportera ConsistencyGuid-rapport

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en ConsistencyGuid-rapport som baseras på en importerad CSV-fil från Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Använd alternativt inloggnings-ID (e-post)

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>– Utdata
Utdata-filnamn för CSV-och LOGGFILer

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Fill-värdnamn, beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SAMMANFATTNING
Hämta användare från AD

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar ett AD-objekt att göra: stöd för flera skogar

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Mål användare i AD för att ange ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta mS-DS-ConsistencyGuid från AD-användaren

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i mS-DS-ConsistencyGuid-attributet för mål-AD-användaren i GUID-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Mål användare i AD att ange

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SAMMANFATTNING
Hämta ObjectGuid från AD-användaren

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Returnerar värdet i attributet ObjectGUID för mål-AD-användaren i GUID-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Mål användare i AD att ange

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SAMMANFATTNING
Hämta historik för AAD Connect-körning

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktion som returnerar AAD Connect-körnings historik i XML-format

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>EXEMPEL 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-days"></a>-Dagar
{{Fill Days Description}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SAMMANFATTNING
Hämta användare med SourceAnchor ändrade fel

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Funktions frågor AAD Connect körnings historik och exporterar alla användare som rapporterar felet: "SourceAnchor-attributet har ändrats".

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
#Required Parameters
```

$sourcePath = Read-Host-prompt "Ange sökvägen till logg filen med fil namnet" # " \<Source_Path\> $outputPath = Read-Host-prompt" Ange din fil Sök väg med fil namnet "#" \<Out_Path\> "

 Get-ADSyncToolsUsersSourceAnchorChanged-sourcePath $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath-Beskrivning}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>– outputPath
{{Fill outputPath-Beskrivning}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SAMMANFATTNING
Importera ImmutableID från AAD

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Genererar en fil med alla Azure AD-synkroniserade användare som innehåller ImmutableID-värdet i GUID-format krav: MSOnline PowerShell-modul

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-output"></a>– Utdata
Utgående CSV-fil

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Hämta synkroniserade användare från Azure AD-pappers korgen

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

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-query"></a>– Fråga
{{Fill Query Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>– SqlConnection
{{Fill SqlConnection-Beskrivning}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMMANFATTNING
Skript för att ta bort utgångna certifikat från attributet UserCertificate

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Det här skriptet tar alla objekt från en mål organisations enhet i Active Directory domän-filtrerad efter objekt klass (användare/dator) och tar bort alla utgångna certifikat som finns i UserCertificate-attributet.
Som standard är det bara att säkerhetskopiera certifikat som gått ut till en fil och inte göra några ändringar i AD.
Om du använder-Auto$false tas eventuella utgångna certifikat som finns i attributet UserCertificate bort från AD efter att de har kopierats till filen.
Varje certifikat säkerhets kopie ras till ett avgränsat fil namn: ObjectClass_ObjectGUID_CertThumprint. cer. skriptet skapar även en loggfil i CSV-format som visar alla användare med certifikat som antingen är giltiga eller har upphört att gälla, inklusive den faktiska åtgärden som har vidtagits (hoppades över/exporter ATS/tagits bort).

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = användare, OU = Corp, DC = contoso, DC = com"-ObjectClass användare

#### <a name="example-2"></a>EXEMPEL 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = Computers, OU = Corp, DC = contoso, DC = com"-ObjectClass Computer-restarted på $false

### <a name="parameters"></a>PARAMETRAR

#### <a name="-targetou"></a>-TargetOU
Mål ORGANISATIONSENHET för sökning efter AD-objekt

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

#### <a name="-backuponly"></a>– Efter
Vid borttagning tas inga certifikat bort från AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Objekt klass filter

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SAMMANFATTNING
Kort beskrivning

### <a name="syntax"></a>SYNTAX

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Fill-värdnamn, beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMMANFATTNING
(ATT GÖRA) Återställer AD UserCertificate-attribut från en certifikat fil

### <a name="syntax"></a>SYNTAX

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMMANFATTNING
Ange mS-DS-ConsistencyGuid på AD-användare

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Ange ett värde i mS-DS-ConsistencyGuid-attributet för mål-AD-användaren

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-user"></a>-User
Mål användare i AD för att ange ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Värde
ImmutableId (byte mat ris, GUID, GUID-sträng eller Base64-sträng)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SAMMANFATTNING
{{Fyll i sammanfattningen}}

### <a name="syntax"></a>SYNTAX

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>BESKRIVNING
{{Fyll i beskrivningen}}

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>Exempel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Lägg till exempel beskrivning här}}

### <a name="parameters"></a>PARAMETRAR

#### <a name="-hostname"></a>-hostName
{{Fill-värdnamn, beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
{{Fill port Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SAMMANFATTNING
Skapar en spårnings fil från och AD-import-steget

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Spår alla LDAP-frågor om en AAD Connect AD-import körs från en angiven kontroll punkt för AD-vattenstämpel (partitions-cookie). Skapar en spårnings fil '. \ ADimportTrace_yyyyMMddHHmmss. log ' i den aktuella mappen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML-Beskrivning}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-DC
XML-fil för AD Connector-export

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Måldomänkontrollanten

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Skogs rotens DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typer av AD-objekt som ska spåras \> * = alla objekt typer

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

#### <a name="-adwatermark"></a>-ADwatermark
Om det redan körs som domän administratör behöver du inte ange AD-autentiseringsuppgifter.
Manuell indata från vattenstämpel, i stället för XML-fil, t. ex. $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SAMMANFATTNING
Kort beskrivning

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Lång beskrivning

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>EXEMPEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-context"></a>– Kontext
Beskrivning av param1-hjälp

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

#### <a name="-server"></a>-Server
Beskrivning av param2-hjälp

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Beskrivning av param2-hjälp

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Beskrivning av param2-hjälp

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Denna cmdlet har stöd för parametrarna -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction och -WarningVariable.
Mer information finns i about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMMANFATTNING
Uppdaterar användare med den nya ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING
Uppdaterar användare med det nya ConsistencyGuid-värdet (ImmutableId) som tas från ConsistencyGuid-rapporten den här funktionen stöder växeln WhatIf switch: ConsistencyGuid-rapporten måste importeras med tab Demiliter

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>EXEMPEL 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-distinguishedname"></a>– DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Åtgärd
Action

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>– Utdata
Utdata-filnamn för LOGGFILer

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
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
