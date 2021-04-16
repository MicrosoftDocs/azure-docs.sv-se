---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502270"
---
Använd cmdleten [Azure PowerShell New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) för att skapa Key Vault i resursgruppen från föregående steg. Du måste ange en del information:

- Namn på nyckelvalv: En sträng på 3 till 24 tecken som endast får innehålla siffror (0–9), bokstäver (a–z, A–Z) och bindestreck (-)

  > [!Important]
  > Varje nyckelvalv måste ha ett unikt namn. Ersätt <your-unique-keyvault-name> med namnet på ditt nyckelvalv i följande exempel.

- Resursgruppsnamn: **myResourceGroup**.
- Platsen: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valvnamn:** Det namn som du angav för parametern --name ovan.
- **Valv-URI:** I det här exemplet https:// &lt; ditt unika nyckelvalvsnamn &gt; .vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.
