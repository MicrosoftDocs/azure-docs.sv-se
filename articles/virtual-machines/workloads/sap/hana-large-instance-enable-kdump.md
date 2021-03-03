---
title: Skript för att aktivera kdump i SAP HANA (stora instanser) | Microsoft Docs
description: Skript för att aktivera kdump i SAP HANA (stora instanser) HLI typ I, HLI typ II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a9b3d534e74ccd44dfaa35ec5bf3b65f50641c7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666657"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump för SAP HANA på stora Azure-instanser (HLI)

Att konfigurera och aktivera kdump är ett steg som behövs för att felsöka system krascher som inte har en tydlig orsak.
Det finns tillfällen när ett system slutar att krascha som inte kan förklaras av ett problem med maskin vara eller infrastruktur.
I dessa fall kan det vara problem med operativ systemet eller programmet och kdump gör att SUSE kan avgöra varför ett system har kraschat.

## <a name="enable-kdump-service"></a>Aktivera kdump-tjänsten

Det här dokumentet beskriver information om hur du aktiverar kdump-tjänsten på den stora Azure HANA-instansen (**typ I och typ II**)

## <a name="supported-skus"></a>SKU: er som stöds

|  Hana-stor instans typ   |  OS-leverantör   |  OS-paketets version   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Förutsättningar

- Kdump-tjänsten använder `/var/crash` katalogen för att skriva dum par, se till att partitionen motsvarar den här katalogen har tillräckligt med utrymme för att ta emot dumpar.

## <a name="setup-details"></a>Installations information

- Skript för att aktivera kdump hittar du [här](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> Det här skriptet görs baserat på vår labb konfiguration och kunden förväntas kontakta system leverantören för att få ytterligare justeringar.
> Separat LUN kommer att tillhandahållas för de nya och befintliga servrarna för att spara dumpningarna och skriptet tar hand om att konfigurera fil systemet från LUN.
> Microsoft ansvarar inte för att analysera dumpningen. Kunden måste öppna en biljett med OS-leverantören för att få den analyserad.

- Kör det här skriptet på HANA-stor instans med kommandot nedan

    > [!NOTE]
    > sudo privilegium som krävs för att köra det här kommandot.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Om kommandot utdata kdump har Aktiver ATS, måste du starta om systemet för att ändringarna ska kunna verkställas.

- Om kommandots utdata inte kunde utföra en viss åtgärd, avslutar!!!!, kdump-tjänsten inte är aktive rad. Se avsnittet [support problem](#support-issue).

## <a name="test-kdump"></a>Testa kdump

> [!NOTE]
>  Under åtgärden utlöses en kernel-krasch och en omstart av systemet.

- Utlösa en kernel-krasch

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- När systemet har startats om kontrollerar du `/var/crash` katalogen för kernel krasch-loggar.

- Om `/var/crash` katalogen innehåller med aktuellt datum har kdump Aktiver ATS.

## <a name="support-issue"></a>Support ärende

Om skriptet Miss lyckas med ett fel eller om kdump inte är aktiverat, kan du generera en tjänstbegäran med Microsoft Support-teamet med följande information.

* Prenumerations-ID för HLI

* Servernamn

* OS-leverantör

* OS-version

* Kernelversion

## <a name="related-documents"></a>Relaterade dokument
- Mer information om [hur du konfigurerar kdump](https://www.suse.com/support/kb/doc/?id=3374462)
