---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-referens | Microsoft Docs'
description: Det här dokumentet innehåller referensinformation för PowerShell-modulen ADConnectivityTools. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070810"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell-referens

Följande dokumentation innehåller referensinformation för PowerShell-modulen ADConnectivityTools. psm1 som ingår i Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar lokala DNS-problem.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör tester för lokal DNS-anslutning.
För att kunna konfigurera Active Directory-anslutningen måste användaren ha både namnet resolutionthe för den skog som de försöker ansluta till samt i domän kontrol Lanterna som är kopplade till den här skogen.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som testet ska testas mot.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Domänkontrollanter

Ange domänkontrollanter att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av den här diagnostiken i form av en PSObject.
Krävs inte vid manuell interaktion med det här verktyget.

```yml
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

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>SAMMANFATTNING

Anger om en angiven skog finns.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Frågar en DNS-Server efter IP-adresserna som är associerade med en skog.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som testet ska testas mot.

```yml
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

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SAMMANFATTNING

Verifierar AD-skogens funktions nivå.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kontrollerar att AD-skogens funktions nivå är lika med eller mer än en specifik MinAdForestVersion (WindowsServer2003).
Konto (domän \ användar namn) och lösen ord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPEL 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Mål skog.
Standardvärdet är den aktuella inloggade användarens skog.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Target ForestFQDN-objekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SAMMANFATTNING

Identifierar problem med lokal nätverks anslutning.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör tester för lokal nätverks anslutning.

För de lokala nätverks testerna måste AAD Connect kunna kommunicera med de namngivna domän kontrol Lanterna på portarna 53 (DNS), 88 (Kerberos) och 389 (LDAP). de flesta organisationer kör DNS på sina domänkontrollanter, vilket är orsaken till att det här testet för närvarande är integrerat.
Port 53 bör hoppas över om en annan DNS-server har angetts.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-dcs"></a>– Domänkontrollanter

Ange domänkontrollanter att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Om användaren inte använder DNS-tjänster som tillhandahålls av AD-platsen/inloggnings-DC, kanske de vill hoppa över att kontrol lera port 53.
Användaren måste fortfarande kunna lösa _.ldap._tcp.\<forestfqdn\>
för att konfigurationen av Active Directory Connector ska lyckas.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av den här diagnostiken i form av en PSObject.
Krävs inte vid manuell interaktion med det här verktyget.

```yml
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

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SAMMANFATTNING

Anger om en angiven skog och dess associerade domänkontrollanter kan kontaktas.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör ping-test (om en dator kan komma åt en måldator via nätverket och/eller Internet)

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som testet ska testas mot.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Domänkontrollanter

Ange domänkontrollanter att testa mot.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SAMMANFATTNING

Verifiera att domänerna i den hämtade skogens FQDN kan kommas åt

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kontrol lera att alla domäner i den hämtade skogens FQDN kan kommas åt genom att försöka hämta DomainGuid och DomainDN.
Konto (domän \ användar namn) och lösen ord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPEL 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Mål skog.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>– ForestFQDN

Target ForestFQDN-objekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SAMMANFATTNING

Kontrollerar om en användare har autentiseringsuppgifter för företags administratör.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Söker om den angivna användaren har autentiseringsuppgifter för företags administratör.
Konto (domän \ användar namn) och lösen ord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar en DomainFQDN från en kombination av konto och lösen ord.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker hämta ett domainFQDN-objekt av tillhandahållna autentiseringsuppgifter.
Om domainFQDN är giltig returneras en DomainFQDNName eller RootDomainName, beroende på användarens val.
Konto (domän \ användar namn) och lösen ord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Önskad typ av data som ska hämtas.
För närvarande begränsad till "DomainFQDNName" eller "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Tilläggs parameter som används av Start-NetworkConnectivityDiagnosisTools funktion

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SAMMANFATTNING

Hämtar en ForestFQDN från en kombination av konto och lösen ord.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Försöker erhålla en ForestFQDN av de tillhandahållna autentiseringsuppgifterna.
Konto (domän \ användar namn) och lösen ord kan begäras.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Mål skog. Standardvärdet är domänen för den inloggade användaren.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funktionen använder autentiseringsuppgifterna för den användare som för närvarande är inloggad på datorn, i stället för att begära anpassade autentiseringsuppgifter från användaren.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SAMMANFATTNING

Main-funktion.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör alla tillgängliga mekanismer som verifierar att autentiseringsuppgifter för AD är giltiga.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Mål skog.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

För anpassade installationer: flagga som är $True om användaren valde "Skapa nytt AD-konto" i fönstret AD-skogs konto i AADConnect-guiden.
$False om användaren valde "Använd befintligt AD-konto".
För Express-installationer: värdet för den här variabeln måste vara $True för Express-installationer.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parameter som fyller i fältet username när användarens autentiseringsuppgifter begärs.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SAMMANFATTNING

Huvud funktion för test av nätverks anslutning.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESKRIVNING

Kör tester för lokal nätverks anslutning.

### <a name="examples"></a>EXEMPEL

#### <a name="example-1"></a>EXEMPEL 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPEL 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRAR

#### <a name="-forest"></a>-Skog

Anger namnet på den skog som ska testas.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Autentiseringsuppgifter

Användar namnet och lösen ordet för den användare som kör testet.
Den kräver samma behörighets nivå som krävs för att köra guiden Azure AD Connect.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Anger platsen för en loggfil som ska innehålla resultatet av den här funktionen.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>– Domänkontrollanter

Ange domänkontrollanter att testa mot.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flagga som gör det möjligt att visa ett meddelande om syftet med den här funktionen.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Returnerar resultatet av den här diagnostiken i form av en PSObject.
Det är inte nödvändigt att ange vid manuell interaktion med det här verktyget.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Anger om de autentiseringsuppgifter som användaren angav är giltiga.
Det är inte nödvändigt att ange vid manuell interaktion med det här verktyget.

```yml
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
