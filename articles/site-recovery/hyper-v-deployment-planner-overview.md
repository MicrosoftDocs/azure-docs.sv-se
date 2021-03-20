---
title: Distributions planeraren för haveri beredskap för Hyper-V med Azure Site Recovery
description: Lär dig mer om den Distributionshanteraren för Azure Site Recovery Hyper-V haveri beredskap till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86528952"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Om Distributionshanteraren för Azure Site Recovery för haveri beredskap för Hyper-V till Azure

Den här artikeln är användarhandboken för Distributionshanteraren för Azure Site Recovery för produktionsdistribution av Hyper-V till Azure.

Innan du börjar skydda virtuella Hyper-V-datorer med hjälp av Site Recovery måste du allokera tillräckligt mycket bandbredd sett till din dagliga dataomsättningsfrekvens så att du når önskat RPO-mål (återställningspunkt), och allokera tillräckligt med ledigt lagringsutrymme för varje volym med Hyper-V-lagring lokalt.

Du måste också skapa rätt typ och antal Azure-mållagringskonton. Du skapar antingen Standard Storage- eller Premium Storage-konton, och väger in tillväxt på källproduktionsservrarna på grund av ökad användning under en viss tid. Du väljer lagringstyp per virtuell dator baserat på arbetsbelastningens egenskaper, exempelvis läs- och skrivbehörighet, i/o-åtgärder per sekund (IOPS) eller dataomsättningen, och Azure Site Recovery-begränsningarna. 

Distributionshanteraren för Azure Site Recovery är ett kommandoradsverktyg för både haveriberedskapsscenarier för Hyper-V till Azure och VMware till Azure. Du kan profilera dina virtuella Hyper-V-datorer på flera olika Hyper-V-värdar via fjärranslutning med det här verktyget (utan att produktionen påverkas alls), så att du får en uppfattning om vilken bandbredd och vilket Azure-lagringsutrymme som kommer att behövas för replikering och redundanstest. Du kan köra verktyget utan att installera några Azure Site Recovery-komponenter lokalt. För att få korrekta resultat för dataflödet rekommenderar vi dock att du kör planeringsverktyget på en Windows Server som har samma maskinvarukonfiguration som en av de Hyper-V-servrar du kommer att använda när du ska aktivera haveriberedskapen till Azure. 

Du kan se följande information i verktyget:

**Utvärdering av kompatibilitet**

* En utvärdering av om den virtuella datorn stöds, baserat på antal diskar, diskstorlek, IOPS, dataomsättning och några egenskaper hos den virtuella datorn.

**Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål**

* Beräknad nätverksbandbredd som krävs för deltareplikering
* Dataflödet som Azure Site Recovery kan få från lokala datorer till Azure
* Möjligt återställningspunktmål för en viss bandbredd
* Påverkan på det önskade återställningspunktmålet vid lägre brandbredder.

    
**Krav på infrastruktur för Azure**

* Typ av lagringsutrymme (Standard Storage- eller Premium Storage-konto) som krävs för varje virtuell dator
* Totalt antal Standard Storage- och Premium Storage-konton som ska konfigureras för replikering
* Namnförslag för lagringskonton baserade på riktlinjer för Azure Storage
* Lagringskontots placering för alla virtuella datorer
* Antalet Azure-kärnor som ska etableras innan redundanstest/redundansväxling för prenumerationen
* Den rekommenderade storleken på den virtuella Azure-datorn för varje lokal virtuell dator

**Krav på lokal infrastruktur**
* Den mängd ledigt lagringsutrymme som krävs för varje Hyper-V-lagringsvolym för en lyckad initial replikering och deltareplikering, för att garantera att replikeringen av virtuella datorer inte orsakar några oönskade driftstopp för dina produktionsprogram.
* Maximal kopieringsfrekvens måste ställas in för Hyper-V-replikering

**Inledande vägledning till batchreplikering** 
* Antal VM-batchar som ska användas för skyddet
* Lista med virtuella datorer i varje batch
* Ordningen som varje batch ska skyddas i
* Uppskattad tidsåtgång för fullständig inledande replikering av varje batch

**Beräknad DR-kostnad för Azure**
* Uppskattad total kostnad för haveriberedskap till Azure: databearbetning, lagring, nätverk och Azure Site Recovery-licenskostnader
* Detaljerad analys av kostnader per virtuell dator



