---
title: Ändra tjänstnivån för en volym dynamiskt för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du dynamiskt ändrar tjänstnivån för en volym.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 3409387adb1e722d8368907d731e02983dd287fc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830168"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Ändra tjänstnivå för en volym dynamiskt

> [!IMPORTANT] 
> Det finns för närvarande inte stöd för att ändra tjänstnivån för en replikeringsmålvolym dynamiskt.

Du kan ändra servicenivån för en befintlig volym genom att flytta volymen till en annan kapacitetspool som använder [den servicenivå](azure-netapp-files-service-levels.md) som du vill använda för volymen. Den här ändringen på servicenivå på plats för volymen kräver inte att du migrerar data. Det påverkar inte heller åtkomsten till volymen.  

Med den här funktionen kan du uppfylla dina arbetsbelastningsbehov på begäran.  Du kan ändra en befintlig volym om du vill använda en högre servicenivå för bättre prestanda eller använda en lägre servicenivå för kostnadsoptimering. Om volymen till exempel för närvarande finns i en kapacitetspool som använder *standardtjänstnivån* och du vill att volymen ska använda *Premium-servicenivån* kan du flytta volymen dynamiskt till en kapacitetspool som använder Premium-tjänstnivån.   

Den kapacitetspool som du vill flytta volymen till måste redan finnas. Kapacitetspoolen kan innehålla andra volymer.  Om du vill flytta volymen till en helt ny kapacitetspool måste du skapa [kapacitetspoolen](azure-netapp-files-set-up-capacity-pool.md) innan du flyttar volymen.  

## <a name="considerations"></a>Överväganden

* När volymen har flyttats till en annan kapacitetspool har du inte längre åtkomst till tidigare volymaktivitetsloggar och volymmått. Volymen börjar med nya aktivitetsloggar och mått under den nya kapacitetspoolen.

* Om du flyttar en volym till en kapacitetspool på en högre servicenivå (till exempel om du flyttar från *Standard* till *Premium* eller *Ultra-servicenivå)* måste du vänta minst sju dagar innan du kan flytta volymen igen till en kapacitetspool med en lägre servicenivå (till exempel flytta från *Ultra* till *Premium* eller *Standard).*   

## <a name="register-the-feature"></a>Registrera funktionen

Funktionen för att flytta en volym till en annan kapacitetspool är för närvarande i förhandsversion. Om du använder den här funktionen för första gången måste du registrera funktionen först.

1. Registrera funktionen: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Kontrollera status för funktionsregistreringen: 

    > [!NOTE]
    > **RegistrationState kan** vara i tillståndet `Registering` i upp till 60 minuter innan du ändrar till `Registered` . Vänta tills statusen är **Registrerad innan** du fortsätter.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Du kan också använda [Azure CLI-kommandon](/cli/azure/feature) `az feature register` och registrera funktionen och visa `az feature show` registreringsstatusen. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Flytta en volym till en annan kapacitetspool

1.  På sidan Volymer högerklickar du på den volym vars servicenivå du vill ändra. Välj **Ändra pool.**

    ![Högerklicka på volym](../media/azure-netapp-files/right-click-volume.png)

2. I fönstret Ändra pool väljer du den kapacitetspool som du vill flytta volymen till. 

    ![Ändra pool](../media/azure-netapp-files/change-pool.png)

3.  Klicka på **OK**.


## <a name="next-steps"></a>Nästa steg  

* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Felsöka problem med att ändra kapacitetspoolen för en volym](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
