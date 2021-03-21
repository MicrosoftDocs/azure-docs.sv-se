---
title: Skapa en moln tjänst behållare (klassisk) med PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur du skapar en moln tjänst behållare med PowerShell. Behållaren är värd för webb-och arbets roller.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610424"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Använd ett Azure PowerShell-kommando för att skapa en tom moln tjänst behållare (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln förklarar hur du snabbt skapar en Cloud Services behållare med hjälp av Azure PowerShell-cmdletar. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdleten från sidan [Azure PowerShell nedladdningar](https://aka.ms/webpi-azps) .
2. Öppna PowerShell-Kommandotolken.
3. Använd [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) för att logga in.

   > [!NOTE]
   > Mer information om hur du installerar Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration finns i så [här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).
   >
   >
4. Använd cmdleten **New-AzureService** för att skapa en tom moln tjänst behållare i Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Följ det här exemplet för att anropa cmdleten:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure Cloud Service får du genom att köra:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg

* Om du vill hantera moln tjänst distributionen läser du kommandona [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)och [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) . Du kan också se [hur du konfigurerar Cloud Services](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Information om hur du publicerar ditt moln tjänst projekt till Azure finns i  **PublishCloudService.ps1** kod exempel från [Arkiverad moln tjänst lagrings plats](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).