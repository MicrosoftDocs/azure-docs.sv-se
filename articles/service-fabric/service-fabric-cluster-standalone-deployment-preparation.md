---
title: Förberedelse av fristående kluster distribution
description: Dokumentation som rör förberedelse av miljön och skapandet av kluster konfigurationen som ska övervägas innan du distribuerar ett kluster som är avsett för hantering av produktions belastningar.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 18b8b0ce8c0e877bf9dd274596b19f85b1febe12
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790960"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planera och förbereda din Service Fabric fristående kluster distribution

<a id="preparemachines"></a>Utför följande steg innan du skapar klustret.

## <a name="plan-your-cluster-infrastructure"></a>Planera din kluster infrastruktur
Du håller på att skapa ett Service Fabric-kluster på datorer som du "äger", så att du kan bestämma vilka typer av fel som du vill att klustret ska överleva. Behöver du till exempel separata strömförsörjnings linjer eller Internet-anslutningar som tillhandahålls till de här datorerna? Tänk också på den fysiska säkerheten för de här datorerna. Var finns datorerna och vem behöver ha åtkomst till dem? När du har fattat dessa beslut kan du logiskt mappa datorerna till olika fel domäner (se nästa steg). Infrastruktur planeringen för produktions kluster är mer engagerad än för test kluster.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Fastställa antalet fel domäner och uppgraderings domäner
En [ *feldomän* (fd)](service-fabric-cluster-resource-manager-cluster-description.md) är en fysisk felkod som är direkt relaterad till den fysiska infrastrukturen i data centren. En feldomän består av maskin varu komponenter (datorer, växlar, nätverk med mera) som delar en enskild felpunkt. Även om det inte finns någon 1:1-mappning mellan fel domäner och rack, kan du lösa problemet genom att betrakta varje rack som en feldomän.

När du anger fd i ClusterConfig.jspå, kan du välja namnet för varje FD. Service Fabric stöder hierarkisk fd, så att du kan återspegla din infrastruktur sto pol Ogin i dem.  Till exempel är följande fd giltiga:

* "Faulydomain": "fd:/Room1/Rack1/machine1"
* "Faulydomain": "fd:/FD1"
* "Faulydomain": "fd:/Room1/Rack1/PDU1/M1"

En *uppgraderings domän* (UD) är en logisk enhet med noder. Under Service Fabric dirigerade uppgraderingar (antingen en program uppgradering eller en kluster uppgradering) tas alla noder i en UD nedåt för att utföra uppgraderingen medan noder i andra UDs är tillgängliga för att betjäna begär Anden. De uppgraderingar av inbyggd program vara som du utför på dina datorer följer inte UDs, så du måste göra dem en dator i taget.

Det enklaste sättet att tänka på dessa begrepp är att överväga fd som enhet för oplanerat haveri och UDs som enhet för planerat underhåll.

När du anger UDs i ClusterConfig.jspå, kan du välja namnet för varje UD. Följande namn är till exempel giltiga:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "blå"

Mer detaljerad information om fd och UDs finns i [beskriva ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md).

Ett kluster i produktionen bör omfatta minst tre fd för att stödjas i en produktions miljö, om du har fullständig kontroll över underhållet och hanteringen av noderna, det vill säga att du ansvarar för att uppdatera och ersätta datorer. För kluster som körs i miljöer (det vill säga Amazon Web Services VM-instanser) där du inte har fullständig kontroll över datorerna bör du ha minst fem fd i klustret. Varje FD kan ha en eller flera noder. Detta är för att förhindra problem som orsakas av dator uppgraderingar och uppdateringar, beroende på deras tids inställning, kan störa körningen av program och tjänster i kluster.

## <a name="determine-the-initial-cluster-size"></a>Fastställ storleken på den ursprungliga klustret

Normalt bestäms antalet noder i klustret utifrån dina affärs behov, det vill säga hur många tjänster och behållare som körs i klustret och hur många resurser du behöver för att hantera dina arbets belastningar. För produktions kluster rekommenderar vi att du har minst fem noder i klustret och spänner över 5 fd. Om du däremot har fullständig kontroll över dina noder och kan omfatta tre fd, bör tre noder också utföra jobbet.