>[!IMPORTANT]
>
>Eftersom användningen troligtvis ökar med tiden utförs alla föregående verktygsberäkningar med en 30-procentig tillväxtfaktor för arbetsbelastningens egenskaper, och den 95:e percentilen används för alla profileringsmått (läs-/skriv-IOPS, omsättning och så vidare). Båda parametrarna (tillväxtfaktorn och percentilberäkningen) kan konfigureras. Om du vill veta mer om tillväxtfaktor, se avsnittet ”Överväganden för tillväxtfaktorer”. Mer information om percentilvärdet finns i avsnittet ”Percentilvärdet som används för beräkningen”.
>

## <a name="support-matrix"></a>Stödmatris

|**Kategorier** | **VMware till Azure** |**Hyper-V till Azure**|**Azure till Azure**|**Hyper-V till sekundär plats**|**VMware till sekundär plats**
--|--|--|--|--|--
Scenarier som stöds |Ja|Ja|Inga|Ja*|Inga
Version som stöds | vCenter 6,7, 6,5, 6,0 eller 5,5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Konfiguration som stöds|vCenter, ESXi| Hyper-V-kluster, Hyper-V-värd|NA|Hyper-V-kluster, Hyper-V-värd|NA|
Antalet servrar som kan profileras per körningsinstans av Distributionshanteraren för Azure Site Recovery |En enda (virtuella datorer som hör till en vCenter-server eller en ESXi-server kan profileras samtidigt)|Flera (virtuella datorer över flera värdar eller värdkluster kan profileras samtidigt)| NA |Flera (virtuella datorer över flera värdar eller värdkluster kan profileras samtidigt)| NA

* Verktyget är främst avsett för haveriberedskapsscenariot Hyper-V till Azure. För haveriberedskap från Hyper-V till sekundär plats kan det bara användas till att förstå rekommendationer för källsidan, till exempel nätverksbandbredd som krävs, ledigt lagringsutrymme som krävs på varje Hyper-V-källserver samt inledande batchnummer för replikering och batchdefinitioner.  Ignorera Azure-rekommendationer och kostnader i rapporten. Åtgärden Get Throughput gäller dessutom inte för haveriberedskapsscenarion från Hyper-V till sekundär plats.

## <a name="prerequisites"></a>Förutsättningar
Verktyget har tre huvudfaser för Hyper-V: hämta listan med virtuella datorer, utför profilering och generera rapporten. Det finns också ett fjärde alternativ som endast beräknar dataflödet. I följande tabell visas kraven för den server där de olika faserna ska köras:

