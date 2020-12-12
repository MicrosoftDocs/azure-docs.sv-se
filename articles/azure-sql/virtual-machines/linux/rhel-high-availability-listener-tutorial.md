---
title: Konfigurera en lyssnare för tillgänglighets grupper för SQL Server virtuella datorer i Azure – Linux Virtual Machines | Microsoft Docs
description: Lär dig mer om att konfigurera en tillgänglighets grupps lyssnare i SQL Server på virtuella RHEL-datorer i Azure
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 7b7ded4e7f94e2f9dfdfdda86aec99ff87f2beda
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359990"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Självstudie: Konfigurera en tillgänglighets grupps lyssnare för SQL Server på virtuella RHEL-datorer i Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Den självstudien som presenteras är i **offentlig för hands version**. 
>
> Vi använder SQL Server 2017 med RHEL 7,6 i den här självstudien, men det går att använda SQL Server 2019 i RHEL 7 eller RHEL 8 för att konfigurera hög tillgänglighet. Kommandona för att konfigurera tillgänglighets grupp resurser har ändrats i RHEL 8, och du vill titta på artikeln [skapa tillgänglighets grupps resurs](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) och RHEL 8-resurser för mer information om rätt kommandon.

I den här självstudien får du lära dig hur du skapar en tillgänglighets grupps lyssnare för dina SQL-servrar på virtuella RHEL-datorer (VM: ar) i Azure. Du lär dig hur du:

> [!div class="checklist"]
> - Skapa en belastningsutjämnare i Azure Portal
> - Konfigurera backend-poolen för belastningsutjämnaren
> - Skapa en avsökning för belastningsutjämnaren
> - Ange regler för belastnings utjämning
> - Skapa belastnings Utjämnings resursen i klustret
> - Skapa tillgänglighets gruppens lyssnare
> - Testa att ansluta till lyssnaren
> - Testa en redundansväxling

## <a name="prerequisite"></a>Förutsättning

