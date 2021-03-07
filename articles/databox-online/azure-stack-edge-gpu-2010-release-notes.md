---
title: Viktig information om Azure Stack Edge Pro GA | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Stack Edge Pro som kör den allmänna tillgänglighets versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 20340a07160a0b4501069e0f4d45599c5bff33f2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444062"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Viktig information om Azure Stack Edge Pro med GPU allmän tillgänglighet (GA)

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

I följande versions information identifieras kritiska öppnings problem och lösta problem för allmän tillgänglighet (GA) för dina Azure Stack Edge Pro-enheter med GPU.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din Azure Stack Edge Pro-enhet.

Den här artikeln gäller för **Azure Stack Edge Pro 2010** -versionen som mappar till program versions nummer **2.1.1377.2170**.

## <a name="whats-new"></a>Nyheter

Följande nya funktioner är tillgängliga i Azure Stack Edge 2010-versionen. 

- **Lagrings klasser** – i den här versionen finns det tillgängliga lagrings klasser som gör det möjligt att etablera lagringen dynamiskt. Mer information finns i [Kubernetes Storage Management på Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Kubernetes-instrumentpanel med mått Server** – i den här versionen läggs en Kubernetes-instrumentpanel till med ett mått Server tillägg. Du kan använda instrument panelen för att få en översikt över de program som körs på din Azure Stack Edge Pro-enhet, Visa status för Kubernetes kluster resurser och se eventuella fel som har inträffat på enheten. Mått servern sammanställer processor-och minnes användningen mellan Kubernetes resurser på enheten. Mer information finns i [använda Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc Enabled Kubernetes på Azure Stack Edge Pro** – från den här versionen kan du distribuera program arbets belastningar på din Azure Stack Edge Pro-enhet via Azure Arc-aktiverad Kubernetes. Azure Arc är ett hybrid hanterings verktyg som gör att du kan distribuera program i Kubernetes-kluster. Mer information finns i [distribuera arbets belastningar via Azure Arc på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Kända problem 

Följande tabell innehåller en sammanfattning av kända problem för Azure Stack Edge Pro-enheten.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
|**1.**|Förhandsgranskningsfunktioner |I den här GA-versionen har följande funktioner: lokala Azure Resource Manager, virtuella datorer, Kubernetes, Azure Arc-aktiverade Kubernetes, multi-process service (MPS) för GPU: er tillgängliga i för hands versionen för din Azure Stack Edge Pro-enhet.  |Dessa funktioner är allmänt tillgängliga i en senare version. |
| **2.** |Azure Stack Edge Pro + Azure SQL | Administratörs åtkomst krävs för att skapa SQL Database.   |Utför följande steg i stället för steg 1-2 i [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Aktivera Compute Interface i enhetens lokala användar gränssnitt. Välj **compute > port # > aktivera för beräkning > tillämpa.**</li><li>Ladda ned `sqlcmd` på klient datorn från https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Anslut till Compute-gränssnittets IP-adress (porten som har Aktiver ATS), och Lägg till en ", 1401" i slutet av adressen.</li><li>Det slutliga kommandot ser ut så här: SQLCMD-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Därefter bör steg 3-4 från den aktuella dokumentationen vara identiska. </li></ul> |
| **3.** |Uppdatera| Stegvisa ändringar av blobbar som återställs via **uppdatering** stöds inte |För BLOB-slutpunkter kan del uppdateringar av blobbar efter en uppdatering resultera i att uppdateringarna inte överförs till molnet. Till exempel en sekvens med åtgärder som:<ul><li>Skapa BLOB i molnet. Eller ta bort en tidigare överförd BLOB från enheten.</li><li>Uppdatera bloben från molnet till enheten med hjälp av uppdaterings funktionen.</li><li>Uppdatera endast en del av bloben med hjälp av Azure SDK REST-API: er.</li></ul>De här åtgärderna kan leda till att de uppdaterade avsnitten i blobben inte uppdateras i molnet. <br>**Lösning**: Använd verktyg som Robocopy eller normal fil kopiering via Utforskaren eller kommando raden för att ersätta hela blobbar.|
|**4.**|Begränsning|Om nya skrivningar inte är tillåtna i enheten under begränsningen, kan skrivningar som görs av NFS-klienten nekas fel meddelandet "behörighet nekad".| Felet visas som nedan:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: det går inte att skapa katalogen "test": behörighet nekad|
|**5.**|Blob Storage inmatning|När du använder AzCopy version 10 för Blob Storage-inmatningen kör du AzCopy med följande argument: `Azcopy <other arguments> --cap-mbps 2000`| Om dessa begränsningar inte anges för AzCopy, kan det potentiellt skicka ett stort antal begär anden till enheten och leda till problem med tjänsten.|
|**3-6.**|Lagrings konton på nivå|Följande gäller när du använder lagrings konton på nivå:<ul><li> Endast block-blobbar stöds. Page blobbar stöds inte.</li><li>Det finns inget stöd för ögonblicks bilder eller kopiering av API.</li><li> Hadoop-arbetsbelastningar `distcp` stöds inte eftersom kopierings åtgärden i stor utsträckning används.</li></ul>||
|**3,7.**|Anslutning till NFS-resurs|Om flera processer kopieras till samma resurs, och `nolock` attributet inte används, kan det hända att du ser fel under kopieringen.|`nolock`Attributet måste skickas till Mount-kommandot för att filer ska kunna kopieras till NFS-resursen. Exempel: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7,8.**|Kubernetes-kluster|När du tillämpar en uppdatering på enheten som kör ett Kubernetes-kluster startas de virtuella Kubernetes-datorerna om och startas om. I den här instansen återställs endast poddar som distribueras med repliker som anges automatiskt efter en uppdatering.  |Om du har skapat enskilda poddar utanför en replikeringsprovider utan att ange en replik uppsättning återställs inte dessa poddar automatiskt efter uppdateringen av enheten. Du måste återställa dessa poddar.<br>En replik uppsättning ersätter poddar som tas bort eller avslutas av någon anledning, t. ex. nodfel eller störningar på nod-uppgraderingen. Därför rekommenderar vi att du använder en replik uppsättning även om programmet bara kräver en enda pod.|
|**1.9.**|Kubernetes-kluster|Kubernetes på Azure Stack Edge Pro stöds bara med Helm v3 eller senare. Mer information finns i [vanliga frågor och svar: borttagning av en till](https://v3.helm.sh/docs/faq/).|
|**10.**|Azure Arc-aktiverade Kubernetes |För GA-versionen uppdateras Azure Arc-aktiverade Kubernetes från version 0.1.18 till 0.2.9. Eftersom Azure Arc-aktiverade Kubernetes-uppdateringen inte stöds på Azure Stack Edge-enhet måste du distribuera om Azure Arc-aktiverade Kubernetes.|Gör så här:<ol><li>[Använd enhets program vara och Kubernetes-uppdateringar](azure-stack-edge-gpu-install-update.md).</li><li>Anslut till [enhetens PowerShell-gränssnitt](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Ta bort den befintliga Azure Arc-agenten. Skriv: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Distribuera [Azure-bågen till en ny resurs](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Använd inte en befintlig Azure Arc-resurs.</li></ol>|
|**11.3.**|Azure Arc-aktiverade Kubernetes|Azure Arc-distributioner stöds inte om webbproxy har kon figurer ATS på din Azure Stack Edge Pro-enhet.||
|**12.5.**|Kubernetes |Port 31000 är reserverad för Kubernetes-instrumentpanelen. På samma sätt är IP-adresserna 172.28.0.1 och 172.28.0.10 reserverade för Kubernetes tjänst och kärn-DNS-tjänst i standard konfigurationen.|Använd inte reserverade IP-adresser.|
|**13.4.**|Kubernetes |Kubernetes tillåter för närvarande inte Multi-Protocol LoadBalancer-tjänster. Till exempel en DNS-tjänst som måste lyssna på både TCP och UDP. |För att undvika den här begränsningen av Kubernetes med MetalLB kan två tjänster (en för TCP, en för UDP) skapas på samma Pod-selektor. Dessa tjänster använder samma delnings nyckel och spec. loadBalancerIP för att dela samma IP-adress. IP-adresser kan också delas om du har fler tjänster än tillgängliga IP-adresser. <br> Mer information finns i [IP-adress delning](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**längre.**|Kubernetes-kluster|Befintliga Azure IoT Edge Marketplace-moduler kan kräva att ändringar körs på IoT Edge på Azure Stack Edge-enhet.|Mer information finns i ändra Azure IoT Edge moduler från Marketplace till att köra på Azure Stack Edge-enhet.<!-- insert link-->|
|**15.4.**|Kubernetes |Filbaserade bind-monteringar stöds inte med Azure IoT Edge på Kubernetes på Azure Stack Edge-enhet.|IoT Edge använder ett översättnings lager för att översätta `ContainerCreate` alternativ till Kubernetes-konstruktioner. Att skapa `Binds` kartor till `hostpath` katalog och därmed kan filbaserade bindnings monteringar inte bindas till sökvägar i IoT Edge behållare. Mappa om möjligt den överordnade katalogen.|
|**16.**|Kubernetes |Om du använder dina egna certifikat för IoT Edge och lägger till dem på din Azure Stack Edge-enhet när beräkningen har kon figurer ATS på enheten, hämtas inte de nya certifikaten.|Undvik det här problemet genom att överföra certifikaten innan du konfigurerar beräkning på enheten. Om beräkningen redan har kon figurer ATS [ansluter du till PowerShell-gränssnittet för enheten och kör IoT Edge kommandon](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Starta om `iotedged` och `edgehub` poddar.|
|**43.**|Certifikat |I vissa fall kan det ta flera sekunder att uppdatera certifikat tillstånd i det lokala användar gränssnittet. |Följande scenarier i det lokala användar gränssnittet kan påverkas.<ul><li>**Status** kolumn på sidan **certifikat** .</li><li>**Säkerhets** panel på sidan **Kom igång** .</li><li>**Konfigurations** panelen på **översikts** sidan.</li></ul>  |
|**43.**|IoT Edge |Moduler som distribueras via IoT Edge kan inte använda värd nätverk. | |
|**arton.**|Compute + Kubernetes |Compute/Kubernetes stöder inte NTLM-webbproxy. ||
|**19.3.**|Compute + Web Proxy + uppdatera |Om du har en beräkning som kon figurer ATS med webbproxy kan det hända att Compute-uppdateringen Miss lyckas. |Vi rekommenderar att du inaktiverar Compute innan uppdateringen. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge Pro-enhet med GPU](azure-stack-edge-gpu-deploy-prep.md)