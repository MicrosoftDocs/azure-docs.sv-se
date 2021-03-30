---
title: Konfigurera en kapacitetspool för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du ställer in en kapacitetspool så att du kan skapa volymer inom den.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325426"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

När du konfigurerar en kapacitetspool kan du skapa volymer inom den.  

## <a name="before-you-begin"></a>Innan du börjar 

Du måste redan ha skapat ett NetApp-konto.   

[Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Steg 

1. Gå till hanteringsbladet för NetApp-kontot. I navigeringsfönstret klickar du på **Kapacitetspooler**.  
    
    ![Gå till kapacitetspoolen](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klicka på **+ Lägg till pooler** när du vill skapa en ny kapacitetspool.   
    Fönstret Ny kapacitetspool visas.

3. Ange följande information för den nya kapacitetspoolen:  
   * **Namn**  
     Ange ett namn för kapacitetspoolen.  
     Namnet på kapacitetspoolen måste vara unikt för varje NetApp-konto.

   * **Service nivå**   
     Det här fältet visar målprestanda för kapacitetspoolen.  
     Ange service nivå för kapacitets gruppen: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)eller [**standard**](azure-netapp-files-service-levels.md#Standard).

    * **Ändra**     
     Ange storleken på den kapacitetspool som du köper.        
     Den minsta kapacitetspoolstorleken är 4 TiB. Du kan skapa poolstorlekar som är multiplar av 4 TiB.   

   * **QoS**   
     Ange om kapacitets gruppen ska använda typen **manuell** eller **Automatisk** QoS.  

     Se [Storage-hierarkin](azure-netapp-files-understand-storage-hierarchy.md) och [prestanda överväganden](azure-netapp-files-performance-considerations.md) för att förstå QoS-typerna.  

     > [!IMPORTANT] 
     > Att ange **QoS-typ** till **manuell** är permanent. Du kan inte konvertera en manuell QoS-kapacitet för att använda automatisk QoS. Du kan dock konvertera en pool med automatisk QoS-kapacitet för att använda manuell QoS. Se [ändra en pool för att använda manuell QoS](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > Att använda den manuella QoS-typen för en kapacitets pool kräver registrering. Se [hantera en manuell pool för QoS-kapacitet](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Ny kapacitetspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg 

- [Lagringspool](azure-netapp-files-understand-storage-hierarchy.md) 
- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Sidan Azure NetApp Files prissättning](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
- [Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
