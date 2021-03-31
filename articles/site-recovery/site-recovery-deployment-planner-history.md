---
title: Distributionshanteraren för Azure Site Recovery versions historik
description: Kända olika versioner av Site Recovery distributions planerare och kända begränsningar tillsammans med deras versions datum.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133809"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Distributionshanteraren för Azure Site Recovery versions historik

Den här artikeln innehåller en historik över alla versioner av Distributionshanteraren för Azure Site Recovery tillsammans med korrigeringar, kända begränsningar för varje versions datum.

## <a name="version-252"></a>Version 2,52

**Utgivnings datum: 4 juni 2020**

**Fixe**

- Stöd har lagts till för vCenter 7,0
- Stöd har lagts till för följande operativ system:

    - SUSE Linux Enterprise 15 (med alla del versioner)
    - Red Hat Enterprise Linux 8 (med alla del versioner)


## <a name="version-251"></a>Version 2,51

**Utgivnings datum: 22 augusti 2019**

**Fixe**

- Fastställde kostnads rekommendations problemet med distributions hanteraren version 2,5

## <a name="version-25"></a>Version 2,5

**Utgivnings datum: 29 juli 2019**

**Fixe**

- För virtuella VMware-datorer och fysiska datorer uppdateras rekommendationen utifrån replikering till Managed Disks.
- Stöd har lagts till för Windows 10 (x64), Windows 8,1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 eller senare

## <a name="version-24"></a>Version 2,4

**Utgivnings datum: 17 april 2019**

**Fixe**

- Förbättrad kompatibilitet för operativ system, särskilt vid hantering av lokaliserings fel.
- Virtuella datorer har lagts till med upp till 20 Mbps data ändrings frekvens (omsättning) i kompatibilitetskontroll.
- Förbättrade felmeddelanden
- Stöd har lagts till för vCenter 6,7.
- Stöd har lagts till för Windows Server 2019 och Red Hat Enterprise Linux (RHEL)-arbets Station.



## <a name="version-23"></a>Version 2,3

**Utgivnings datum: 3 december 2018**

**Fixe**

- Ett problem har åtgärd ATS som gjorde att distributions hanteraren inte kunde skapa en rapport med den angivna mål platsen och prenumerationen.

## <a name="version-22"></a>Version 2,2 

**Utgivnings datum: 25 april 2018**

**Fixe**

- GetVMList-åtgärder:
  - Ett problem har åtgärd ATS som orsakade GetVMList av fel om den angivna mappen inte finns. Nu skapas standard katalogen, eller så skapas den katalog som anges i parametern utfil.
  - Ytterligare utförliga felorsaks orsaker har lagts till för GetVMList.
- Har lagt till information om VM-typ som en kolumn i bladet kompatibla virtuella datorer i distributions planerings rapporten.
- Katastrof återställning för Hyper-V till Azure:
  - Undantagna virtuella datorer med delade virtuella hård diskar och genom strömning av diskar från profilering. Startprofiling-åtgärden visar listan över undantagna virtuella datorer i-konsolen.
  - Virtuella datorer med fler än 64 diskar har lagts till i listan över inkompatibla virtuella datorer.
  - Uppdaterad komprimerings faktorn för den inledande replikeringen (IR) och delta replikeringen (DR).
  - Begränsat stöd för SMB-lagring har lagts till.

## <a name="version-21"></a>Version 2,1

**Utgivnings datum: 3 januari 2018**

**Fixe**

- Excel-rapporten har uppdaterats.
- Åtgärdade buggar i GetThroughput-åtgärden.
- Alternativet har lagts till för att begränsa antalet virtuella datorer till profil eller generera rapporten. Standard gränsen är 1 000 virtuella datorer.
- Katastrof återställning för VMware till Azure:
  - Ett problem med Windows Server 2016 VM har åtgärd ATS i den inkompatibla tabellen. 
  - Uppdaterade kompatibilitetsproblem för Extensible Firmware Interface (EFI) virtuella Windows-datorer.
- Uppdaterat VMware till Azure och Hyper-V till Azure, gränsen för VM-dataomsättning per virtuell dator. 
- Förbättrad tillförlitlighet för fil parsning i VM-listor.

