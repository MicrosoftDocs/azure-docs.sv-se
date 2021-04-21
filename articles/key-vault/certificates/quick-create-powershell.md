---
title: Snabbstart – Ange & visa Azure Key Vault certifikat med Azure PowerShell
description: Snabbstart som visar hur du ställer in och hämtar ett certifikat från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: a641ca1206cb41ded0513db72daa278dc3753c85
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750400"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Snabbstart: Ange och hämta ett certifikat från Azure Key Vault med Azure PowerShell

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [Översikt.](../general/overview.md) Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det lagrar du ett certifikat.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Lägga till ett certifikat i Key Vault

Om du vill lägga till ett certifikat i valvet behöver du bara vidta några ytterligare steg. Det här certifikatet kan användas av ett program. 

Skriv kommandona nedan för att skapa ett själv signerat certifikat med principen **ExampleCertificate** :

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Du kan nu referera till det här certifikatet som du har lagt till Azure Key Vault med hjälp av dess URI. Använd **"https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate" för** att hämta den aktuella versionen. 

Så här visar du tidigare lagrade certifikat:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Nu har du skapat en Key Vault, lagrat ett certifikat och hämtat det.

**Felsökning:**

Åtgärden returnerade den ogiltiga statuskoden "Förbjuden"

Om du får det här felet har kontot som använder Azure Key Vault inte rätt behörighet för att skapa certifikat.

Kör följande Azure PowerShell för att tilldela rätt behörigheter:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)
- Granska [Key Vault säkerhetsöversikten](../general/security-overview.md)
