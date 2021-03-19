---
title: Hantera Azure blockchain service Consortium-medlemmar – PowerShell
description: Lär dig hur du hanterar Azure blockchain service Consortium-medlemmar med hjälp av Azure PowerShell.
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85211348"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Hantera konsortiums medlemmar i Azure blockchain-tjänsten med hjälp av PowerShell

Du kan använda PowerShell för att hantera blockchain Consortium-medlemmar för din Azure blockchain-tjänst. Medlemmar som har administratörs behörighet kan bjuda in, lägga till, ta bort och ändra roller för alla deltagare i blockchain-konsortiet. Medlemmar som har användar behörighet kan visa alla deltagare i blockchain-konsortiet och ändra deras medlems visnings namn.

## <a name="prerequisites"></a>Förutsättningar

* Skapa en blockchain-medlem med hjälp av [Azure Portal](create-member.md).
* Mer information om konsortier, medlemmar och noder finns i [Azure blockchain service Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Du kan också öppna Cloud Shell på en separat flik i webbläsaren genom att gå till [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och välj **RETUR** för att köra den.

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Installera Microsoft.AzureBlockchainService.ConsortiumManagement.PS-paketet från PowerShell-galleriet.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ange alternativ för information

Du kan få mer information när du kör cmdletarna genom att ange variabeln för informations inställningar. *$InformationPreference* anges som standard till *SilentlyContinue*.

Om du vill ha mer utförlig information från cmdletar anger du inställningen i PowerShell på följande sätt:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Upprätta en Web3-anslutning

Om du vill hantera konsortiets medlemmar upprättar du en Web3-anslutning till din blockchain service member-slutpunkt. Du kan använda det här skriptet för att ange globala variabler för att anropa hanterings-cmdletarna för konsortiet.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Ersätt *\<Member account password\>* med det medlems konto lösen ord som du använde när du skapade medlemmen.

Hitta de andra värdena i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din standard **översikts** sida för blockchain-tjänst medlemmar.

    ![Medlems översikt](./media/manage-consortium-powershell/member-overview.png)

    Ersätt *\<Member account\>* och *\<RootContract address\>* med värdena från portalen.

1. För slut punkts adress väljer du **Transaction** Nodes och väljer sedan **noden standard transaktion**. Standardnoden har samma namn som blockchain-medlemmen.
1. Välj **anslutnings strängar**.

    ![Anslutningssträngar](./media/manage-consortium-powershell/connection-strings.png)

    Ersätt *\<Endpoint address\>* med värdet från **https (åtkomst nyckel 1)** eller **https (åtkomst nyckel 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Hantera nätverket och smarta kontrakt

Använd cmdletarna Network och smart-kontrakt för att upprätta en anslutning till blockchain-slutpunktens smarta kontrakt som är ansvariga för hantering av konsortiet.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Använd denna cmdlet för att ansluta till konsortiets hanterings smarta kontrakt. Dessa kontrakt används för att hantera och genomdriva medlemmar i konsortiet.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RootContractAddress | Adress till rot kontrakt för de smarta avtalen för konsortiet Management | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Använd denna cmdlet för att skapa ett objekt för att lagra informationen för en fjärrnods hanterings konto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain medlems konto adress | Ja |
| ManagedAccountPassword | Konto adress lösen ord | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Använd denna cmdlet för att upprätta en anslutning till RPC-slutpunkten för en Transaction-nod.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain medlems slut punkts adress | Ja |

#### <a name="example"></a>Exempel

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Hantera medlemmar i konsortiet

Använd medlems hanterings-cmdletar i konsortiet för att hantera medlemmar i konsortiet. Vilka åtgärder som är tillgängliga beror på din konsortiums roll.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Använd denna cmdlet för att hämta medlems information eller lista medlemmar i konsortiet.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Name | Namnet på den blockchain-tjänst medlem som du vill hämta information om. När ett namn anges returneras medlemmens information. När ett namn utelämnas returneras en lista över alla konsortiums medlemmar. | Inga |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange variabeln $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Exempel på utdata

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Använd denna cmdlet för att ta bort en blockchain-medlem.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Name | Medlems namn att ta bort | Ja |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-objekt hämtades från Import-Web3Account | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection och $MemberAccount variabler.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Använd denna cmdlet för att ange blockchain, inklusive visnings namnet och konsortiet.

Konsortier-administratörer kan ange **DisplayName** och **roll** för alla medlemmar. En konsortiet medlem med användar rollen kan bara ändra sin egen medlems visnings namn.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| Name | Namnet på blockchain-medlemmen | Ja |
| DisplayName | Nytt visnings namn | Inga |
| AccountAddress | Konto adress | Inga |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-objekt hämtades från Import-Web3Account | Ja |
| Web3Client |  Web3Client-objekt hämtades från New-Web3Connection| Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection och $MemberAccount variabler.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Hantera de konsortiums medlemmarnas inbjudningar

Använd-cmdletar för att hantera konsortiets medlemmar med hjälp av konsortiets hanterings-cmdletar för medlems inbjudan. Vilka åtgärder som är tillgängliga beror på din konsortiums roll.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Använd denna cmdlet för att bjuda in nya medlemmar till konsortiet.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Konsortiet-rollen. Värden kan vara ADMIN eller användare. ADMIN är rollen som konsortiet administratör. ANVÄNDAREN är konsortiets medlems roll. | Ja |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-objekt hämtades från Import-Web3Account | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection och $MemberAccount variabler.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Använd denna cmdlet för att hämta eller Visa en medlems status för en konsortium.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för den medlem som ska bjudas in. Om prenumerations-ID: t anges returnerar det prenumerations-ID: t för inbjudan. Om prenumerations-ID: t utelämnas returneras en lista över alla medlems inbjudningar. | Inga |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange variabeln $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Exempel på utdata

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Använd denna cmdlet för att återkalla en konsortiums medlems inbjudan.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för den medlem som ska återkallas | Ja |
| Medlemmar | Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-objekt hämtades från Import-Web3Account | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection och $MemberAccount variabler.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Använd denna cmdlet för att ange **rollen** för en befintlig inbjudan. Endast konsortier-administratörer kan ändra inbjudningar.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beskrivning | Krävs |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-prenumerations-ID för medlemmen att bjuda in | Ja |
| Roll | Ny konsortiums roll för inbjudan. Värden kan vara **användare** eller **administratör**. | Ja |
| Medlemmar |  Medlems objekt som hämtats från Import-ConsortiumManagementContracts | Ja |
| Web3Account | Web3Account-objekt hämtades från Import-Web3Account | Ja |
| Web3Client | Web3Client-objekt hämtades från New-Web3Connection | Ja |

#### <a name="example"></a>Exempel

[Upprätta en Web3-anslutning](#establish-a-web3-connection) för att ange $ContractConnection och $MemberAccount variabler.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Nästa steg

Mer information om konsortier, medlemmar och noder finns i [Azure blockchain service Consortium](consortium.md)
