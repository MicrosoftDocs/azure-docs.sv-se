---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401089"
---
Tillgänglighets gruppens lyssnare är en IP-adress och ett nätverks namn som SQL Server tillgänglighets gruppen lyssnar på. Skapa tillgänglighets gruppens lyssnare genom att göra följande:

1. <a name="getnet"></a>Hämta namnet på kluster nätverks resursen.

    a. Använd RDP för att ansluta till den virtuella Azure-dator som är värd för den primära repliken. 

    b. Öppna Klusterhanteraren för växling vid fel.

    c. Välj noden **nätverk** och anteckna klustrets nätverks namn. Använd det här namnet i `$ClusterNetworkName` variabeln i PowerShell-skriptet. I följande bild är kluster nätverks namnet **kluster nätverk 1**:

   ![Kluster nätverks namn](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Lägg till klient åtkomst punkten.  
    Klient åtkomst punkten är det nätverks namn som program använder för att ansluta till databaserna i en tillgänglighets grupp. Skapa klient åtkomst punkten i Klusterhanteraren för växling vid fel.

    a. Expandera kluster namnet och klicka sedan på **roller**.

    b. I fönstret **roller** högerklickar du på namnet på tillgänglighets gruppen och väljer sedan **Lägg till resurs**  >  **klient åtkomst punkt**.

   ![Skärm bild som visar meny alternativet klient åtkomst punkt.](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. I rutan **namn** skapar du ett namn för den nya lyssnaren. 
   Namnet på den nya lyssnaren är det nätverks namn som program använder för att ansluta till databaser i SQL Server tillgänglighets grupp.

    d. Slutför skapandet av lyssnaren genom att klicka på **Nästa** två gånger och sedan på **Slutför**. Ta inte med lyssnaren eller resursen online just nu.

1. Koppla från tillgänglighets gruppens kluster roll offline. I **Klusterhanteraren för växling vid fel** under **roller** högerklickar du på rollen och väljer **stoppa roll**.

1. <a name="congroup"></a>Konfigurera IP-resursen för tillgänglighets gruppen.

    a. Klicka på fliken **resurser** och expandera sedan den klient åtkomst punkt som du skapade.  
    Klient åtkomst punkten är offline.

   ![Klient åtkomst punkt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Högerklicka på IP-resursen och klicka sedan på egenskaper. Anteckna namnet på IP-adressen och Använd det i `$IPResourceName` variabeln i PowerShell-skriptet.

    c. Under **IP-adress** klickar du på **statisk IP-adress**. Ange IP-adressen som samma adress som du använde när du konfigurerade adressen för belastningsutjämnaren på Azure Portal.

   ![Skärm bild som visar var du anger IP-adressen.](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Gör SQL Server tillgänglighets grupps resurs beroende av klient åtkomst punkten.

    a. Klicka på **roller** i Klusterhanteraren för växling vid fel och klicka sedan på din tillgänglighets grupp.

    b. På fliken **resurser** , under **andra resurser**, högerklickar du på resurs gruppen tillgänglighet och klickar sedan på **Egenskaper**. 

    c. På fliken beroenden lägger du till namnet på resursen för klient åtkomst punkt (lyssnare).

   ![Skärm bild som visar var du kan lägga till namnet på fliken beroenden.](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klicka på **OK**.

1. <a name="listname"></a>Gör klient åtkomst punkt resursen beroende av IP-adressen.

    a. Klicka på **roller** i Klusterhanteraren för växling vid fel och klicka sedan på din tillgänglighets grupp. 

    b. På fliken **resurser** högerklickar du på resursen för klient åtkomst punkt under **Server namn** och klickar sedan på **Egenskaper**. 

   ![Skärm bild som visar meny alternativet egenskaper för serverns namn.](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klicka på fliken **beroenden** . Kontrol lera att IP-adressen är ett beroende. Om den inte är det anger du ett beroende på IP-adressen. Om det finns flera resurser i listan kontrollerar du att IP-adresserna har eller, inte och, är beroende av varandra. Klicka på **OK**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Du kan kontrol lera att beroendena är korrekt konfigurerade. I Klusterhanteraren för växling vid fel går du till roller, högerklickar på tillgänglighets gruppen, klickar på **fler åtgärder** och klickar sedan på  **Visa beroende rapport**. När beroendena är korrekt konfigurerade är tillgänglighets gruppen beroende av nätverks namnet och nätverks namnet är beroende av IP-adressen. 


1. <a name="setparam"></a>Ange kluster parametrarna i PowerShell.

   a. Kopiera följande PowerShell-skript till någon av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

   - `$ListenerILBIP` är den IP-adress som du skapade i Azure Load Balancer för tillgänglighets gruppens lyssnare.
    
   - `$ListenerProbePort` är den port som du konfigurerade på Azure Load Balancer för tillgänglighets gruppens lyssnare.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ange kluster parametrarna genom att köra PowerShell-skriptet på en av klusternoderna.  

   > [!NOTE]
   > Om SQL Server instanserna finns i olika regioner måste du köra PowerShell-skriptet två gånger. Första gången använder du `$ListenerILBIP` och `$ListenerProbePort` från den första regionen. Den andra gången använder du `$ListenerILBIP` och `$ListenerProbePort` från den andra regionen. Kluster nätverks namnet och kluster-IP-resursens namn är också olika för varje region.

1. Ta tillgänglighets gruppens kluster roll online. I **Klusterhanteraren för växling vid fel** under **roller** högerklickar du på rollen och väljer **Starta roll**.

Om det behövs upprepar du stegen ovan för att ange kluster parametrar för IP-adressen för WSFC-klustret.

1. Hämta IP-adress namnet för WSFC-klustrets IP-adress. Leta upp **Server namn** i **Klusterhanteraren för växling vid fel** under **kluster kärn resurser**.

1. Högerklicka på **IP-adress** och välj **Egenskaper**.

1. Kopiera **namnet** på IP-adressen. Det kan vara `Cluster IP Address` . 

1. <a name="setwsfcparam"></a>Ange kluster parametrarna i PowerShell.
  
   a. Kopiera följande PowerShell-skript till någon av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

   - `$ClusterCoreIP` är den IP-adress som du skapade i Azure Load Balancer för WSFC Core Cluster-resursen. Den skiljer sig från IP-adressen för tillgänglighets gruppens lyssnare.

   - `$ClusterProbePort` är den port som du konfigurerade på Azure Load Balancer för WSFC-hälsosökningen. Det skiljer sig från avsökningen för tillgänglighets gruppens lyssnare.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ange kluster parametrarna genom att köra PowerShell-skriptet på en av klusternoderna.  

>[!WARNING]
>Tillgänglighets gruppens lyssnar på hälso avsöknings port måste skilja sig från klustrets kärn port för hälso avsöknings port. I de här exemplen är lyssnar porten 59999 och klustrets kärn port för hälso avsökning av IP-adresser är 58888. Båda portarna kräver en Tillåt inkommande brand Väggs regel.
