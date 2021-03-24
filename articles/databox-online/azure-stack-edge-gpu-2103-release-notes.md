---
title: Viktig information om Azure Stack Edge Pro 2103
description: Beskriver kritiska öppna problem och lösningar för Azure Stack Edge Pro som kör 2103-versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962738"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Viktig information om Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I följande viktig information identifieras kritiska öppna problem och de lösta problemen för 2103-versionen för dina Azure Stack Edge-enheter. Den här viktig information gäller för Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter. Funktioner och problem som motsvarar en speciell modell anropas i tillämpliga fall.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar enheten bör du läsa igenom informationen i viktig information.

Den här artikeln gäller **Azure Stack Edge 2103** -versionen som mappas till versions nummer **2.2.1540.2890**. Den här program varan kan användas på din enhet om du kör minst Azure Stack Edge 2010-program (2.1.1377.2170).

## <a name="whats-new"></a>Nyheter

Följande nya funktioner är tillgängliga i Azure Stack Edge 2103-versionen. 
 
- **Nya funktioner för Virtual Machines** – om du startar den här versionen kan du utföra följande hanterings åtgärder på de virtuella datorerna via Azure Portal:
    - Lägg till eller ta bort flera nätverks gränssnitt till en befintlig virtuell dator.
    - Lägg till eller ta bort flera diskar i en befintlig virtuell dator.
    - Ändra storlek på den virtuella datorn.
    - Lägg till anpassade data när du distribuerar en Windows-eller Linux-dator.

  Du kan också [ansluta till den virtuella dator konsolen på enheten](azure-stack-edge-gpu-connect-virtual-machine-console.md) och felsöka eventuella problem med virtuella datorer.

- **Anslut till PowerShell-gränssnittet via https** – om du startar den här versionen kommer du inte längre att kunna öppna en PowerShell-fjärrsession i en enhet via *http*. Som standard används *https* för alla sessioner. Mer information finns i så här [ansluter du till PowerShell-gränssnittet](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) på enheten.

