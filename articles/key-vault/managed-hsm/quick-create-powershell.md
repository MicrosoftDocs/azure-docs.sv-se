---
title: Skapa och hämta attribut för en hanterad nyckel i Azure Key Vault – Azure PowerShell
description: Snabb start visar hur du ställer in och hämtar en hanterad nyckel från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072921"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Snabb start: Ange och hämta en hanterad nyckel från Azure Key Vault med PowerShell

I den här snabb starten skapar du ett nyckel valv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault kan du läsa [översikten](../general/overview.md). Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det kommer du att lagra en nyckel.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd Azure PowerShell [New-AzResourceGroup-](/powershell/module/az.resources/new-azresourcegroup) cmdlet för att skapa en resurs grupp med namnet *myResourceGroup* på platsen för *väst* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Ditt huvud-ID kommer att returneras i formatet "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX".

## <a name="create-a-managed-hsm"></a>Skapa en hanterad HSM

Använd Azure PowerShell [New-AzKeyVaultManagedHsm-](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) cmdlet för att skapa en ny Key Vault hanterad HSM. Du måste ange en del information:

- Hanterat HSM-namn: en sträng på 3 till 24 tecken som bara får innehålla siffror (0-9), bokstäver (a-z, A-Z) och bindestreck (-)

  > [!Important]
  > Varje hanterad HSM måste ha ett unikt namn. Ersätt <ditt unika hanterade HSM-namn> med namnet på din hanterade HSM i följande exempel.

- Resurs grupp namn: **myResourceGroup**.
- Platsen: **öster**.
- Ditt ägar-ID: skicka Azure Active Directorys huvud-ID som du fick i det sista avsnittet till "administratör"-parametern. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Utdata från denna cmdlet visar egenskaper för den nyligen skapade hanterade HSM. Anteckna de två egenskaperna som visas nedan:

- **Hanterat HSM-namn**: det namn du angav för parametern--name ovan.
- **Valv-URI**: i exemplet är detta https:// &lt; din-unika-hanterad-HSM-name &gt; . Vault.Azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="activate-your-managed-hsm"></a>Aktivera din hanterade HSM

Alla data Plans kommandon är inaktiverade tills HSM har Aktiver ATS. Du kommer inte att kunna skapa nycklar eller tilldela roller. Endast de angivna administratörer som tilldelades under kommandot CREATE kan aktivera HSM. Om du vill aktivera HSM måste du ladda ned [säkerhets domänen](security-domain.md).

För att aktivera din HSM behöver du:
- Minst 3 RSA-nyckelpar (max 10)
- Ange minsta antal nycklar som krävs för att dekryptera säkerhets domänen (kvorum)

Om du vill aktivera HSM skickar du minst 3 (maximalt 10) RSA-offentliga nycklar till HSM. HSM krypterar säkerhets domänen med dessa nycklar och skickar tillbaka den. När den här säkerhets domänen har hämtats är din HSM klar att användas. Du måste också ange kvorum, vilket är det minsta antalet privata nycklar som krävs för att dekryptera säkerhets domänen.

Exemplet nedan visar hur du använder `openssl` (tillgängligt för Windows [här](https://slproweb.com/products/Win32OpenSSL.html)) för att generera tre självsignerade certifikat.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Skapa och lagra RSA-nyckelpar och säkerhets domän fil som genererats i det här steget på ett säkert sätt.

Använd cmdleten Azure PowerShell [export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) för att ladda ned säkerhets domänen och aktivera hanterad HSM. Exemplet nedan använder 3 RSA-nyckelpar (endast offentliga nycklar krävs för det här kommandot) och anger kvorumet till 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Lagra säkerhets domän filen och RSA-nyckel paren på ett säkert sätt. Du behöver dem för haveri beredskap eller för att skapa en annan hanterad HSM som delar samma säkerhets domän, så att de kan dela nycklar.

När du har hämtat säkerhets domänen kommer HSM att vara i aktivt läge och redo att användas.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Key Vault och lagrat ett certifikat i det. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault-cmdletar](/powershell/module/az.keyvault/)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
