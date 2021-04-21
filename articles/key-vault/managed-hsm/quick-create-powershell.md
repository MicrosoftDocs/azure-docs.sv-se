---
title: Skapa och hämta attribut för en hanterad nyckel i Azure Key Vault – Azure PowerShell
description: Snabbstart som visar hur du ställer in och hämtar en hanterad nyckel från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815440"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Snabbstart: Ange och hämta en hanterad nyckel från en Azure Key Vault powershell

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [Översikt.](../general/overview.md) Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det lagrar du en nyckel.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd cmdleten [Azure PowerShell New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en resursgrupp med namnet *myResourceGroup* på *platsen westus.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Ditt huvudnamns-ID returneras i formatet "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Skapa en hanterad HSM

Använd cmdlet Azure PowerShell en [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) för att skapa en ny Key Vault HSM. Du måste ange en del information:

- Namn på hanterad HSM: En sträng på 3 till 24 tecken som endast får innehålla siffror (0–9), bokstäver (a–z, A–Z) och bindestreck (-)

  > [!Important]
  > Varje hanterad HSM måste ha ett unikt namn. Ersätt <-your-unique-managed-hsm-name> med namnet på din hanterade HSM i följande exempel.

- Resursgruppsnamn: **myResourceGroup**.
- Platsen: **EastUS**.
- Ditt huvudnamns-ID: Azure Active Directory det huvudnamns-ID som du fick i det förra avsnittet till parametern "Administratör". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Utdata från den här cmdleten visar egenskaperna för den nyligen skapade hanterade HSM:n. Anteckna de två egenskaperna som visas nedan:

- **Namn på hanterad HSM:** Det namn som du angav för parametern --name ovan.
- **Valv-URI:** I det här exemplet är https:// ditt unika hanterade &lt; hsm-namn &gt; .vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="activate-your-managed-hsm"></a>Aktivera din hanterade HSM

Alla kommandon för dataplanet är inaktiverade tills HSM har aktiverats. Du kommer inte att kunna skapa nycklar eller tilldela roller. Endast de utsedda administratörerna som tilldelades under create-kommandot kan aktivera HSM. Om du vill aktivera HSM måste du ladda ned [säkerhetsdomänen](security-domain.md).

För att aktivera din HSM behöver du:
- Minst 3 RSA-nyckelpar (högst 10)
- Ange det minsta antalet nycklar som krävs för att dekryptera säkerhetsdomänen (kvorum)

För att aktivera HSM skickar du minst 3 (högst 10) offentliga RSA-nycklar till HSM. HSM krypterar säkerhetsdomänen med dessa nycklar och skickar tillbaka den. När nedladdningen av säkerhetsdomänen har slutförts är din HSM redo att användas. Du måste också ange kvorum, vilket är det minsta antalet privata nycklar som krävs för att dekryptera säkerhetsdomänen.

Exemplet nedan visar hur du använder `openssl` (tillgängligt för Windows här ) [för](https://slproweb.com/products/Win32OpenSSL.html)att generera 3 själv signerade certifikat.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Skapa och lagra RSA-nyckelparen och säkerhetsdomänfilen som genereras i det här steget på ett säkert sätt.

Använd cmdleten Azure PowerShell Export-AzKeyVaultSecurityDomain för att ladda ned säkerhetsdomänen och aktivera din hanterade HSM med hjälp av cmdleten [Export-AzKeyVaultSecurityDomain.](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) I exemplet nedan används 3 RSA-nyckelpar (endast offentliga nycklar behövs för det här kommandot) och kvorumet anges till 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Lagra säkerhetsdomänfilen och RSA-nyckelparen på ett säkert sätt. Du behöver dem för haveriberedskap eller för att skapa en annan hanterad HSM som delar samma säkerhetsdomän, så att de kan dela nycklar.

När du har hämtat säkerhetsdomänen är din HSM i aktivt tillstånd och redo att användas.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)
- Granska [Key Vault säkerhetsöversikten](../general/security-features.md)
