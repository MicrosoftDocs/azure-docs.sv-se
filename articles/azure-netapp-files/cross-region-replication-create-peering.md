---
title: Skapa Volume-replikering för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du skapar replikering mellan flera regioner för att Azure NetApp Files att konfigurera replikering över flera regioner.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 2a3c788ce50ccc1d537fd2903fe05acffd079b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591018"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Skapa Volume Replication för Azure NetApp Files

> [!IMPORTANT]
> Funktionen för replikering över flera regioner finns för närvarande i offentlig för hands version. Du måste skicka in en Waitlist-begäran för att få åtkomst till funktionen via [Azure NetApp Files över flera regioner Waitlist sändnings sida](https://aka.ms/anfcrrpreviewsignup). Vänta tills en officiell bekräftelse skickas från Azure NetApp Files-teamet innan du använder funktionen för replikering över flera regioner.

Den här artikeln visar hur du konfigurerar replikering mellan regioner genom att skapa en replikerings-peering. 

Genom att konfigurera replikering peering kan du replikera data asynkront från en Azure NetApp Files volym (källa) till en annan Azure NetApp Files volym (mål). Käll volymen och mål volymen måste distribueras i olika regioner. Service nivån för mål kapacitets gruppen kan matcha käll kapacitetens pool, eller så kan du välja en annan service nivå.   

Azure NetApp Files replikering har för närvarande inte stöd för flera prenumerationer. alla replikeringar måste utföras under en enda prenumeration.

Innan du börjar bör du kontrol lera att du har granskat [kraven och övervägandena för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Leta upp käll volymens resurs-ID  

Du måste hämta resurs-ID för den käll volym som du vill replikera. 

1. Gå till käll volymen och välj **Egenskaper** under Inställningar för att Visa käll volymens resurs-ID.   
    ![Hitta käll volymens resurs-ID](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Kopiera resurs-ID: t till Urklipp.  Du behöver den senare.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Skapa en datareplikerings volym (mål volymen)

Du måste skapa en mål volym där du vill att data från käll volymen ska replikeras till.  Innan du kan skapa en mål volym måste du ha ett NetApp-konto och en pool för kapacitet i mål regionen. 

1. Mål kontot måste finnas i en annan region än käll volymens region. Om det behövs skapar du ett NetApp-konto i Azure-regionen som ska användas för replikering genom att följa stegen i [skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md).   
Du kan också välja ett befintligt NetApp-konto i en annan region.  

2. Vid behov kan du skapa en pool i det nyskapade NetApp-kontot genom att följa stegen i [Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md).   

    Du kan också välja en befintlig kapacitets pool som ska vara värd för mål volymen för replikeringen.  

    Service nivån för mål kapacitets gruppen kan matcha käll kapacitetens pool, eller så kan du välja en annan service nivå.

3. Delegera ett undernät i den region som ska användas för replikering genom att följa stegen i [delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

4. Skapa volymen för datareplikering genom att välja **volymer** under lagrings tjänsten i mål NetApp-kontot. Klicka sedan på knappen **+ Lägg till datareplikering** .  

    ![Lägg till datareplikering](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. På sidan Skapa en volym som visas fyller du i följande fält under fliken **grundläggande** :
    * Volymnamn
    * Pool för kapacitet
    * Volym kvot
        > [!NOTE] 
        > Volym kvoten (storlek) för mål volymen ska spegla käll volymens storlek. Om du anger en storlek som är mindre än käll volymen ändras storleken på mål volymen automatiskt till käll volymens storlek. 
    * Virtuellt nätverk 
    * Undernät

    Mer information om fälten finns i [skapa en NFS-volym](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. På fliken **protokoll** väljer du samma protokoll som käll volymen.  
För NFS-protokollet ser du till att export policy reglerna uppfyller kraven för alla värdar i fjärrnätverket som kommer att komma åt exporten.  

7. Under fliken **taggar** skapar du nyckel/värde-par vid behov.  

8. Under fliken **replikering** klistrar du in käll volymens resurs-ID som du har fått i [hitta käll VOLYMENS resurs-ID](#locate-the-source-volume-resource-id)och väljer sedan det önskade replikeringsschema. Alternativen för replikeringsschema är: var 10: e minut, varje timme, varje dag, varje vecka och varje månad.  

    ![Skapa volymreplikering](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Klicka på **Granska + skapa** och klicka sedan på **skapa** för att skapa en datareplikerings volym.   

    ![Granska och skapa replikering](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Auktorisera replikering från käll volymen  

Om du vill auktorisera replikeringen måste du hämta resurs-ID för replikeringens mål volym och klistra in den i fältet auktorisera i käll volymen för replikeringen. 

1. I Azure Portal navigerar du till Azure NetApp Files.

2. Gå till mål NetApp-kontot och mål kapacitets poolen där replikeringens mål volym finns.

3. Välj mål volym för replikering, gå till **Egenskaper** under Inställningar och leta reda på **resurs-ID** för mål volymen. Kopiera mål volymens resurs-ID till Urklipp.

    ![Egenskaper för resurs-ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. I Azure NetApp Files går du till käll kontot för replikering och käll kapacitets gruppen. 

5. Leta upp käll volymen för replikering och markera den. Gå till **replikering** under Storage service och klicka på **auktorisera**.

    ![Auktorisera replikering](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. I fältet auktorisera klistrar du in det volym resurs-ID för målplats som du fick i steg 3 och klickar sedan på **OK**.

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Mått för volym replikering](azure-netapp-files-metrics.md#replication)
* [Hantera haveriberedskap](cross-region-replication-manage-disaster-recovery.md)
* [Ta bort volymreplikeringar eller volymer](cross-region-replication-delete.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)