| Serverkrav | Beskrivning |
|---|---|
|Hämta listan med virtuella datorer, utför profilering och mäta dataflödet |<ul><li>Operativsystem: Microsoft Windows Server 2016 eller Microsoft Windows Server 2012 R2 </li><li>Datorkonfiguration: 8 virtuella processorer, 16 GB RAM-minne, 300 GB hårddisk</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internet åtkomst till Azure (*. blob.core.windows.net) från den här servern, port 443<br>[Detta är valfritt. Du kan välja att tillhandahålla tillgänglig bandbredd under rapportgenerering manuellt.]</li><li>Azure Storage-konto</li><li>Administratörsbehörighet till servern</li><li>Minst 100 GB ledigt diskutrymme (förutsatt 1 000 virtuella datorer med ett medeltal av de tre diskar vardera, profilerade under 30 dagar)</li><li>Den virtuella dator som du kör Distributionshanteraren för Azure Site Recovery från måste läggas till i listan TrustedHosts på alla Hyper-V-servrar.</li><li>Alla Hyper-V-servrar som ska profileras måste läggas till i listan TrustedHosts för den klient-VM från vilken verktyget körs. [Läs mer om att lägga till servrar i listan TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> Verktyget måste köras med administratörsbehörighet från PowerShell eller i kommandotolken på klienten.</ul></ul>|
| Rapportgenerering | En Windows-dator eller Windows Server med Microsoft Excel 2013 eller senare |
| Användarbehörigheter | Administratörskonto för åtkomst till Hyper-V kluster/Hyper-V-värd under åtgärden att hämta listan med virtuella datorer samt profilering.<br>Alla värdar som ska profileras måste ha ett domänadministratörskonto med samma autentiseringsuppgifter, som användarnamn och lösenord
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Steg för att lägga till servrar i listan TrustedHosts
1. Den virtuella dator som verktyget ska köras från måste ha alla värdar som ska profileras i listan TrustedHosts. När du ska lägga till klienten i listan TrustedHosts kör du följande kommando från en utökad PowerShell-session på den virtuella datorn. Den virtuella datorn kan vara en Windows Server 2012 R2 eller Windows Server 2016. 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. Varje Hyper-V-värd som ska profileras ska ha:

    a. Den virtuella dator som verktyget ska köras från i listan TrustedHosts. Kör följande kommando från en utökad PowerShell-session på Hyper-V-värden:

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. PowerShell-fjärrkommunikation aktiverad.

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Ladda ned och extrahera distributionshanteraren

1.  Ladda ned den senaste versionen av [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
Verktyget är paketerat i en komprimerad mapp. Samma verktyg har stöd för båda haveriberedskapsscenarierna VMware till Azure och Hyper-V till Azure. Du kan använda det här verktyget för scenariot Hyper-V-till sekundär plats också, men då måste du ignorera rekommendationen om Azure-infrastruktur i rapporten.

1.  Kopiera den komprimerade mappen till den Windows Server du vill köra verktyget från. Du kan köra verktyget på en Windows Server 2012 R2 eller Windows Server 2016. Servern måste ha nätverksåtkomst för anslutning till Hyper-V-klustret eller Hyper-V-värden som innehåller de virtuella datorer som ska profileras. Vi rekommenderar att du använder samma maskinvarukonfiguration för den virtuella dator där verktyget ska köras som den Hyper-V-server du vill skydda. En sådan konfiguration garanterar att det uppnådda dataflödet som verktyget rapporterar matchar det faktiska dataflöde som Azure Site Recovery kan uppnå under replikeringen. Dataflödesberäkningen är beroende av den tillgängliga nätverksbandbredden och maskinvarukonfigurationen (processor, lagringsutrymme och så vidare) på servern. Dataflödet beräknas från servern där verktyget körs till Azure. Om servern har en annan maskinvarukonfiguration än Hyper-V-servern kommer verktyget att rapportera ett felaktigt dataflöde.
Den rekommenderade konfigurationen för de virtuella datorerna: 8 virtuella processorer, 16 GB RAM, 300 GB hårddisk.

1.  Extrahera .zip-filen.
Mappen innehåller flera filer och undermappar. Den körbara filen är ASRDeploymentPlanner.exe i den överordnade mappen.

Exempel: Kopiera .zip-filen till enheten E:\ och packa upp den. E:\ASR distributions Planner_v2.3.zip

E:\ASR distributions Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Uppdatera till den senaste versionen av distributionshanteraren

De senaste uppdateringarna sammanfattas i [versions historiken](site-recovery-deployment-planner-history.md)för Deployment Planner.

Om du har en tidigare version av distributionshanteraren gör du något av följande:
 * Om den senaste versionen innehåller inte en profileringskorrigering och profileringen pågår redan på den aktuella versionen av planeringsverktyget fortsätter du profileringen.
 * Om den senaste versionen innehåller en profileringskorrigering rekommenderar vi att du stoppar profileringen av din aktuella version och startar om profileringen med den nya versionen.


  >[!NOTE]
  >
  >När du startar profilering med den nya versionen skickar du samma sökväg för utdatakatalogen så att verktyget lägger till profildata i de befintliga filerna. En fullständig uppsättning profilerade data används för att generera rapporten. Om du skickar en annan utdatakatalog kommer nya filer att skapas och gamla profildata använda inte för att skapa rapporten.
  >
  >Varje ny kapacitetsplanerare är en ackumulerad uppdatering av .zip-filen. Du behöver inte kopiera de senaste filerna till föregående mapp. Du kan skapa och använda en ny mapp.

## <a name="version-history"></a>Versionshistorik
Den senaste versionen av Distributionshanteraren för Azure Site Recoverys verktyget är 2,5.
Se sidan [Distributionshanteraren för Azure Site Recovery versions historik](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) för de korrigeringar som läggs till i varje uppdatering.


## <a name="next-steps"></a>Nästa steg
* [Kör distributions planeraren](./hyper-v-deployment-planner-run.md).
