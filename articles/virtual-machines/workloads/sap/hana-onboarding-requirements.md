---
title: Onboarding-krav för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Onboarding-krav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675614"
---
# <a name="onboarding-requirements"></a>Registreringskrav

I den här listan samlas kraven för att köra SAP HANA på Azure (större instanser).

**Microsoft Azure**

- En Azure-prenumeration som kan länkas till SAP HANA på Azure (stora instanser).
- Microsoft Premier support-avtal. För detaljerad information om att köra SAP i Azure, se [SAP support Note #2015553 – SAP på Microsoft Azure: stöd för krav](https://launchpad.support.sap.com/#/notes/2015553). Om du använder processorer med stora instanser med 384 och fler processorer måste du också utöka Premier Support-kontraktet för att inkludera Azure Rapid Response.
- Medvetenhet om de stora instanser av HANA-instanser du behöver när du har gjort en storleks övning med SAP.

**Nätverksanslutningar**

- ExpressRoute mellan lokala och Azure: om du vill ansluta ditt lokala data Center till Azure måste du beställa minst en 1 Gbit/s-anslutning från Internet leverantören. Anslutning mellan HANA stora instans enheter och Azure använder även ExpressRoute-teknik. Den här ExpressRoute-anslutningen mellan de HANA-stora instans enheterna och Azure ingår i priset för de många HANA-instanser, inklusive alla inkommande och utgående kostnader för den här ExpressRoute-kretsen. Därför kommer du som kund inte att upptäcka ytterligare kostnader utöver din ExpressRoute-länk mellan lokala platser och Azure.

**Operativsystem**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

   > [!NOTE] 
   > Operativ systemet som levereras av Microsoft är inte registrerat med SUSE. Den är inte ansluten till en instans av prenumerations hanterings verktyget.

- Prenumerations hanterings verktyget SUSE Linux distribueras i Azure på en virtuell dator. Det här verktyget ger möjlighet för SAP HANA på Azure (stora instanser) att registreras och uppdateras av SUSE. (Det finns ingen Internet åtkomst i data centret HANA stor instans.) 
- Licenser för Red Hat Enterprise Linux 6,7 eller 7. x för SAP HANA.

   > [!NOTE]
   > Operativ systemet som levereras av Microsoft är inte registrerat med Red Hat. Den är inte ansluten till en Red Hat Subscription Manager-instans.

- Red Hat-prenumerations hanteraren distribuerad i Azure på en virtuell dator. Med Red Hat-prenumerations hanteraren får du SAP HANA på Azure (stora instanser) som ska registreras och uppdateras med Red Hat. (Det finns ingen direkt Internet åtkomst inifrån klienten som distribuerats på den stora Azure-instansnamnet.)
- SAP kräver också att du har ett support avtal med din Linux-Provider. Detta krav tas inte bort i lösningen av HANA stor instans eller det faktum att du kör Linux i Azure. Till skillnad från vissa av Linux Azure Gallery-avbildningarna ingår *inte* service avgiften i lösnings erbjudandet för Hana stor instans. Det är ditt ansvar att uppfylla kraven i SAP angående support avtal med Linux-distributören. 
   - För SUSE Linux letar du upp kraven för support avtal i [SAP Note #1984787-SUSE Linux Enterprise Server 12: installations information](https://launchpad.support.sap.com/#/notes/1984787) och [SAP-anteckning #1056161-SUSE prioritets stöd för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - För Red Hat Linux måste du ha rätt prenumerations nivåer som omfattar support-och tjänst uppdateringar till operativ systemen för HANA stor instans. Red Hat rekommenderar Red Hat Enterprise Linux prenumeration för SAP-lösning. Se https://access.redhat.com/solutions/3082481 . 

En support mat ris för olika SAP HANA versioner med olika Linux-versioner finns i [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Om du vill ha en HLI för operativ systemet och den inbyggda program varan/driv rutins versionerna, se [operativ Systems uppgradering för HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> För typ II-enheter stöds endast SLES 12 SP2 OS-versionen just nu. 


**Databas**

- Licenser och program installations komponenter för SAP HANA (plattform eller Enterprise Edition).

**Program**

- Licenser och program installations komponenter för alla SAP-program som ansluter till SAP HANA och relaterade SAP-support avtal.
- Licenser och program installations komponenter för alla icke-SAP-program som används med SAP HANA på Azure-miljöer (stora instanser) och relaterade support avtal.

**Kompetens**

- Upplev med och kunskap om Azure-IaaS och dess komponenter.
- Upplev med och kunskap om hur du distribuerar en SAP-arbetsbelastning i Azure.
- SAP HANA installation certifierat personligt.
- SAP arkitekt-kunskaper för att designa hög tillgänglighet och haveri beredskap kring SAP HANA.

**SAP**

- Förväntat är att du är SAP-kund och har ett support avtal med SAP.
- Särskilt för implementeringar av Type II-klassen i HANA stor instans-SKU: er, se med SAP på versioner av SAP HANA och de konfigurationer som finns på storskalig maskin vara med stor storlek.

**Nästa steg**
- Referera [SAP HANA (stora instanser) arkitektur i Azure](hana-architecture.md)