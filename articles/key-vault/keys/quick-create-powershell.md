---
title: Skapa och hämta attribut för en nyckel i Azure Key Vault – Azure PowerShell
description: Snabb start visar hur du ställer in och hämtar en nyckel från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e3570a716eccf131cf19192454bfbc1008a6306e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935044"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Snabb start: Ange och hämta en nyckel från Azure Key Vault med Azure PowerShell

I den här snabb starten skapar du ett nyckel valv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault kan du läsa [översikten](../general/overview.md). Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det kommer du att lagra en nyckel.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Sedan skapar du ett nyckelvalv. När du utför det här steget behöver du en del information:

Även om vi använder "contoso KeyVault2" som namn på vår Key Vault i den här snabb starten måste du använda ett unikt namn.

- **Valvnamn** Contoso-Vault2.
- **Resurs grupps namn** ContosoResourceGroup.
- **Plats** USA, östra.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I det här exemplet är det https://Contoso-Vault2.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

När du har skapat valvet så är ditt Azure-konto det enda kontot med behörighet att göra någonting i valvet.

## <a name="add-a-key-to-key-vault"></a>Lägg till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara göra några ytterligare steg. Den här nyckeln kan användas av ett program. 

Skriv följande kommandon för att skapa en anropad **ExampleKey** :

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Nu kan du referera till den här nyckeln som du lade till Azure Key Vault med hjälp av dess URI. Använd **för https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** att hämta den aktuella versionen. 

Så här visar du tidigare lagrad nyckel:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Nu har du skapat en Key Vault, lagrat en nyckel och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Key Vault och lagrat ett certifikat i det. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault-cmdletar](/powershell/module/az.keyvault/)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
