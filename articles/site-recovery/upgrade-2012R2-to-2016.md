---
title: Uppgradera Windows Server/System Center VMM 2012 R2 till Windows Server 2016 – Azure Site Recovery
description: Lär dig hur du uppgraderar Windows Server 2012 R2-värdar & SCVMM 2012 R2 som är konfigurerade med Azure Site Recovery, till Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b3df487d690befadd249142c449163c2393f6df6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640329"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Uppgradera Windows Server Server/System Center 2012 R2 VMM till Windows Server/VMM 2016 

Den här artikeln visar hur du uppgraderar Windows Server 2012 R2-värdar & SCVMM 2012 R2 som är konfigurerade med Azure Site Recovery, till Windows Server 2016 & SCVMM 2016

Site Recovery bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR). Tjänsten säkerställer att dina VM-arbetsbelastningar är tillgängliga när förväntade och oväntade avbrott inträffar.

> [!IMPORTANT]
> När du uppgraderar Windows Server 2012 R2-värdar som redan har kon figurer ATS för replikering med Azure Site Recovery, måste du följa stegen som beskrivs i det här dokumentet. Eventuella alternativa sökvägar som valts för uppgradering kan resultera i tillstånd som inte stöds och kan leda till en paus i replikeringen eller möjlighet att utföra redundans.


I den här artikeln får du lära dig hur du uppgraderar följande konfigurationer i din miljö:

> [!div class="checklist"]
> * **Windows Server 2012 R2-värdar som inte hanteras av SCVMM** 
> * **Windows Server 2012 R2-värdar som hanteras av en fristående SCVMM 2012 R2-server** 
> * **Windows Server 2012 R2-värdar som hanteras av SCVMM 2012 R2-server med hög tillgänglighet**


## <a name="prerequisites--factors-to-consider"></a>Förutsättningar & faktorer att överväga

Observera följande innan du uppgraderar:-

- Om du har Windows Server 2012 R2-värdar som inte hanteras av SCVMM, och det är en fristående miljö, kommer det att finnas en paus i replikeringen om du försöker utföra uppgraderingen.
- Om du har valt "*Spara inte mina nycklar i Active Directory under hantering av distribuerad nyckel*" när SCVMM 2012 R2 installerades på den första platsen, slutförs inte uppgraderingen.

- Om du använder System Center 2012 R2 VMM 

    - Kontrol lera databas informationen på VMM: inställningar för **VMM-konsolen**  ->    ->  **allmän**  ->  **databas anslutning**
    - Kontrol lera vilka tjänst konton som används för tjänsten System Center Virtual Machine Manager agent
    - Kontrol lera att du har en säkerhets kopia av VMM-databasen.
    - Anteckna databas namnet för de SCVMM-servrar som berörs. Detta kan göras genom att gå till **VMM-konsolens**  ->  **Inställningar**  ->  **allmän**  ->  **databas anslutning**
    - Anteckna VMM-ID för både 2012R2-primära och återställnings VMM-servrar. Du hittar VMM-ID från registret "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Se till att den nya SCVMMs som du lägger till i klustret har samma namn som tidigare. 

- Om du replikerar mellan två av dina platser som hanteras av SCVMMs på båda sidorna, måste du först uppgradera återställnings sidan innan du uppgraderar den primära sidan.
  > [!WARNING]
  > När du uppgraderar SCVMM 2012 R2, under hantering av distribuerad nyckel, väljer du att **lagra krypterings nycklar i Active Directory**. Välj inställningar för tjänst konto och hantering av distribuerad nyckel noggrant. Beroende på ditt val kanske krypterade data som lösen ord i mallar inte är tillgängliga efter uppgraderingen och kan påverka replikeringen med Azure Site Recovery

