---
title: Skapa en Azure Blockchain Service medlem – Azure PowerShell
description: Skapa en Azure Blockchain Service medlem för ett blockkedjekonsortium med Azure PowerShell.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529634"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Snabbstart: Skapa en Azure Blockchain Service blockkedjemedlem med Azure PowerShell

I den här snabbstarten distribuerar du en ny blockkedjemedlem och ett konsortium i Azure Blockchain Service med Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> När **PowerShell-modulen Az.Blockchain** är i förhandsversion måste du installera den separat från Az PowerShell-modulen med hjälp av `Install-Module` cmdleten . När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av Az PowerShell-modulen och blir tillgänglig inbyggt Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Registrera resursprovider

Om det här är första gången du Azure Blockchain tjänsten måste du registrera **resursprovidern Microsoft.Blockchain.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Välj en specifik Azure-prenumeration

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en specifik prenumeration med hjälp [av cmdleten Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definiera variabler

Du kommer att använda flera typer av information upprepade gånger. Skapa variabler för att lagra informationen.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) med hjälp [av cmdleten New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp baserat på namnet i `$resourceGroupName` variabeln i den region som anges i `$location` variabeln.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

En Azure Blockchain Service är en blockkedjenod i ett privat konsortiums blockkedjenätverk.
När du etablerar en medlem kan du skapa eller ansluta till ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockkedjemedlemmar som deltagarna behöver beror på ditt scenario. Konsortiumdeltagarna kan ha en eller flera blockkedjemedlemmar eller dela medlemmar med andra deltagare. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).

Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempelparametrarna med dina värden.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parameter | Beskrivning |
|---------|-------------|
| **ResourceGroupName** | Resursgruppnamn där Azure Blockchain Service resurser skapas. Använd resursgruppen som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockkedjemedlem. Namnet används för den offentliga slutpunktsadressen. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där blockkedjemedlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. Azure Blockchain Data Manager finns i följande Azure-regioner: USA, östra och Europa, västra.
| **Lösenord** | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod.
| **Protokoll** | Blockkedjeprotokoll. _Kvorumprotokollet_ stöds för närvarande.
| **Konsortium** | Namnet på det konsortium som ska anslutas eller skapas. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | Lösenordet för konsortiumkontot kallas även för lösenordet för medlemskontot. Lösenordet för medlemskontot används för att kryptera den privata nyckeln för det Ethereum-konto som har skapats för din medlem. Du använder medlemskontot och medlemskontolösenordet för konsortiumhantering.
| **Sku** | Nivåtyp. **S0** för standard eller **B0** för basic. Använd _Basic-nivån_ för utveckling, testning och konceptbevis. Använd _standardnivån_ för distributioner i produktionsklass. Du bör också använda _standardnivån_ om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra prisnivån mellan Basic och Standard när medlemmar har skapats.

Det tar cirka 10 minuter att skapa blockkedjemedlemmen och stödresurserna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda blockkedjemedlemmen som du skapade för nästa snabbstart eller självstudie. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resursgruppen som du skapade för snabbstarten.

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här artikeln i den angivna resursgruppen tas de också bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en Azure Blockchain Service medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio Code för att ansluta till Azure Blockchain Service](connect-vscode.md)