Test kluster som kör tillstånds känsliga arbets belastningar ska ha tre noder, medan test kluster bara kör tillstånds lösa arbets belastningar behöver bara en nod. Det bör även noteras att du kan ha mer än en nod på en specifik dator i utvecklings syfte. I en produktions miljö Service Fabric bara stöd för en nod per fysisk eller virtuell dator.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Förbereda de datorer som ska fungera som noder

Här är rekommenderade specifikationer för datorer i ett Service Fabric kluster:

* Minst 16 GB RAM-minne
* Minst 40 GB ledigt disk utrymme
* En processor på 4 kärnor eller större
* Anslutning till ett säkert nätverk eller nätverk för alla datorer
* Windows Server OS installerat (giltiga versioner: 2012 R2, 2016, 1709 eller 1803). Service Fabric version 6.4.654.9590 och senare stöder också Server 2019 och 1809.
* [.NET Framework 4.5.1 eller senare](https://www.microsoft.com/download/details.aspx?id=40773), fullständig installation
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell)
* [RemoteRegistry-tjänsten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) ska köras på alla datorer
* **Service Fabric installations enhet måste vara NTFS-filsystem**
* **Prestanda loggar för Windows-tjänster *&-aviseringar* och *Windows-händelseloggen* måste [vara aktiverade](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))**.

> [!IMPORTANT]
> Kluster administratören som distribuerar och konfigurerar klustret måste ha [administratörs behörighet](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) på var och en av datorerna. Du kan inte installera Service Fabric på en domänkontrollant.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Hämta det fristående Service Fabric-paketet för Windows Server
[Hämta länk-Service Fabric fristående paket – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) och packa upp paketet, antingen till en distributions dator som inte ingår i klustret eller till en av de datorer som ska ingå i klustret.

