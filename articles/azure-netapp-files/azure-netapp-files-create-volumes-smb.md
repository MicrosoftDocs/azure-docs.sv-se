---
title: Skapa en SMB-volym för Azure NetApp Files | Microsoft Docs
description: Den här artikeln visar hur du skapar en SMB3-volym i Azure NetApp Files. Läs mer om kraven för Active Directory anslutningar och domän tjänster.
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
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 6bb787084d4396737d3002bb98fe50e283c5764a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582898"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Skapa en SMB-volym för Azure NetApp Files

Azure NetApp Files stöder skapande av volymer med NFS (NFSv3 och NFSv 4.1), SMB3 eller Dual Protocol (NFSv3 och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. Den här artikeln visar hur du skapar en SMB3-volym.

## <a name="before-you-begin"></a>Innan du börjar 
Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="configure-active-directory-connections"></a>Konfigurera Active Directory anslutningar 

Innan du skapar en SMB-volym måste du skapa en Active Directory anslutning. Om du inte har konfigurerat Active Directory anslutningar för Azure NetApp-filer, följ instruktionerna som beskrivs i [skapa och hantera Active Directory-anslutningar](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Lägg till en SMB-volym

1. Klicka på bladet **volymer** på bladet kapacitets pooler. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicka på **+ Lägg till volym** för att skapa en volym.  
    Fönstret Skapa en volym visas.

3. I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält under fliken grundläggande:   
    * **Volym namn**      
        Ange namnet på den volym du skapar.   

        Ett volym namn måste vara unikt inom varje pool för kapacitet. Det måste innehålla minst tre tecken. Du kan använda alla alfanumeriska tecken.   

        Du kan inte använda `default` eller `bin` som volym namn.

    * **Pool för kapacitet**  
        Ange den pool där du vill att volymen ska skapas.

    * **Kvot**  
        Ange mängden logisk lagring som tilldelas till volymen.  

        Fältet **Tillgänglig kvot** visar mängden outnyttjat utrymme i kapacitetspoolen, som du kan använda för att skapa en ny volym. Storleken på den nya volymen får inte överskrida den tillgängliga kvoten.  

    * **Data flöde (MiB/S)**   
        Om volymen har skapats i en manuell pool för QoS-kapacitet anger du det data flöde som du vill använda för volymen.   

        Om volymen skapas i en pool med automatisk QoS-kapacitet är värdet som visas i det här fältet (kvot x service nivåns data flöde).   

    * **Virtuellt nätverk**  
        Ange det virtuella Azure-nätverk (VNet) som du vill ha åtkomst till volymen från.  

        Det virtuella nätverk du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Filess tjänsten kan endast nås från samma VNet eller från ett VNet som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Om du vill använda en befintlig ögonblicks bilds princip på volymen klickar du på **Visa Avancerat avsnitt** för att expandera den, ange om du vill dölja sökvägen till ögonblicks bilden och välj en ögonblicks bilds princip på den nedrullningsbara menyn. 

        Information om hur du skapar en ögonblicks bilds princip finns i [Hantera ögonblicks bild principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Visa avancerad markering](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klicka på **protokoll** och Slutför följande information:  
    * Välj **SMB** som protokoll typ för volymen. 
    * Välj din **Active Directory** anslutning i list rutan.
    * Ange namnet på den delade volymen i  **resurs namn**.

    ![Ange SMB-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Granska volym informationen genom att klicka på **Granska + skapa** .  Klicka sedan på **skapa** för att skapa SMB-volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.

## <a name="control-access-to-an-smb-volume"></a>Kontrol lera åtkomst till en SMB-volym  

Åtkomst till en SMB-volym hanteras via behörigheter.  

### <a name="share-permissions"></a>Resurs behörigheter  

Som standard har en ny volym behörigheten **alla/fullständig behörighet** till resurs. Medlemmar i gruppen domän administratörer kan ändra resurs behörigheter genom att använda dator hantering på det dator konto som används för den Azure NetApp Files volymen.

![](../media/azure-netapp-files/smb-mount-path.png) 
Resurs behörigheter för SMB Mount-sökväg ![](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-fil-och mappbehörigheter  

Du kan ange behörigheter för en fil eller mapp genom att använda fliken **säkerhet** i objektets egenskaper i Windows SMB-klienten.
 
![Ange behörigheter för filer och mappar](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Nästa steg  

* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vanliga frågor och svar om SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Felsöka SMB-eller Dual-Protocol-volymer](troubleshoot-dual-protocol-volumes.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
* [Installera en ny Active Directory skog med hjälp av Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
