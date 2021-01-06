---
title: 'Snabb start: Ange & hämta en hemlighet från Key Vault med PowerShell'
description: I den här snabb starten lär du dig att skapa, hämta och ta bort hemligheter från en Azure Key Vault med hjälp av Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: d1fa63da035cba35538d13ffe4c3897458364a65
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936659"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell

Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [översikten](../general/overview.md). I den här snabbstarten använder du PowerShell till att skapa ett nyckelvalv. Sedan lagrar du en hemlighet i valvet du skapade.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Connect-AzAccount
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

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Ge ditt användar konto behörighet att hantera hemligheter i Key Vault

Använd Azure PowerShell Set-AzKeyVaultAccessPolicy-cmdlet: en för att uppdatera Key Vault åtkomst princip och bevilja hemliga behörigheter till ditt användar konto.
```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName 'Contoso-Vault2' -UserPrincipalName 'user@domain.com' -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

När du ska lägga till en hemlighet i valvet behöver du bara utföra ett fåtal steg. I det här fallet lägger du till ett lösenord som kan användas av ett program. Lösenordet kallas **ExamplePassword** och lagrar värdet **hVFkk965BuUv**.

Konvertera först värdet **hVFkk965BuUv** till en säker sträng genom att skriva följande:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Skriv sedan PowerShell-kommandona nedan för att skapa en hemlighet i nyckelvalvet med namnet **ExamplePassword**, som har värdet **hVFkk965BuUv** :


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Hämta en hemlighet från Key Vault

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword'
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Nu har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

 De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med andra snabbstarter och självstudier kan du lämna kvar de här resurserna.

När du inte behöver resursgruppen längre kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) till att ta bort resursgruppen, nyckelvalvet och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en hemlighet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault-cmdletar](/powershell/module/az.keyvault/#key_vault)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