## <a name="modify-cluster-configuration"></a>Ändra kluster konfiguration
Om du vill skapa ett fristående kluster måste du skapa en fristående kluster konfiguration ClusterConfig.jspå en fil som beskriver klusterets specifikation. Du kan basera konfigurations filen på mallarna som finns på länken nedan. <br>
[Fristående klusterkonfigurationer](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Mer information om avsnitten i den här filen finns i [konfigurations inställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md).

Öppna en av ClusterConfig.jspå filer från paketet som du laddade ned och ändra följande inställningar:

| **Konfigurations inställning** | **Beskrivning** |
| --- | --- |
| **NodeTypes** |Med nodtyper kan du avgränsa klusternoderna i olika grupper. Ett kluster måste ha minst en NodeType. Alla noder i en grupp har följande gemensamma egenskaper: <br> **Namn** – det här är namnet på nodtypen. <br>**Slut punkts portar** – dessa är olika namngivna slut punkter (portar) som är associerade med den här nodtypen. Du kan använda vilket port nummer som helst, så länge de inte står i konflikt med något annat i det här manifestet och som inte redan används av andra program som körs på datorn/den virtuella datorn. <br> **Placerings egenskaper** – dessa beskriver egenskaper för den här nodtypen som du använder som placerings begränsningar för system tjänsterna eller dina tjänster. De här egenskaperna är användardefinierade nyckel/värde-par som tillhandahåller extra metadata för en viss nod. Exempel på Egenskaper för Node är om noden har en hård disk eller ett grafik kort, antalet spindlar på hård disken, kärnor och andra fysiska egenskaper. <br> **Kapacitet** – nodens kapacitet definierar namnet och mängden för en viss resurs som en viss nod har till gång till för användning. En nod kan till exempel definiera att den har kapacitet för ett Mät värde med namnet "MemoryInMb" och att den har 2048 MB tillgängligt som standard. Dessa kapaciteter används vid körning för att säkerställa att tjänster som kräver särskilda mängder resurser placeras på noderna som har dessa resurser tillgängliga i de nödvändiga beloppen.<br>**IsPrimary** – om du har mer än en NodeType definierad ser du till att endast en är inställd på primär med värdet *True*, vilket är den plats där system tjänsterna körs. Alla andra nodtyper måste anges till värdet *false* |
| **Noder** |Detta är information om var och en av noderna som ingår i klustret (nodtyp, nodnamn, IP-adress, feldomän och uppgraderings domän för noden). De datorer som du vill att klustret ska skapas på måste anges här med deras IP-adresser. <br> Om du använder samma IP-adress för alla noder skapas ett kluster med en ruta, som du kan använda i test syfte. Använd inte kluster med en enda ruta för att distribuera arbets belastningar för produktion. |

När konfigurations inställningarna för klustret har kon figurer ATS för miljön kan den testas mot kluster miljön (steg 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Miljökonfiguration

När en kluster administratör konfigurerar ett Service Fabric fristående kluster, måste miljön konfigureras med följande kriterier: <br>
1. Användaren som skapar klustret bör ha säkerhets behörighet på administratörs nivå för alla datorer som listas som noder i kluster konfigurations filen.
2. Datorn från vilken klustret skapas, samt varje klusternod måste:
   * Låta Service Fabric SDK avinstalleras
   * Kör Service Fabric Runtime har avinstallerats
   * Aktivera Windows-brandväggen (MPSSVC)
   * Aktivera Remote Registry-tjänsten (fjär registret)
   * Ha fildelning (SMB) aktiverat
   * Har nödvändiga portar öppna, baserat på kluster konfigurations portar
   * Har nödvändiga portar öppnats för Windows SMB och tjänsten Remote Registry: 135, 137, 138, 139 och 445
   * Ha nätverks anslutning till varandra
3. Ingen av klusternoderna ska vara en domänkontrollant.
4. Om det kluster som ska distribueras är ett säkert kluster, verifierar du nödvändiga säkerhets krav på plats och är korrekt konfigurerade för konfigurationen.
5. Om kluster datorerna inte är Internet-tillgängliga, ställer du in följande i kluster konfigurationen:
   * Inaktivera telemetri: under *Egenskaper* ange *"enableTelemetry": falskt*
   * Inaktivera automatisk hantering av infrastruktur resurser & meddelanden som den aktuella kluster versionen närmar sig slutet på supporten: under *Egenskaper* set *"fabricClusterAutoupgradeEnabled": false*
   * Alternativt, om nätverks Internet åtkomst är begränsad till allowlisted-domäner, krävs nedanstående domäner för automatisk uppgradering: go.microsoft.com download.microsoft.com

6. Ange lämpliga Service Fabric Antivirus undantag:

| **Undantagna Antivirus kataloger** |
| --- |
| Program\Microsoft Service Fabric |
| FabricDataRoot (från kluster konfiguration) |
| FabricLogRoot (från kluster konfiguration) |

| **Undantagna Antivirus processer** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Verifiera miljö med TestConfiguration-skript
Du hittar TestConfiguration.ps1-skriptet i det fristående paketet. Den används som en Best Practices Analyzer för att validera några av kriterierna ovan och ska användas som en Sanity för att kontrol lera om ett kluster kan distribueras i en viss miljö. Om det uppstår ett fel, se listan under [miljö inställningar](service-fabric-cluster-standalone-deployment-preparation.md) för fel sökning.

Det här skriptet kan köras på alla datorer som har administratörs åtkomst till alla datorer som listas som noder i kluster konfigurations filen. Datorn som skriptet körs på behöver inte vara en del av klustret.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

För närvarande verifierar den här konfigurations testnings modulen säkerhets konfigurationen så att detta måste göras oberoende.

> [!NOTE]
> Vi gör kontinuerligt förbättringar för att göra den här modulen mer robust, så om det uppstår ett fel eller saknat fall som du anser inte har fångats av TestConfiguration, så låt oss ta reda på våra [Support kanaler](./service-fabric-support.md).
>
>

## <a name="next-steps"></a>Nästa steg
* [Skapa ett fristående kluster som körs på Windows Server](service-fabric-cluster-creation-for-windows-server.md)