- **Förbättringar för beräkning** – flera förbättringar och förbättringar har gjorts inklusive de för:

    - **Total beräknings plattforms kvalitet**. Den här versionen har fel korrigeringar för att förbättra den övergripande beräknings plattformens kvalitet. Se [problem som korrigeras i 2103-versionen](#issues-fixed-in-2103-release). 
    - **Compute Platform-komponenter**. Säkerhets uppdateringar tillämpades på avbildningen av Compute VM. IoT Edge och Azure Arc för Kubernetes-versioner har också uppdaterats.
    - **Diagnostik**. Ett nytt API släpps för att kontrol lera resurs-och nätverks förhållanden. Du kan ansluta till PowerShell-gränssnittet på enheten och använda `Test-HcsKubernetesStatus` kommandot för att kontrol lera enhetens nätverks beredskap.
    - **Logg insamling** som leder till förbättrad fel sökning. 
    - **Aviserings infrastruktur** som gör att du kan identifiera IP-konflikter för beräknings-IP-adresser. 
    - **Blanda arbets belastning** för Kubernetes och lokal Azure Resource Manager. 

- **Proaktiv loggning som standard** – från och med den här versionen är proaktiv logg samling aktiverat som standard på enheten. Den här funktionen gör att Microsoft kan samla in loggar proaktivt baserat på systemets hälso indikatorer för att effektivt felsöka eventuella problem med enheten. Mer information finns i [proaktiv logg insamling på enheten](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Problem som korrigeras i 2103-versionen

I följande tabell visas de problem som har angivits i tidigare versioner och åtgärd ATS i den aktuella versionen.

| Nej. | Funktion | Problem | 
| --- | --- | --- |
|**1.**|Kubernetes |Edge container Registry fungerar inte när webbproxy är aktiverat.|
|**2.**|Kubernetes |Edge container Registry fungerar inte med IoT Edge moduler.| 

## <a name="known-issues-in-2103-release"></a>Kända problem i 2103-versionen

Följande tabell innehåller en sammanfattning av kända problem i 2103-versionen.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
|**1.**|Förhandsgranskningsfunktioner |I den här versionen har följande funktioner: lokala Azure Resource Manager, virtuella datorer, moln hantering av virtuella datorer, Kubernetes Cloud Management, Azure Arc Enabled Kubernetes, VPN för Azure Stack Edge Pro R och Azure Stack Edge Mini R, multi-process service (MPS) för Azure Stack Edge Pro GPU – är alla tillgängliga i för hands versionen.  |Dessa funktioner är allmänt tillgängliga i senare versioner. |
|**2.**|GPU-virtuella datorer |Före den här versionen hanterade GPU VM-livscykeln inte i uppdaterings flödet. När du uppdaterar till 2103-versionen stoppas därför GPU-VM: ar inte automatiskt under uppdateringen. Du måste manuellt stoppa de virtuella GPU-datorerna med en `stop-stayProvisioned` flagga innan du uppdaterar enheten. Mer information finns i [pausa eller stänga av den virtuella datorn](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Alla GPU-datorer som hålls igång före uppdateringen startas efter uppdateringen. I dessa instanser avslutas inte de arbets belastningar som körs på de virtuella datorerna på ett smidigt sätt. Och de virtuella datorerna kan bli i ett olämpligt tillstånd efter uppdateringen. <br>Alla GPU-datorer som har stoppats via `stop-stayProvisioned` innan uppdateringen startas automatiskt efter uppdateringen. <br>Om du stoppar de virtuella GPU-datorerna via Azure Portal måste du starta den virtuella datorn manuellt efter uppdateringen av enheten.| Om du kör virtuella GPU-datorer med Kubernetes kan du stoppa GPU-datorerna direkt före uppdateringen. <br>När de virtuella datorerna i GPU: erna stoppas tar Kubernetes över de GPU: er som användes ursprungligen av virtuella datorer. <br>De längre GPU-datorerna är i stoppat tillstånd, desto högre är de chanser som Kubernetes tar över GPU: er. |
|**3.**|Anpassat skript för VM-tillägg |Det finns ett känt problem i de virtuella Windows-datorer som har skapats i en tidigare version och enheten har uppdaterats till 2103. <br> Om du lägger till ett anpassat skript tillägg på de här virtuella datorerna kommer Windows-gäst agenten för virtuella datorer (endast version 2.7.41491.901) att fastna i uppdateringen som gör att distributionen av tillägget blir timeout. | Så här kan du lösa problemet: <ul><li> Anslut till den virtuella Windows-datorn med Remote Desktop Protocol (RDP). </li><li> Kontrol lera att `waappagent.exe` körs på datorn: `Get-Process WaAppAgent` . </li><li> Starta om `waappagent.exe` tjänsten om den inte körs `rdagent` : `Get-Service RdAgent` \| `Restart-Service` . Vänta i 5 minuter.</li><li> `waappagent.exe`Stoppa processen när den körs `WindowsAzureGuest.exe` . </li><li>När du har avslutat processen börjar processen köras igen med den nyare versionen.</li><li>Kontrol lera att den virtuella Windows-2.7.41491.971 med det här kommandot: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Konfigurera anpassat skript tillägg på virtuell Windows-dator](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Multi-process service (MPS) |När enhetens program vara och Kubernetes-klustret har uppdaterats behålls inte produktions inställningen för arbets belastningarna.   |Återaktivera [MPS](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) och distribuera om de arbets belastningar som använde MPS. |


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
|**30.**|Kubernetes-instrumentpanel | *Https* -slutpunkten för Kubernetes-instrumentpanelen med SSL-certifikat stöds inte. | |
|**22.2.**|Kubernetes |Kubernetes stöder inte ":" i miljö variabel namn som används av .NET-program. Detta krävs också för Event Grid IoT Edge-modulen för att fungera på Azure Stack Edge-enhet och andra program. Mer information finns i [ASP.net Core-dokumentation](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Ersätt ":" med dubbel under streck. Mer information finns i [Kubernetes-problem](https://github.com/kubernetes/kubernetes/issues/53201)|
|**23.** |Azure-båg + Kubernetes-kluster |När resursen `yamls` tas bort från git-lagringsplatsen tas som standard inte de motsvarande resurserna bort från Kubernetes-klustret.  |För att tillåta borttagning av resurser när de tas bort från git-lagringsplatsen, anges `--sync-garbage-collection` i Arc-OperatorParams. Mer information finns i [ta bort en konfiguration](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.1.**|NFS |Program som använder NFS-resurser monteras på enheten för att skriva data ska använda exklusiv skrivning. Detta säkerställer att skrivningarna skrivs till disken.| |
|**25.1.**|Beräknings konfiguration |Beräknings konfiguration Miss lyckas i nätverkskonfigurationer där gatewayer eller växlar eller routrar svarar på ARP-begäranden (Address Resolution Protocol) för system som inte finns i nätverket.| |
|**26.**|Compute och Kubernetes |Om Kubernetes konfigureras först på din enhet anlitar den alla tillgängliga GPU: er. Därför är det inte möjligt att skapa Azure Resource Manager virtuella datorer med GPU: er när du har konfigurerat Kubernetes. |Om enheten har 2 GPU: er kan du skapa en virtuell dator som använder GPU: n och sedan konfigurera Kubernetes. I det här fallet kommer Kubernetes att använda de återstående tillgängliga 1 GPU: n. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Nästa steg

- [Uppdatera enheten](azure-stack-edge-gpu-install-update.md)