> [!IMPORTANT]
> Se den detaljerade SCVMM-dokumentationen för [krav](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-värdar som inte hanteras av SCVMM 
Listan över steg som beskrivs nedan gäller användar konfigurationen från [Hyper-V-värdar till Azure](./hyper-v-azure-architecture.md) som körs genom att följa den här [självstudien](./hyper-v-prepare-on-premises-tutorial.md)

> [!WARNING]
> Som anges i kraven gäller de här stegen bara för ett klustrat miljö scenario och inte i en fristående konfiguration av Hyper-V-värdar.

1. Följ stegen för att utföra uppgraderingen av det [rullande klustret.](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) för att köra uppgraderingen av rullande kluster.
2. Med varje ny Windows Server 2016-värd som introduceras i klustret tar du bort referensen till en Windows Server 2012 R2-värd från Azure Site Recovery genom att följa anvisningarna [här]. Detta bör vara den värd som du har valt att tömma & avlägsna från klustret.
3. När kommandot *Update-VMVersion* har körts för alla virtuella datorer har uppgraderingen slutförts. 
4. Använd de steg som beskrivs [här](./hyper-v-azure-tutorial.md#set-up-the-source-environment) för att registrera den nya Windows Server 2016-värden för Azure Site Recovery. Observera att Hyper-V-platsen redan är aktiv och att du bara behöver registrera den nya värden i klustret. 
5. Gå till Azure Portal och kontrol lera den replikerade hälso statusen i Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar som hanteras av fristående SCVMM 2012 R2-server
Innan du uppgraderar dina Windows Server-2012 R2-värdar måste du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följ stegen nedan:-

**Uppgradera fristående SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att gå till kontroll panelen – > program – > program och funktioner – >Microsoft Azure Site Recovery och klicka på Avinstallera
2. [Behåll SCVMM-databasen och uppgradera operativ systemet](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#back-up-and-upgrade-the-operating-system)
3. I **Lägg till ta bort program** väljer du **VMM**-  >  **avinstallation**. b. Välj **ta bort funktioner** och välj sedan V **mm-hanterings Server och VMM-konsol**. c. I **databas alternativ** väljer du **Kvarhåll databas**. d. Granska sammanfattningen och klicka på **Avinstallera**.

4. [Installera VMM 2016](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#install-vmm-2016)
5. Starta SCVMM och kontrol lera status för varje värd under fliken **infrastruktur** resurs. Klicka på **Uppdatera** för att få den senaste statusen. Du bör se status som "behöver åtgärdas". 
17.    Installera den senaste [Microsoft Azure Site Recovery-providern](https://aka.ms/downloaddra) på SCVMM.
16.    Installera den senaste [mars-agenten (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att se till att SCVMM kan skicka frågor till värdarna.

**Uppgradera Windows Server 2012 R2-värdar till Windows Server 2016**

1. Följ stegen som beskrivs [här](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) om du vill köra uppgraderingen av rullande kluster. 
2. När du har lagt till den nya värden i klustret uppdaterar du värden från SCVMM-konsolen för att installera VMM-agenten på den här uppdaterade värden.
3. Kör *Update-VMVersion* för att uppdatera VM-versionerna av de virtuella datorerna. 
4. Gå till Azure Portal och kontrol lera de virtuella datorernas replikerade hälso status i Recovery Services valvet. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uppgradera Windows Server 2012 R2-värdar hanteras med hög tillgängliga SCVMM 2012 R2-server
Innan du uppgraderar dina Windows Server-2012 R2-värdar måste du uppgradera SCVMM 2012 R2 till SCVMM 2016. Följande uppgraderings lägen stöds vid uppgradering av SCVMM 2012 R2-servrar som kon figurer ATS med Azure Site Recovery blandat läge utan ytterligare VMM-servrar & blandat läge med ytterligare VMM-servrar.

**Uppgradera SCVMM 2012 R2 till SCVMM 2016**

1.  Avinstallera ASR-providern genom att gå till kontroll panelen – > program – > program och funktioner – >Microsoft Azure Site Recovery och klicka på Avinstallera
2. Följ de steg som beskrivs [här](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016&preserve-view=true#upgrade-a-standalone-vmm-server) baserat på det uppgraderings läge du vill köra.
3. Starta SCVMM-konsolen och kontrol lera status för varje värd under fliken **infrastruktur** resurs. Klicka på **Uppdatera** för att få den senaste statusen. Du bör se status som "behöver åtgärdas".
4. Installera den senaste [Microsoft Azure Site Recovery-providern](https://aka.ms/downloaddra) på SCVMM.
5. Uppdatera den senaste [mars-agenten (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) på varje värd i klustret. Uppdatera för att se till att SC VMM kan skicka frågor till värdarna.


**Uppgradera Windows Server 2012 R2-värdar till Windows Server 2016**

1. Följ stegen som beskrivs [här](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) om du vill köra uppgraderingen av rullande kluster.
2. När du har lagt till den nya värden i klustret uppdaterar du värden från SCVMM-konsolen för att installera VMM-agenten på den här uppdaterade värden.
3. Kör *Update-VMVersion* för att uppdatera VM-versionerna av de virtuella datorerna. 
4. Gå till Azure Portal och kontrol lera de virtuella datorernas replikerade hälso status i Recovery Services valvet. 

## <a name="next-steps"></a>Nästa steg
När uppgraderingen av värdarna har utförts kan du utföra en [redundanstest](tutorial-dr-drill-azure.md) för att testa hälso tillståndet för din replikering och haveri beredskap.
