---
title: Oracle-lösningar på virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om konfigurationer som stöds och begränsningar för avbildningar av virtuella Oracle-datorer på Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 2f34e0bb3c4abcf4efba807f95decd798bbc1f86
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669064"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>VIRTUELLA Oracle-avbildningar och deras distribution på Microsoft Azure

Den här artikeln beskriver information om Oracle-lösningar baserade på avbildningar av virtuella datorer som publicerats av Oracle på Azure Marketplace. Om du är intresse rad av program lösningar över molnet med Oracle Cloud Infrastructure, se [Oracle-programlösningar som integrerar Microsoft Azure och Oracle Cloud Infrastructure](oracle-oci-overview.md).

Om du vill hämta en lista över tillgängliga avbildningar kör du följande kommando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Från och med juni 2020 finns följande avbildningar:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

De här avbildningarna betraktas som "ta med din egen licens" och så att du bara debiteras för beräknings-, lagrings-och nätverks kostnader som uppstår genom att köra en virtuell dator.  Vi förutsätter att du är korrekt licensierad till att använda Oracle-programvara och att du har ett aktuellt support avtal på plats med Oracle. Oracle har garanterat licens mobilitet från lokal plats till Azure. Se publicerad [Oracle och Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) -anteckning för mer information om licens mobilitet.

Individer kan också välja att basera sina lösningar på en anpassad avbildning som de skapar från grunden i Azure eller ladda upp en anpassad avbildning från sin lokala miljö.

## <a name="oracle-database-vm-images"></a>VM-avbildningar för Oracle-databasen