## <a name="version-201"></a>Version 2.0.1

**Utgivnings datum: 7 december 2017**

**Fixe**

- Rekommendationen har lagts till för att optimera nätverks bandbredden.

## <a name="version-20"></a>Version 2,0

**Utgivnings datum: 28 november 2017**

**Fixe**

- Stöd har lagts till för haveri beredskap för Hyper-V till Azure.
- Kostnads kalkylator har lagts till.
- OS-versionen har lagts till för haveri beredskap för VMware till Azure för att avgöra om den virtuella datorn är kompatibel eller inkompatibel med skyddet. Verktyget använder operativ systemets versions sträng som returneras av vCenter-servern för den virtuella datorn. Det är den gäst operativ system version som användaren har valt när den virtuella datorn skapades i VMware.

**Kända begränsningar:**

- För haveri beredskap för Hyper-V till Azure kan den virtuella datorn ha ett namn som innehåller de tecken som: `,` , `"` ,, och som `[` `]` ``` ` ``` inte stöds. Om profilerat Miss lyckas rapporterar rapportgenerering eller kommer att ha ett felaktigt resultat.
- För haveri beredskap för VMware till Azure stöds inte VM med namn som innehåller kommatecken. Om profilerade, rapportgenerering, Miss lyckas eller kommer att ha ett felaktigt resultat.

## <a name="version-131"></a>Version 1.3.1

**Utgivnings datum: 19 juli 2017** 

**Fixe**

- Stöd har lagts till för stora diskar (> 1 TB) i rapportgenerering. Nu kan du använda Deployment Planner för att planera replikering för virtuella datorer som har disk storlekar som är större än 1 TB (upp till 4095 GB).
Läs mer om [stöd för stora diskar i Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Version 1,3

**Utgivnings datum: 9 maj 2017**

**Fixe**

- Stöd har lagts till för hanterad disk i rapportgenerering. Antalet virtuella datorer som kan placeras i ett enda lagrings konto beräknas baserat på om den hanterade disken är vald för redundans/redundanstest.

## <a name="version-12"></a>Version 1,2

**Utgivnings datum: 7 april 2017**

**Fixe**

- Kontroll av start typ (BIOS eller EFI) har lagts till för varje virtuell dator för att avgöra om den virtuella datorn är kompatibel eller inkompatibel för skyddet.
- Information om OS-typ har lagts till för varje virtuell dator i de kompatibla virtuella datorerna och de virtuella datorerna i inkompatibla
- Stöd har lagts till för GetThroughput-åtgärden för amerikanska myndigheter och Kina Microsoft Azure regioner.
- Lade till några fler nödvändiga kontroller för vCenter- och ESXi-Server.
- Ett problem med fel rapporter som genererades när nationella inställningar är inställt på icke-engelska har åtgärd ATS.

## <a name="version-11"></a>Version 1,1

**Utgivnings datum: 9 mars 2017**

**Fixe**

- Ett problem har åtgärd ATS som förhindrade profilering av virtuella datorer när det finns två eller flera virtuella datorer med samma namn eller IP-adress över olika vCenter ESXi-värdar.
- Ett problem som gjorde att kopiering och sökning har inaktiverats för de kompatibla virtuella datorerna och de kompatibla virtuella datorerna har åtgärd ATS.

## <a name="version-10"></a>Version 1,0

**Utgivnings datum: 23 februari 2017**

**Kända begränsningar:**

- Stöder endast för katastrof återställnings scenarier med VMware till Azure. För katastrof återställnings scenarier för Hyper-V till Azure använder du [verktyget för Hyper-v-kapacitets planering](./hyper-v-deployment-planner-overview.md).
- Har inte stöd för GetThroughput-åtgärden för amerikanska myndigheter och Kina Microsoft Azure regioner.
- Verktyget Cann't virtuella datorer om vCenter-servern har två eller flera virtuella datorer med samma namn eller IP-adress över olika ESXi-värdar.
I den här versionen hoppar verktyget över profilering för dubbletter av namn på virtuella datorer eller IP-adresser i VMListFile. Lösningen är att profilera de virtuella datorerna med hjälp av en ESXi-värd i stället för vCenter-servern. Se till att köra en instans för varje ESXi-värd.
