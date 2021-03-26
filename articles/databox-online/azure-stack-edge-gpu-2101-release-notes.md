---
title: Viktig information om Azure Stack Edge Pro 2101
description: Beskriver kritiska öppna problem och lösningar för Azure Stack Edge Pro som kör 2101-versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 84bf14caeec163c31004a430fa954fc36f4be68b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562791"
---
# <a name="azure-stack-edge-2101-release-notes"></a>Viktig information om Azure Stack Edge 2101

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I följande viktig information identifieras kritiska öppna problem och de lösta problemen för 2101-versionen för dina Azure Stack Edge-enheter. Den här viktig information gäller för Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter. Funktioner och problem som motsvarar en speciell modell anropas i tillämpliga fall.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar enheten bör du läsa igenom informationen i viktig information.

Den här artikeln gäller **Azure Stack Edge 2101** -versionen som mappas till versions nummer **2.2.1473.2521**.

## <a name="whats-new"></a>Nyheter

Följande nya funktioner är tillgängliga i Azure Stack Edge 2101-versionen. 

- **Allmän tillgänglighet för Azure Stack Edge Pro r och Azure Stack Edge mini r-enheter** – från och med den här versionen, Azure Stack Edge Pro r-och Azure Stack Edge mini r-enheter är tillgängliga. Mer information finns i [Vad är Azure Stack Edge Pro R](azure-stack-edge-pro-r-overview.md) och [Vad är Azure Stack Edge mini r](azure-stack-edge-mini-r-overview.md).  
- **Moln hantering av Virtual Machines** – från och med den här versionen kan du skapa och hantera de virtuella datorerna på enheten via Azure Portal. Mer information finns i [distribuera virtuella datorer via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
- **Integrering med Azure Monitor** – nu kan du använda Azure Monitor för att övervaka behållare från beräknings programmen som körs på enheten. Azure Monitor Metrics Store stöds inte i den här versionen. Mer information finns i så här [aktiverar du Azure Monitor på enheten](azure-stack-edge-gpu-enable-azure-monitor.md).
- **Edge container Registry** – i den här versionen är ett gräns container register tillgängligt som tillhandahåller en lagrings plats på enheten. Du kan använda det här registret för att lagra och hantera behållar avbildningar. Mer information finns i [Aktivera gräns container Registry](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). 
- **Virtuellt privat nätverk (VPN)** – Använd VPN för att tillhandahålla ett annat krypterings lager för de data som flödar mellan enheterna och molnet. VPN är bara tillgängligt på Azure Stack Edge Pro R och Azure Stack Edge Mini R. Mer information finns i så här [konfigurerar du VPN på enheten](azure-stack-edge-mini-r-configure-vpn-powershell.md). 
- **Rotera kryptering – rest-nycklar** – nu kan du rotera de krypterings-at-rest-nycklar som används för att skydda enheterna på enheten. Den här funktionen är endast tillgänglig för Azure Stack Edge Pro R-och Azure Stack Edge-Mini R-enheter. Mer information finns i [Rotera kryptering – vid-rest-nycklar](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data).
- **Proaktiv loggning** – om du startar den här versionen kan du aktivera proaktiv logg insamling på enheten baserat på systemets hälso indikatorer för att effektivt felsöka eventuella problem med enheten. Mer information finns i [proaktiv logg insamling på enheten](azure-stack-edge-gpu-proactive-log-collection.md).


## <a name="known-issues-in-2101-release"></a>Kända problem i 2101-versionen

Följande tabell innehåller en sammanfattning av kända problem i 2101-versionen.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
|**1.**|Förhandsgranskningsfunktioner |I den här versionen har följande funktioner: lokala Azure Resource Manager, virtuella datorer, moln hantering av virtuella datorer, Azure Arc-aktiverade Kubernetes, VPN för Azure Stack Edge Pro R och Azure Stack Edge Mini R, multi-process service (MPS) för Azure Stack Edge Pro GPU – är alla tillgängliga i för hands versionen.  |Dessa funktioner är allmänt tillgängliga i senare versioner. |
|**2.**|Kubernetes-instrumentpanel | *Https* -slutpunkten för Kubernetes-instrumentpanelen med SSL-certifikat stöds inte. | |
|**3.**|Kubernetes |Edge container Registry fungerar inte när webbproxy är aktiverat.|Funktionen kommer att vara tillgänglig i framtida versioner. |
|**4.**|Kubernetes |Edge container Registry fungerar inte med IoT Edge moduler.| |
|**5.**|Kubernetes |Kubernetes stöder inte ":" i miljö variabel namn som används av .NET-program. Detta krävs också för Event Grid IoT Edge-modulen för att fungera på Azure Stack Edge-enhet och andra program. Mer information finns i [ASP.net Core-dokumentation](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Ersätt ":" med dubbel under streck. Mer information finns i [Kubernetes-problem](https://github.com/kubernetes/kubernetes/issues/53201)|
|**3-6.** |Azure-båg + Kubernetes-kluster |När resursen `yamls` tas bort från git-lagringsplatsen tas som standard inte de motsvarande resurserna bort från Kubernetes-klustret.  |För att tillåta borttagning av resurser när de tas bort från git-lagringsplatsen, anges `--sync-garbage-collection` i Arc-OperatorParams. Mer information finns i [ta bort en konfiguration](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters). |
|**3,7.**|NFS |Program som använder NFS-resurser monteras på enheten för att skriva data ska använda exklusiv skrivning. Detta säkerställer att skrivningarna skrivs till disken.| |
|**7,8.**|Beräknings konfiguration |Beräknings konfiguration Miss lyckas i nätverkskonfigurationer där gatewayer eller växlar eller routrar svarar på ARP-begäranden (Address Resolution Protocol) för system som inte finns i nätverket.| |
|**1.9.**|Compute och Kubernetes |Om Kubernetes konfigureras först på din enhet anlitar den alla tillgängliga GPU: er. Därför är det inte möjligt att skapa Azure Resource Manager virtuella datorer med GPU: er när du har konfigurerat Kubernetes. |Om enheten har 2 GPU: er kan du skapa en virtuell dator som använder GPU: n och sedan konfigurera Kubernetes. I det här fallet kommer Kubernetes att använda de återstående tillgängliga 1 GPU: n. |


## <a name="known-issues-from-previous-releases"></a>Kända problem från tidigare versioner

Följande tabell innehåller en sammanfattning av kända problem som överförs från tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Administratörs åtkomst krävs för att skapa SQL Database.   |Utför följande steg i stället för steg 1-2 i [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Aktivera Compute Interface i enhetens lokala användar gränssnitt. Välj **compute > port # > aktivera för beräkning > tillämpa.**</li><li>Ladda ned `sqlcmd` på klient datorn från https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Anslut till Compute-gränssnittets IP-adress (porten som har Aktiver ATS), och Lägg till en ", 1401" i slutet av adressen.</li><li>Det slutliga kommandot ser ut så här: SQLCMD-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Därefter bör steg 3-4 från den aktuella dokumentationen vara identiska. </li></ul> |
| **2.** |Uppdatera| Stegvisa ändringar av blobbar som återställs via **uppdatering** stöds inte |För BLOB-slutpunkter kan del uppdateringar av blobbar efter en uppdatering resultera i att uppdateringarna inte överförs till molnet. Till exempel en sekvens med åtgärder som:<ul><li>Skapa BLOB i molnet. Eller ta bort en tidigare överförd BLOB från enheten.</li><li>Uppdatera bloben från molnet till enheten med hjälp av uppdaterings funktionen.</li><li>Uppdatera endast en del av bloben med hjälp av Azure SDK REST-API: er.</li></ul>De här åtgärderna kan leda till att de uppdaterade avsnitten i blobben inte uppdateras i molnet. <br>**Lösning**: Använd verktyg som Robocopy eller normal fil kopiering via Utforskaren eller kommando raden för att ersätta hela blobbar.|
|**3.**|Begränsning|Om det inte är tillåtet att skriva av NFS-klienten under begränsningen fungerar inte fel meddelandet "behörighet nekad".| Felet visas som nedan:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: det går inte att skapa katalogen "test": behörighet nekad|
|**4.**|Blob Storage inmatning|När du använder AzCopy version 10 för Blob Storage-inmatningen kör du AzCopy med följande argument: `Azcopy <other arguments> --cap-mbps 2000`| Om dessa begränsningar inte anges för AzCopy kan det potentiellt kunna skicka ett stort antal begär anden till enheten, vilket leder till problem med tjänsten.|
|**5.**|Lagrings konton på nivå|Följande gäller när du använder lagrings konton på nivå:<ul><li> Endast block-blobbar stöds. Page blobbar stöds inte.</li><li>Det finns inget stöd för ögonblicks bilder eller kopiering av API.</li><li> Hadoop-arbetsbelastningar `distcp` stöds inte eftersom kopierings åtgärden i stor utsträckning används.</li></ul>||
|**3-6.**|Anslutning till NFS-resurs|Om flera processer kopieras till samma resurs, och `nolock` attributet inte används, kan det hända att du ser fel under kopieringen.|`nolock`Attributet måste skickas till Mount-kommandot för att filer ska kunna kopieras till NFS-resursen. Exempel: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**3,7.**|Kubernetes-kluster|När du tillämpar en uppdatering på enheten som kör ett Kubernetes-kluster startas de virtuella Kubernetes-datorerna om och startas om. I den här instansen återställs endast poddar som distribueras med repliker som anges automatiskt efter en uppdatering.  |Om du har skapat enskilda poddar utanför en replikeringsprovider utan att ange en replik uppsättning, återställs inte dessa poddar automatiskt efter uppdateringen av enheten. Du måste återställa dessa poddar.<br>En replik uppsättning ersätter poddar som tas bort eller avslutas av någon anledning, t. ex. nodfel eller störningar på nod-uppgraderingen. Därför rekommenderar vi att du använder en replik uppsättning även om programmet bara kräver en enda pod.|
|**7,8.**|Kubernetes-kluster|Kubernetes på Azure Stack Edge Pro stöds bara med Helm v3 eller senare. Mer information finns i [vanliga frågor och svar: borttagning av en till](https://v3.helm.sh/docs/faq/).|
|**1.9.**|Azure Arc-aktiverade Kubernetes |För GA-versionen uppdateras Azure Arc-aktiverade Kubernetes från version 0.1.18 till 0.2.9. Eftersom Azure Arc-aktiverade Kubernetes-uppdateringen inte stöds på Azure Stack Edge-enhet måste du distribuera om Azure Arc-aktiverade Kubernetes.|Följ de här stegen:<ol><li>[Använd enhets program vara och Kubernetes-uppdateringar](azure-stack-edge-gpu-install-update.md).</li><li>Anslut till [enhetens PowerShell-gränssnitt](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Ta bort den befintliga Azure Arc-agenten. Skriv: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Distribuera [Azure-bågen till en ny resurs](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Använd inte en befintlig Azure Arc-resurs.</li></ol>|
|**10.**|Azure Arc-aktiverade Kubernetes|Azure Arc-distributioner stöds inte om webbproxy har kon figurer ATS på din Azure Stack Edge Pro-enhet.||
|**11.3.**|Kubernetes |Port 31000 är reserverad för Kubernetes-instrumentpanelen. Port 31001 är reserverad för Edge container Registry. På samma sätt är IP-adresserna 172.28.0.1 och 172.28.0.10 reserverade för Kubernetes tjänst och kärn-DNS-tjänst i standard konfigurationen.|Använd inte reserverade IP-adresser.|
|**12.5.**|Kubernetes |Kubernetes tillåter för närvarande inte Multi-Protocol LoadBalancer-tjänster. Till exempel en DNS-tjänst som måste lyssna på både TCP och UDP. |För att undvika den här begränsningen av Kubernetes med MetalLB kan två tjänster (en för TCP, en för UDP) skapas på samma Pod-selektor. Dessa tjänster använder samma delnings nyckel och spec. loadBalancerIP för att dela samma IP-adress. IP-adresser kan också delas om du har fler tjänster än tillgängliga IP-adresser. <br> Mer information finns i [IP-adress delning](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.4.**|Kubernetes-kluster|Befintliga Azure IoT Edge Marketplace-moduler kan kräva att ändringar körs på IoT Edge på Azure Stack Edge-enhet.|Mer information finns i ändra Azure IoT Edge moduler från Marketplace till att köra på Azure Stack Edge-enhet.<!-- insert link-->|
|**längre.**|Kubernetes |Filbaserade bind-monteringar stöds inte med Azure IoT Edge på Kubernetes på Azure Stack Edge-enhet.|IoT Edge använder ett översättnings lager för att översätta `ContainerCreate` alternativ till Kubernetes-konstruktioner. Att skapa `Binds` kartor till `hostpath` katalog och därmed kan filbaserade bindnings monteringar inte bindas till sökvägar i IoT Edge behållare. Mappa om möjligt den överordnade katalogen.|
|**15.4.**|Kubernetes |Om du tar med dina egna certifikat för IoT Edge och lägger till dessa certifikat på din Azure Stack Edge-enhet när beräkningen har kon figurer ATS på enheten, hämtas inte de nya certifikaten.|Undvik det här problemet genom att överföra certifikaten innan du konfigurerar beräkning på enheten. Om beräkningen redan har kon figurer ATS [ansluter du till PowerShell-gränssnittet för enheten och kör IoT Edge kommandon](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Starta om `iotedged` och `edgehub` poddar.|
|**16.**|Certifikat |I vissa fall kan det ta flera sekunder att uppdatera certifikat tillstånd i det lokala användar gränssnittet. |Följande scenarier i det lokala användar gränssnittet kan påverkas.<ul><li>**Status** kolumn på sidan **certifikat** .</li><li>**Säkerhets** panel på sidan **Kom igång** .</li><li>**Konfigurations** panelen på **översikts** sidan.</li></ul>  |
|**43.**|IoT Edge |Moduler som distribueras via IoT Edge kan inte använda värd nätverk. | |
|**arton.**|Compute + Kubernetes |Compute/Kubernetes stöder inte NTLM-webbproxy. ||
|**19.3.**|Kubernetes + uppdatera |Tidigare program versioner som 2008-versioner har ett problem med en problem uppdatering som gör att uppdateringen Miss känner till ClusterConnectionException. |Att använda nya versioner bör hjälpa dig att undvika det här problemet. Om du fortfarande ser det här problemet är lösningen att försöka uppgradera igen och bör fungera.|
|**tjugo**|Internet Explorer|Om utökade säkerhetsfunktioner är aktiverade kanske du inte kan komma åt lokala webb GRÄNSSNITTs sidor. | Inaktivera Förbättrad säkerhet och starta om webbläsaren.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Nästa steg

- [Uppdatera enheten](azure-stack-edge-gpu-install-update.md)