Oracle stöder körning av Oracle Database 12,1 och högre standard-och Enterprise-versioner i Azure på avbildningar av virtuella datorer baserat på Oracle Linux.  För att få bästa möjliga prestanda för produktions arbets belastningar för Oracle Database på Azure, måste du se till att du har rätt storlek på den virtuella dator avbildningen och använder Premium SSD eller Ultra SSD Managed Disks. För instruktioner om hur du snabbt får en Oracle Database igång i Azure med hjälp av den virtuella Oracle-avbildningen, [provar du genom gången Oracle Database snabb start](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Konfigurations alternativ för anslutna diskar

Anslutna diskar är beroende av Azure Blob Storage-tjänsten. Varje standard disk kan ha en teoretisk max på cirka 500 indata/utdata-åtgärder per sekund (IOPS). Vårt erbjudande för Premium disk är att föredra för databas arbets belastningar med höga prestanda och kan få upp till 5000 IOps per disk. Du kan använda en enskild disk om den uppfyller dina prestanda behov. Du kan dock förbättra den effektiva IOPS-prestandan om du använder flera anslutna diskar, sprida databas data över dem och sedan använda hantering av Oracle automatisk lagring (ASM). Se [Översikt över Oracle Automatic Storage](https://www.oracle.com/technetwork/database/index-100339.html) för mer Oracle ASM-detaljerad information. Ett exempel på hur du installerar och konfigurerar Oracle ASM på en Linux Azure VM finns i själv studie kursen [Installera och konfigurera Oracle automatisk lagrings hantering](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Konfigurations alternativ för delad lagring

Azure NetApp Files har utformats för att uppfylla kärn kraven för att köra arbets belastningar med höga prestanda som databaser i molnet, och ger

- Azure Native Shared NFS-lagrings tjänst för att köra Oracle-arbetsbelastningar antingen via den ursprungliga virtuella datorn NFS-klienten eller Oracle dNFS
- Skalbara prestanda nivåer som återspeglar det verkliga utbudet av IOPS-krav
- Låg latens
- Hög tillgänglighet, hög hållbarhet och hanterbarhet i skala, vanligt vis efter verksamhets kritiska företags arbets belastningar (t. ex. SAP och Oracle)
- Snabb och effektiv säkerhets kopiering och återställning för att uppnå de mest aggressiva RTO och återställnings service avtalet

Dessa funktioner är möjliga eftersom Azure NetApp Files baseras på NetApp® ONTAP® alla Flash-system som körs i Azure Data Center-miljön, som en Azure Native-tjänst. Resultatet är en idealisk teknik för databas lagring som kan tillhandahållas och konsumeras precis som andra alternativ för Azure Storage. Mer information om hur du distribuerar och får åtkomst till Azure NetApp Files NFS-volymer finns i [Azure NetApp Files-dokumentationen](../../../azure-netapp-files/index.yml) . Se [metod tips för Oracle i Azure-distribution med hjälp av Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) för bästa praxis för att distribuera en Oracle-databas på Azure NetApp Files.

## <a name="licensing-oracle-database--software-on-azure"></a>Licensierings Oracle Database & program vara på Azure

Microsoft Azure är en godkänd moln miljö för att köra Oracle Database. Tabellen för Oracle Core Factor kan inte användas när du licensierar Oracle-databaser i molnet. I stället kan du, när du använder virtuella datorer med Hyper-Threading teknik som är aktive rad för Enterprise Edition-databaser, räkna två virtuella processorer som likvärdiga med en Oracle processor licens om hyperthreading är aktiverat (enligt vad som anges i princip dokumentet). Du hittar information om principen [här](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Oracle-databaser kräver vanligt vis mer minne och IO. Därför rekommenderas [Minnesoptimerade virtuella datorer](../../sizes-memory.md) för dessa arbets belastningar. För att optimera dina arbets belastningar rekommenderas [begränsade kärn virtuella processorer](../../constrained-vcpu.md) för Oracle Database arbets belastningar som kräver hög minnes-, lagrings-och I/O-bandbredd, men inte med ett högt antal kärnor.

När du migrerar Oracle-programvara och arbets belastningar från en lokal plats till Microsoft Azure tillhandahåller Oracle License Mobility såsom anges i [vanliga frågor och svar om Oracle på Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="high-availability-and-disaster-recovery-considerations"></a>Överväganden för hög tillgänglighet och haveri beredskap

När du använder Oracle-databaser i Azure ansvarar du för att implementera en lösning för hög tillgänglighet och haveri beredskap för att undvika avbrott.

Hög tillgänglighet och haveri beredskap för Oracle Database Enterprise Edition (utan att lita på Oracle RAC) kan uppnås på Azure med hjälp av [data Guard, Active data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)eller [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), med två databaser på två separata virtuella datorer. Båda de virtuella datorerna bör finnas i samma [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) för att säkerställa att de kan komma åt varandra via den privata, BEständiga IP-adressen.  Dessutom rekommenderar vi att du placerar de virtuella datorerna i samma tillgänglighets uppsättning för att tillåta Azure att placera dem i separata fel domäner och uppgraderings domäner. Vill du ha GEO-redundans ställer du in de två databaserna för replikering mellan två olika regioner och ansluter de två instanserna med en VPN Gateway.

I självstudien [implementera Oracle data Guard i Azure](configure-oracle-dataguard.md) får du stegvisa anvisningar genom den grundläggande installations proceduren i Azure.  

Med Oracle data Guard kan hög tillgänglighet uppnås med en primär databas på en virtuell dator, en sekundär databas (vänte läge) i en annan virtuell dator och enkelriktad replikering som kon figurer ATS mellan dem. Resultatet är Läs behörighet till kopian av databasen. Med Oracle GoldenGate kan du konfigurera dubbelriktad replikering mellan de två databaserna. Information om hur du konfigurerar en lösning med hög tillgänglighet för dina databaser med hjälp av dessa verktyg finns i dokumentationen om [Active data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) och [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) på Oracle-webbplatsen. Om du behöver Läs-och Skriv behörighet till kopian av databasen kan du använda [Oracle Active data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

I själv studie kursen [implementeras Oracle GoldenGate i Azure](configure-oracle-golden-gate.md) med hjälp av den grundläggande installations proceduren i Azure.

Förutom att ha en HA-och DR-lösning som är konstruerad i Azure bör du ha en säkerhets kopierings strategi på plats för att återställa databasen. I självstudien om [säkerhets kopiering och återställning av en Oracle Database](./oracle-overview.md) går vi igenom den grundläggande proceduren för att upprätta en konsekvent säkerhets kopia.

## <a name="support-for-jd-edwards"></a>Stöd för JD-Edwards

Enligt Oracle support NOTE [dokument-ID 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne-versionerna 9,2 och senare, stöds i **alla offentliga moln** som uppfyller deras speciella `Minimum Technical Requirements` (MTR).  Du måste skapa anpassade avbildningar som uppfyller MTR-specifikationerna för operativ system-och program varu kompatibilitet.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Den virtuella datorn för Oracle WebLogic Server erbjuder

Oracle och Microsoft samarbetar för att ta WebLogic-Server till Azure Marketplace i form av en samling med Azure Application erbjudanden.  Dessa erbjudanden beskrivs i artikeln [Oracle WebLogic Server Azure-program](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Avbildningar av virtuella datorer i Oracle WebLogic-Server

- **Klustring stöds endast på Enterprise Edition.** Du är licensierad att endast använda WebLogic-kluster när du använder Enterprise-versionen av Oracle WebLogic Server. Använd inte kluster med Oracle WebLogic Server Standard Edition.
- **UDP-multicast stöds inte.** Azure har stöd för UDP-unicast, men inte multicasting eller Broadcasting. Oracle WebLogic Server kan förlita sig på funktioner i Azure UDP unicast. För bästa resultat som förlitar sig på UDP unicast, rekommenderar vi att kluster storleken för WebLogic hålls statisk eller hålls kvar utan fler än 10 hanterade servrar.
- **Oracle WebLogic-servern förväntar sig att offentliga och privata portar är samma för T3-åtkomst (till exempel när du använder företags-JavaBeans).** Överväg ett scenario med flera nivåer där ett Service Layer (EJB)-program körs på ett Oracle WebLogic Server-kluster som består av två eller flera virtuella datorer i ett virtuellt nätverk med namnet *SLWLS*. Klient nivån finns i ett annat undernät i samma virtuella nätverk, med ett enkelt Java-program som försöker anropa EJB i tjänst skiktet. Eftersom det är nödvändigt att belastningsutjämna tjänst lagret måste en offentlig belastningsutjämnad slut punkt skapas för de virtuella datorerna i Oracle WebLogic-serverkluster. Om den privata port som du anger skiljer sig från den offentliga porten (till exempel 7006:7008) uppstår ett fel som följande inträffar:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Detta beror på att för en fjärr-T3-åtkomst förväntar sig Oracle WebLogic-servern att belastningsutjämnarens port och den WebLogic-hanterade Server porten är densamma. I föregående fall ansluter klienten till port 7006 (belastnings Utjämnings porten) och den hanterade servern lyssnar på 7008 (den privata porten). Den här begränsningen gäller endast för T3-åtkomst, inte HTTP.

   Undvik det här problemet genom att använda någon av följande lösningar:

- Använd samma privata och offentliga port nummer för belastningsutjämnade slut punkter som är dedikerade till T3-åtkomst.
- Ta med följande JVM-parameter när du startar Oracle WebLogic-servern:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Relaterad information finns i KB-artikel **860340,1** på <https://support.oracle.com> .

- **Begränsningar för dynamisk kluster-och belastnings utjämning.** Anta att du vill använda ett dynamiskt kluster i Oracle WebLogic-servern och exponera det genom en enda offentlig belastningsutjämnad slut punkt i Azure. Detta kan göras så länge du använder ett fast port nummer för var och en av de hanterade servrarna (inte dynamiskt tilldelade från ett intervall) och inte startar fler hanterade servrar än det finns datorer som administratören spårar. Det finns inte fler än en hanterad server per virtuell dator. Om konfigurationen leder till att fler Oracle WebLogic-servrar startas än det finns virtuella datorer (det vill säga där flera Oracle WebLogic-instanser delar samma virtuella dator), är det inte möjligt för mer än en av dessa instanser av Oracle WebLogic-servrar att binda till ett angivet port nummer. De andra på den virtuella datorn fungerar inte.

   Om du konfigurerar administrations servern så att den automatiskt tilldelar unika port nummer till dess hanterade servrar, är det inte möjligt att aktivera belastnings utjämning eftersom Azure inte stöder mappning från en enda offentlig port till flera privata portar, vilket krävs för den här konfigurationen.
- **Flera instanser av Oracle WebLogic-Server på en virtuell dator.** Beroende på din distributions krav kan du överväga att köra flera instanser av Oracle WebLogic Server på samma virtuella dator, om den virtuella datorn är tillräckligt stor. Till exempel på en virtuell dator med medelhög storlek, som innehåller två kärnor, kan du välja att köra två instanser av Oracle WebLogic Server. Vi rekommenderar dock fortfarande att du undviker att införa enskilda felpunkter i din arkitektur, vilket är fallet om du bara använde en virtuell dator som kör flera instanser av Oracle WebLogic Server. Att använda minst två virtuella datorer kan vara en bättre metod och varje virtuell dator kan sedan köra flera instanser av Oracle WebLogic Server. Varje instans av Oracle WebLogic-servern kan fortfarande vara en del av samma kluster. Men det är för närvarande inte möjligt att använda Azure för att belastningsutjämna slut punkter som exponeras av sådana Oracle WebLogic Server-distributioner inom samma virtuella dator, eftersom Azure Load Balancer kräver att de belastningsutjämnade servrarna distribueras mellan unika virtuella datorer.

## <a name="oracle-jdk-virtual-machine-images"></a>Avbildningar av virtuella Oracle JDK-datorer

- **JDK 6 och 7 de senaste uppdateringarna.** Även om vi rekommenderar att du använder den senaste offentliga, stödda versionen av Java (för närvarande Java 8), gör Azure också att JDK 6-och 7-avbildningar är tillgängliga. Detta är avsett för äldre program som ännu inte är klara att uppgraderas till JDK 8. Även om uppdateringar av tidigare JDK-avbildningar kanske inte längre är tillgängliga för allmänheten, med tanke på Microsoft partner med Oracle, är de JDK 6-och 7-avbildningar som tillhandahålls av Azure avsedda att innehålla en nyare icke-offentlig uppdatering som normalt erbjuds av Oracle till endast en SELECT-grupp av Oracle-kunder som stöds. Nya versioner av JDK-avbildningarna blir tillgängliga med tiden med uppdaterade versioner av JDK 6 och 7.

   JDK som är tillgängliga i JDK 6-och 7-avbildningarna och de virtuella datorer och avbildningar som härletts från dem kan bara användas i Azure.
- **64-bitars JDK.** De virtuella dator avbildningarna för Oracle WebLogic-servern och de virtuella Oracle JDK-avbildningarna som tillhandahålls av Azure innehåller 64-bitars versionerna av både Windows Server och JDK.

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över aktuella Oracle-lösningar baserade på avbildningar av virtuella datorer i Microsoft Azure. Nästa steg är att distribuera din första Oracle-databas på Azure.

> [!div class="nextstepaction"]
> [Skapa en Oracle-databas på Azure](oracle-database-quick-create.md)
