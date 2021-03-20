---
title: Skapa en Azure blockchain service-medlem – Azure PowerShell
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med Azure PowerShell.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b57c44e79d599ab41b2c3356ee337811acdf639d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948365"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med Azure PowerShell

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om PowerShell-modulen **AZ. blockchain** är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av `Install-Module` cmdleten. När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Registrera resursprovider

Om det här är första gången du använder Azure blockchain-tjänsten måste du registrera Resource-providern för **Microsoft. blockchain** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Välj en enskild Azure-prenumeration

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definiera variabler

Du kommer att använda flera olika informations delar flera gånger. Skapa variabler för att lagra informationen.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp baserat på namnet i `$resourceGroupName` variabeln i den region som anges i `$location` variabeln.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk.
När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempel parametrarna med dina värden.

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
| **ResourceGroupName** | Resurs grupp namn där Azure blockchain service-resurser skapas. Använd den resurs grupp som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där blockchain-medlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.
| **Lösenord** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.
| **Protokoll** | Blockchain-protokoll. För närvarande stöds _kvorum_ protokoll.
| **Konsortium** | Namnet på konsortiet att ansluta till eller skapa. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
| **SKU** | Nivå typ. **S0** för standard-eller **B0** för Basic. Använd _Basic_ -nivån för utveckling, testning och bevis på koncept. Använd _standard_ nivån för distributioner av produktions nivåer. Du bör också använda _standard_ nivån om du använder blockchain Data Manager eller när du skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

Det tar cirka 10 minuter att skapa blockchain-medlemmen och de resurser som stöds.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs gruppen som du skapade för snabb starten.

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)