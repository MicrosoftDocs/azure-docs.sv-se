---
title: Distribuera IBM DB2 pureScale på Azure
description: Lär dig hur du distribuerar en exempel arkitektur som används nyligen för att migrera ett företag från dess IBM DB2-miljö som körs på z/OS till IBM DB2 pureScale på Azure.
author: njray
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 33ff6174d7e5107076dda177731c9daec7e57266
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956153"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Distribuera IBM DB2 pureScale på Azure

Den här artikeln beskriver hur du distribuerar en [exempel arkitektur](ibm-db2-purescale-azure.md) som en företags kund nyligen använt för att migrera från sin IBM DB2-miljö som körs på z/OS till IBM DB2 PureScale på Azure.

För att följa stegen som används för migreringen, se installations skripten i [DB2onAzure](https://aka.ms/db2onazure) -lagringsplatsen på GitHub. Dessa skript baseras på arkitekturen för en typisk OLTP-arbetsbelastning (Online Transaction Processing) i medel stora mängder.

## <a name="get-started"></a>Kom igång

Om du vill distribuera den här arkitekturen laddar du ned och kör deploy.sh-skriptet som finns i [DB2onAzure](https://aka.ms/db2onazure) -lagringsplatsen på GitHub.

Lagrings platsen innehåller också skript för att konfigurera en Grafana-instrumentpanel. Du kan använda instrument panelen för att fråga Prometheus, övervaknings-och aviserings systemet med öppen källkod som ingår i DB2.

> [!NOTE]
> Deploy.sh-skriptet på klienten skapar privata SSH-nycklar och skickar dem till distributions mal len över HTTPS. För större säkerhet rekommenderar vi att du använder [Azure Key Vault](../../../../key-vault/general/overview.md) för att lagra hemligheter, nycklar och lösen ord.

## <a name="how-the-deployment-script-works"></a>Så här fungerar distributions skriptet

Deploy.sh-skriptet skapar och konfigurerar Azure-resurser för den här arkitekturen. Skriptet efterfrågar dig för Azure-prenumerationen och virtuella datorer som används i mål miljön och utför sedan följande åtgärder:

-   Konfigurerar resurs gruppen, det virtuella nätverket och undernät på Azure för installationen.

-   Konfigurerar nätverks säkerhets grupper och SSH för miljön.

-   Konfigurerar flera nätverkskort på både den delade lagringen och de virtuella DB2 pureScale-datorerna.

-   Skapar de virtuella datorerna för delad lagring. Om du använder Lagringsdirigering eller någon annan lagrings lösning, se [Lagringsdirigering översikt](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Skapar den virtuella bygel-datorn.

-   Skapar virtuella DB2 pureScale-datorer.

-   Skapar den virtuella vittnes datorn som DB2 pureScale-pingar. Hoppa över den här delen av distributionen om din version av DB2-pureScale inte kräver något vittne.

-   Skapar en virtuell Windows-dator som ska användas för testning men installerar inte något på den.

Sedan konfigurerar distributions skripten en virtuell iSCSI-storage area network (virtuellt SAN) för delad lagring på Azure. I det här exemplet ansluter iSCSI till det delade lagrings klustret. GlusterFS användes i den ursprungliga kund lösningen. IBM stöder dock inte längre den här metoden. Om du vill underhålla ditt stöd från IBM måste du använda ett iSCSI-kompatibelt fil system som stöds. Microsoft erbjuder Lagringsdirigering (S2D) som ett alternativ.

Den här lösningen ger dig också möjlighet att installera iSCSI-målen som en enda Windows-nod. iSCSI tillhandahåller ett delat block lagrings gränssnitt över TCP/IP som gör att installations proceduren för DB2-pureScale kan använda ett enhets gränssnitt för att ansluta till delad lagring.

Distributions skripten kör dessa generella steg:

1.  Konfigurera ett delat lagrings kluster i Azure. Det här steget omfattar minst två Linux-noder.

2.  Konfigurera ett iSCSI Direct-gränssnitt på mål Linux-servrar för det delade lagrings klustret.

3.  Konfigurera iSCSI-initieraren på de virtuella Linux-datorerna. Initieraren kommer åt det delade lagrings klustret genom att använda ett iSCSI-mål. Installations information finns i [så här konfigurerar du ett iSCSI-mål och en initierare i Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) i RootUsers-dokumentationen.

4.  Installera det delade lagrings skiktet för iSCSI-gränssnittet.

När skripten har skapat iSCSI-enheten är det sista steget att installera DB2 pureScale. Som en del av installationen av DB2-pureScale kompileras och installeras [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (tidigare kallat GPFS) och installeras i GlusterFS-klustret. Det här klustrade fil systemet gör det möjligt för DB2 pureScale att dela data mellan de virtuella datorerna som kör DB2 pureScale-motorn. Mer information finns i dokumentationen för [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) på IBM-webbplatsen.

## <a name="db2-purescale-response-file"></a>DB2 pureScale-svarsfil

GitHub-lagringsplatsen innehåller DB2server. rsp, en svars fil (. RSP) som gör att du kan generera ett automatiserat skript för installationen av DB2 pureScale. I följande tabell visas de DB2 pureScale-alternativ som svars filen använder för installations programmet. Du kan anpassa svars filen efter behov för din miljö.

> [!NOTE]
> En exempel svars fil, DB2server. rsp, ingår i [DB2onAzure](https://aka.ms/db2onazure) -lagringsplatsen på GitHub. Om du använder den här filen måste du redigera den innan den kan fungera i din miljö.

| Skärm namn               | Fält                                        | Värde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Välkommen                   |                                              | Ny installation                                                                                           |
| Välj en produkt          |                                              | DB2-version 11.1.3.3. Server versioner med DB2 pureScale                                              |
| Konfiguration             | Katalog                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Välj Installations typ                 | Vanligt                                                                                               |
|                           | Jag godkänner IBM-villkoren                     | Markerad                                                                                               |
| Instans ägare            | Befintlig användare för instans, användar namn        | DB2sdin1                                                                                              |
| Avgränsad användare               | Befintlig användare, användar namn                     | DB2sdfe1                                                                                              |
| Kluster fil system       | Enhets Sök väg för delad diskpartition            | /dev/dm-2                                                                                             |
|                           | Monterings punkt                                  | /DB2sd \_ 1804a                                                                                         |
|                           | Delad disk för data                         | /dev/dm-1                                                                                             |
|                           | Monterings punkt (data)                           | /DB2fs/datafs1                                                                                        |
|                           | Delad disk för logg                          | /dev/dm-0                                                                                             |
|                           | Monterings punkt (logg)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services tiebreaker. Enhets Sök väg | /dev/dm-3                                                                                             |
| Värd lista                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Prioritera primär CF                         | cf1                                                                                                   |
|                           | Prioriterad sekundär CF                       | cf2                                                                                                   |
| Svarsfil och sammanfattning | första alternativet                                 | Installera DB2 Server Edition med funktionen IBM DB2 pureScale och spara inställningarna i en svarsfil |
|                           | Svars filens namn                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Information om den här distributionen

- Värdena för/dev-dm0,/dev-DM1,/dev-dm2 och/dev-DM3 kan ändras efter en omstart på den virtuella datorn där installationen äger rum (D0 i det automatiserade skriptet). Om du vill hitta rätt värden kan du utfärda följande kommando innan du slutför svars filen på den server där installationen ska köras:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Installations skripten använder alias för iSCSI-diskarna så att de faktiska namnen kan hittas enkelt.

- När installations skriptet körs på D0 kan **/dev/DM- \*** -värdena vara olika på D1, cf0 och CF1. Skillnaden i värden påverkar inte installationen av DB2-pureScale.

## <a name="troubleshooting-and-known-issues"></a>Felsökning och kända problem

GitHub-lagrings platsen innehåller en kunskaps bas som författarna underhåller. Det visar potentiella problem som du kan ha och lösningar som du kan prova. Till exempel kan kända problem inträffa när:

-   Du försöker komma åt gatewayens IP-adress.

-   Du kompilerar allmän offentlig licens (GPL).

-   Säkerhets hand skakningen mellan värdarna Miss lyckas.

-   DB2-installationsprogrammet identifierar ett befintligt fil system.

-   Du installerar IBM Spectrum-skalning manuellt.

-   Du installerar DB2 pureScale när IBM Spectrum Scale redan har skapats.

-   Du tar bort DB2 pureScale och IBM Spectrum Scale.

Mer information om dessa och andra kända problem finns i kb.md-filen i [DB2onAzure](https://aka.ms/DB2onAzure) -lagrings platsen.

## <a name="next-steps"></a>Nästa steg

-   [Skapa nödvändiga användare för en funktion installation av DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – skapa instans kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Data lösning för DB2 pureScale-kluster](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Guide för att lyfta och flytta Azure Virtual Data Center](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
