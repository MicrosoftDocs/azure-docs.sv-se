---
title: inkludera fil för PowerShell för Azure DNS
description: inkludera fil för PowerShell för Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67133519"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Konfigurera Azure PowerShell för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

Om du vill använda privata zoner (allmänt tillgänglig förhandsversion) måste du också ha PowerShell-modulerna och -versionerna nedan. 
* AzureRM.Dns – [version 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) eller senare
* AzureRM.Network – [version 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) eller senare

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
Utdata från ovanstående kommandon måste visa att versionen av AzureRM. DNS är 4.1.0 eller senare och att AzureRM. Network är 5.4.0 eller senare.  

Om systemet har tidigare versioner installerar du antingen den senaste versionen av Azure PowerShell eller så hämtar du de ovan nämnda modulerna från PowerShell-galleriet med hjälp av länkarna ovan bredvid modulversionerna. Du kan sedan installera dem med hjälp av kommandona nedan. Båda modulerna som krävs och är helt bakåtkompatibla. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna PowerShell-konsolen och anslut till ditt konto. Mer information finns i [Logga in med AzureRM](/powershell/azure/azurerm/authenticate-azureps).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Välja prenumerationen
 
Kontrollera prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrera resursprovider

Azure DNS-tjänsten hanteras av Microsoft.Network-resursprovidern. Din Azure-prenumeration måste vara registrerad att använda den här resursprovidern innan du kan använda Azure DNS. Det här är en engångsåtgärd för varje prenumeration.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
