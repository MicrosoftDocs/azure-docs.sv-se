---
title: Snabb start – skapa en Azure Key Vault med Azure PowerShell
description: Snabb start visar hur du skapar en Azure Key Vault med hjälp av Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 73d247464f167040c6f7129bdf7014b877317fc5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936273"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Snabb start: skapa ett nyckel valv med PowerShell

Azure Key Vault är en moln tjänst som tillhandahåller en säker lagring för [nycklar](../keys/index.yml), [hemligheter](../secrets/index.yml)och [certifikat](../certificates/index.yml). Mer information om Key Vault finns i [About Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckel valv finns i [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

I den här snabb starten skapar du ett nyckel valv med [Azure PowerShell](/powershell/azure/). Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa en Key Vault i resurs gruppen från föregående steg. Du måste ange en del information:

- Nyckel valvets namn: en sträng på 3 till 24 tecken som bara får innehålla siffror (0-9), bokstäver (a-z, A-Z) och bindestreck (-)

  > [!Important]
  > Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

- Resurs grupp namn: **myResourceGroup**.
- Platsen: **öster**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-key-vault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valv namn**: det namn du angav för parametern--name ovan.
- **Valv-URI**: i exemplet är detta https://<ditt-Unique--namn>. Vault.Azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med andra snabbstarter och självstudier kan du lämna kvar de här resurserna.

När de inte längre behövs kan du använda kommandot Azure PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault med hjälp av Azure PowerShell. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](overview.md)
- Se referensen för [Azure PowerShell Key Vault-cmdletar](/powershell/module/az.keyvault/)
- Granska [Azure Key Vault säkerhets översikt](security-overview.md)