Slutförd [självstudie: Konfigurera tillgänglighets grupper för SQL Server på virtuella RHEL-datorer i Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure Portal

Följande instruktioner tar dig igenom steg 1 till 4 från guiden för att [skapa och konfigurera belastningsutjämnaren i Azure Portal](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) avsnittet i artikeln [load balances-Azure Portal](../windows/availability-group-load-balancer-portal-configure.md) .

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

1. Öppna den resurs grupp som innehåller de SQL Server virtuella datorerna i Azure Portal. 

2. I resurs gruppen klickar du på **Lägg till**.

3. Sök efter **belastningsutjämnare** och välj **Load Balancer** i Sök resultaten, som publiceras av **Microsoft**.

4. Klicka på **skapa** på bladet **Load Balancer** .

5. I dialog rutan **skapa belastnings utjämning** konfigurerar du belastningsutjämnaren enligt följande:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett text namn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Intern** |
   | **Virtuellt nätverk** |Det virtuella standard nätverket som skapades ska ha namnet **VM1VNET**. |
   | **Undernät** |Välj det undernät som de SQL Server instanserna finns i. Standardvärdet ska vara **VM1Subnet**.|
   | **Tilldelning av IP-adress** |**Statiskt** |
   | **Privat IP-adress** |Använd `virtualip` IP-adressen som skapades i klustret. |
   | **Prenumeration** |Använd den prenumeration som användes för din resurs grupp. |
   | **Resursgrupp** |Välj den resurs grupp som SQL Server instanserna finns i. |
   | **Plats** |Välj den Azure-plats som SQL Server instanserna finns i. |

### <a name="configure-the-back-end-pool"></a>Konfigurera backend-poolen
Azure anropar *backend-adresspoolen* för backend-adresspoolen. I det här fallet är backend-poolen adresserna till de tre SQL Server instanserna i tillgänglighets gruppen. 

1. I resurs gruppen klickar du på den belastningsutjämnare som du har skapat. 

2. I **Inställningar** klickar du på **backend-pooler**.

3. På **backend-pooler** klickar du på **Lägg till** för att skapa en backend-adresspool. 

4. I **Lägg till backend-pool**, under **namn**, anger du ett namn för backend-poolen.

5. Under **kopplad till** väljer du **virtuell dator**. 

6. Välj varje virtuell dator i miljön och koppla lämplig IP-adress till varje val.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Lägg till backend-pool":::

7. Klicka på **Lägg till**. 

### <a name="create-a-probe"></a>Skapa en avsökning

Avsökningen definierar hur Azure verifierar vilken av de SQL Server instanser som för närvarande äger tillgänglighets gruppens lyssnare. Azure avsöker tjänsten baserat på IP-adressen på en port som du anger när du skapar avsökningen.

1. Klicka på **hälso avsökningar** på bladet **Inställningar** för belastnings utjämning. 

2. På bladet **hälso avsökningar** klickar du på **Lägg till**.

3. Konfigurera avsökningen på bladet **Lägg till sökning** . Använd följande värden för att konfigurera avsökningen:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett text namn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Du kan använda valfri tillgänglig port. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Tröskelvärde för ej felfri** |*2* |

4.  Klicka på **OK**. 

5. Logga in på alla virtuella datorer och öppna avsöknings porten med följande kommandon:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure skapar avsökningen och använder den för att testa vilken SQL Server instans som har lyssnaren för tillgänglighets gruppen.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastnings utjämning

Reglerna för belastnings utjämning anger hur belastningsutjämnaren dirigerar trafik till SQL Server instanser. För den här belastningsutjämnaren aktiverar du direkt Server retur eftersom endast en av de tre SQL Server instanserna äger tillgänglighets gruppens lyssnar resurs i taget.

1. Klicka på **belastnings Utjämnings regler** på bladet **Inställningar** för belastningsutjämnare. 

2. Klicka på **Lägg till** på bladet **belastnings Utjämnings regler** .

3. Konfigurera belastnings Utjämnings regeln på bladet **Lägg till belastnings Utjämnings regler** . Använd följande inställningar: 

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett text namn som representerar reglerna för belastnings utjämning. Till exempel **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Serverdelsport** |*1433*. det här värdet ignoreras eftersom den här regeln använder **flytande IP (direkt Server retur)**. |
   | **Avsökning** |Använd namnet på avsökningen som du skapade för den här belastningsutjämnaren. |
   | **Sessionspermanens** |**Ingen** |
   | **Tids gräns för inaktivitet (minuter)** |*4* |
   | **Flytande IP (direkt Server retur)** |**Aktiverad** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Lägg till belastnings Utjämnings regel":::

4. Klicka på **OK**. 
5. Azure konfigurerar belastnings Utjämnings regeln. Nu konfigureras belastningsutjämnaren för att dirigera trafik till den SQL Server-instans som är värd för lyssnaren för tillgänglighets gruppen. 

I det här läget har resurs gruppen en belastningsutjämnare som ansluter till alla SQL Server datorer. Belastningsutjämnaren innehåller också en IP-adress för SQL Server Always on-tillgänglighetsgrupper, så att alla datorer kan svara på begär Anden för tillgänglighets grupper.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Skapa belastnings Utjämnings resursen i klustret

1. Logga in på den primära virtuella datorn. Vi måste skapa resursen för att aktivera avsöknings porten för Azure Load Balancer (59999 används i vårt exempel). Kör följande kommando:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Skapa en grupp som innehåller- `virtualip` och- `azure_load_balancer` resursen:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Lägg till begränsningar

1. En samplacerings begränsning måste konfigureras för att se till att IP-adressen för Azure Load Balancer och AG-resursen körs på samma nod. Kör följande kommando:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Skapa en ordnings begränsning för att säkerställa att AG-resursen är igång innan IP-adressen för Azure Load Balancer. Även om den samplacerings begränsningen indikerar en ordnings begränsning används den.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Verifiera begränsningarna genom att köra följande kommando:

    ```bash
    sudo pcs constraint list --full
    ```

    Du bör se följande utdata:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Skapa tillgänglighets gruppens lyssnare

1. Kör följande kommando på den primära noden i SQLCMD eller SSMS:

    - Ersätt IP-adressen som används nedan med `virtualip` IP-adressen.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Logga in på varje VM-nod. Använd följande kommando för att öppna hosts-filen och konfigurera värd namns matchning för `ag1-listener` på varje dator.

    ```
    sudo vi /etc/hosts
    ```

    I redigeraren för **vi** skriver `i` du in text och lägger till IP-adressen för i en tom rad `ag1-listener` . Lägg sedan till `ag1-listener` efter ett blank steg bredvid IP-adressen.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Om du vill avsluta **vi** -redigeraren trycker du först på **ESC** -tangenten och anger sedan kommandot `:wq` för att skriva filen och avsluta. Gör detta på varje nod.

## <a name="test-the-listener-and-a-failover"></a>Testa lyssnaren och redundansväxlingen

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Testa inloggning till SQL Server med hjälp av tillgänglighets gruppens lyssnare

1. Använd SQLCMD för att logga in på den primära noden i SQL Server att använda tillgänglighets gruppens lyssnar namn:

    - Använd en inloggning som tidigare har skapats och Ersätt `<YourPassword>` med rätt lösen ord. I exemplet nedan används den `sa` inloggning som skapades med SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Kontrol lera namnet på den server som du är ansluten till. Kör följande kommando i SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Din utdata bör visa den aktuella primära noden. Detta bör vara `VM1` om du aldrig har testat en redundansväxling.

    Avsluta SQL Server-sessionen genom att skriva `exit` kommandot.

### <a name="test-a-failover"></a>Testa en redundansväxling

1. Kör följande kommando för att redundansväxla den primära repliken manuellt till `<VM2>` eller till en annan replik. Ersätt `<VM2>` med värdet för Server namnet.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Om du kontrollerar dina begränsningar ser du att en annan begränsning har lagts till på grund av manuell redundans:

    ```bash
    sudo pcs constraint list --full
    ```

    Du ser att en begränsning med ID har `cli-prefer-ag_cluster-master` lagts till.

1. Ta bort begränsningen med ID `cli-prefer-ag_cluster-master` med hjälp av följande kommando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Kontrol lera kluster resurserna med kommandot `sudo pcs resource` och se till att den primära instansen är nu `<VM2>` .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Använd SQLCMD för att logga in på din primära replik med hjälp av lyssnar namnet:

    - Använd en inloggning som tidigare har skapats och Ersätt `<YourPassword>` med rätt lösen ord. I exemplet nedan används den `sa` inloggning som skapades med SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Kontrol lera den server som du är ansluten till. Kör följande kommando i SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Du bör se att du nu är ansluten till den virtuella datorn som du har växlat till.

## <a name="next-steps"></a>Nästa steg

Mer information om belastnings utjämning i Azure finns i:

> [!div class="nextstepaction"]
> [Konfigurera en belastnings utjämning för en tillgänglighets grupp på SQL Server på virtuella Azure-datorer](../windows/availability-group-load-balancer-portal-configure.md)
