---
title: Skapa en NFS-volym för Azure NetApp Files | Microsoft Docs
description: Den här artikeln visar hur du skapar en NFS-volym i Azure NetApp Files. Lär dig mer om att tänka på, t. ex. vilken version som ska användas och bästa praxis.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 3b6104bcf68c720fa727d16e408a25adcba805aa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259582"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Skapa en NFS-volym för Azure NetApp Files

Azure NetApp Files stöder skapande av volymer med NFS (NFSv3 och NFSv 4.1), SMB3 eller Dual Protocol (NFSv3 och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. 

Den här artikeln visar hur du skapar en NFS-volym. För SMB-volymer, se [skapa en SMB-volym](azure-netapp-files-create-volumes-smb.md). För dubbla protokoll volymer, se [skapa en volym med dubbla protokoll](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Innan du börjar 
* Du måste redan ha konfigurerat en kapacitetspool.  
    Se [Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md).   
* Ett undernät måste delegeras till Azure NetApp Files.  
    Se [delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Överväganden 

* Bestämma vilken NFS-version som ska användas  
  NFSv3 kan hantera en mängd olika användnings fall och distribueras ofta i de flesta företags program. Du bör validera vilken version (NFSv3 eller NFSv 4.1) som programmet behöver och skapa din volym med lämplig version. Om du till exempel använder [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)rekommenderas fil låsning med nfsv 4.1 över NFSv3. 

* Säkerhet  
  Stöd för UNIX läges bitar (läsa, skriva och köra) är tillgängligt för NFSv3 och NFSv 4.1. Åtkomst till rot nivå krävs på NFS-klienten för att montera NFS-volymer.

* Lokal användare/grupp och LDAP-stöd för NFSv 4.1  
  För närvarande stöder NFSv 4.1 endast rot åtkomst till volymer. Se [Konfigurera nfsv 4.1 standard domän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Bästa praxis

* Se till att du använder rätt monterings instruktioner för volymen.  Se [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* NFS-klienten ska finnas i samma VNet eller peer-VNet som Azure NetApp Files volym. Det finns stöd för att ansluta utanför VNet. den kommer dock att införa ytterligare svars tid och minska den övergripande prestandan.

* Se till att NFS-klienten är uppdaterad och att de senaste uppdateringarna för operativ systemet körs.

## <a name="create-an-nfs-volume"></a>Skapa en NFS-volym

1.  Klicka på bladet **volymer** på bladet kapacitets pooler. Klicka på **+ Lägg till volym** för att skapa en volym. 

    ![Navigera till volymer](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  I fönstret Skapa en volym klickar du på **skapa** och anger information för följande fält under fliken grundläggande:   
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

        Det virtuella nätverk som du anger måste ha ett undernät delegerat till Azure NetApp Files. Azure NetApp Files-tjänsten kan endast nås från samma virtuella nätverk eller från ett virtuellt nätverk som finns i samma region som volymen via VNet-peering. Du kan också komma åt volymen från det lokala nätverket via Express Route.   

    * **Undernät**  
        Ange det undernät som du vill använda för volymen.  
        Det undernät som du anger måste delegeras till Azure NetApp Files. 
        
        Om du inte har delegerat ett undernät kan du klicka på **Skapa nytt** på sidan Skapa en volym. På sidan Skapa undernät anger du sedan undernätsinformationen och väljer **Microsoft.NetApp/volumes** för att delegera undernätet för Azure NetApp Files. I varje VNet kan endast ett undernät delegeras till Azure NetApp Files.   
 
        ![Skapa en volym](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Skapa undernät](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Om du vill använda en befintlig ögonblicks bilds princip på volymen klickar du på **Visa Avancerat avsnitt** för att expandera den, ange om du vill dölja sökvägen till ögonblicks bilden och välj en ögonblicks bilds princip på den nedrullningsbara menyn. 

        Information om hur du skapar en ögonblicks bilds princip finns i [Hantera ögonblicks bild principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Visa avancerad markering](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klicka på **protokoll** och utför följande åtgärder:  
    * Välj **NFS** som protokoll typ för volymen.   
    * Ange den **fil Sök väg** som ska användas för att skapa export Sök vägen för den nya volymen. Exportvägen används för att sätta upp och komma åt volymen.

        Filsökvägen får endast innehålla bokstäver, siffror och bindestreck (-). Det måste vara mellan 16 och 40 tecken långt. 

        Fil Sök vägen måste vara unik inom varje prenumeration och varje region. 

    * Välj NFS-version (**NFSv3** eller **nfsv 4.1**) för volymen.  

    * Om du använder NFSv 4.1 anger du om du vill aktivera **Kerberos** -kryptering för volymen.  

        Ytterligare konfigurationer krävs om du använder Kerberos med NFSv 4.1. Följ anvisningarna i [Konfigurera nfsv 4.1 Kerberos-kryptering](configure-kerberos-encryption.md).

    * Om du vill aktivera Active Directory LDAP-användare och utökade grupper (upp till 1024 grupper) för att få åtkomst till volymen väljer du alternativet **LDAP** . Följ anvisningarna i [Konfigurera lägger till LDAP med utökade grupper för NFS-volym åtkomst](configure-ldap-extended-groups.md) för att slutföra de konfigurationer som krävs. 
 
    * Du kan också [Konfigurera export princip för NFS-volymen](azure-netapp-files-configure-export-policy.md).

    ![Ange NFS-protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Granska volym informationen genom att klicka på **Granska + skapa** .  Klicka sedan på **skapa** för att skapa volymen.

    Volymen som du skapade visas på sidan volymer. 
 
    En volym ärver prenumeration, resursgrupp och platsattribut från kapacitetspoolen. Du kan övervaka volymdistributionsstatusen via fliken Meddelanden.


## <a name="next-steps"></a>Nästa steg  

* [Konfigurera NFSv4.1-standarddomän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Konfigurera NFSv4.1 Kerberos-kryptering](configure-kerberos-encryption.md)
* [Konfigurera lägger till LDAP med utökade grupper för NFS-volym åtkomst](configure-ldap-extended-groups.md)
* [Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurera exportprincipen för en NFS-volym](azure-netapp-files-configure-export-policy.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)