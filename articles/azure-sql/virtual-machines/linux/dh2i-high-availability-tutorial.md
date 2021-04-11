---
title: Konfigurera Always on-tillgänglighetsgrupper med DH2i DxEnterprise som körs på Linux-baserade Azure-Virtual Machines
description: Använd DH2i DxEnterprise som kluster hanterare för att uppnå hög tillgänglighet med en tillgänglighets grupp på SQL Server på Linux Azure Virtual Machines
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449434"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Självstudie – konfigurera en tre nods Always on-tillgänglighetsgrupper med DH2i DxEnterprise som körs på Linux-baserade Azure-Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här självstudien beskrivs hur du konfigurerar SQL Server Always on tillgänglighets grupp med DH2i DxEnterprise som körs på Linux-baserade Azure-Virtual Machines (VM). 

Mer information om DxEnterprise finns i [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft stöder data flytt, tillgänglighets grupp och SQL Server-komponenter. Kontakta DH2i om du vill ha support som rör dokumentationen av DH2i DxEnterprise-kluster för kluster-och hantering av kvorum.
 

Den här självstudien består av följande steg:

> [!div class="checklist"]
> * Installera SQL Server på alla virtuella Azure-datorer (VM) som ska ingå i tillgänglighets gruppen.
> * Installera DxEnterprise på alla virtuella datorer och konfigurera DxEnterprise-klustret.
> * Skapa virtuella värdar för att tillhandahålla stöd för redundans och hög tillgänglighet, Lägg till en tillgänglighets grupp och en databas i tillgänglighets gruppen.
> * Skapa den interna Azure Load Balancer för tillgänglighets gruppens lyssnare (valfritt).
> * Utför en manuell eller automatisk redundansväxling.

I den här självstudien ska vi konfigurera ett DxEnterprise-kluster med hjälp av [DxAdmin-klientens användar gränssnitt](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Alternativt kan du också konfigurera klustret med hjälp av kommando rads gränssnittet [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) . I det här exemplet har vi använt fyra virtuella datorer. Tre av de virtuella datorerna kör Ubuntu 18,04 och ingår i det tre nod klustret. Den fjärde virtuella datorn kör Windows 10 med DxAdmin-verktyget för att hantera och konfigurera klustret.

## <a name="prerequisites"></a>Förutsättningar

- Skapa fyra virtuella datorer i Azure. Följ [snabb starten: skapa en virtuell Linux-dator i Azure Portal](../../../virtual-machines/linux/quick-create-portal.md) artikeln för att skapa Linux-baserade virtuella datorer. På samma sätt, för att skapa den Windows-baserade virtuella datorn, följer du [snabb start: skapa en virtuell Windows-dator i Azure Portal](../../../virtual-machines/windows/quick-create-portal.md) artikeln.
- Installera .NET 3,1 på alla Linux-baserade virtuella datorer som ska ingå i klustret. Följ anvisningarna som beskrivs [här](/dotnet/core/install/linux) baserat på det Linux-operativsystem som du väljer.
- En giltig DxEnterprise-licens med aktiverade tillgänglighets grupp hanterings funktioner krävs. Mer information finns i [DxEnterprise kostnads fri utvärderings version](https://dh2i.com/trial/) om hur du kan få en kostnads fri utvärderings version.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Installera SQL Server på alla virtuella Azure-datorer som ska ingå i tillgänglighets gruppen

I den här självstudien konfigurerar vi ett Linux-baserat kluster med tre noder som kör tillgänglighets gruppen. Följ dokumentationen för [SQL Server installation på Linux](/sql/linux/sql-server-linux-overview#install) baserat på valet av Linux-plattform. Vi rekommenderar också att du installerar [SQL Server verktyg](/sql/linux/sql-server-linux-setup-tools) för den här självstudien.
 
> [!NOTE]
> Se till att det Linux-OS du väljer är en gemensam distribution som stöds av både [DH2i DxEnterprise (se avsnittet minimala system krav)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) och [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> I det här exemplet använder vi Ubuntu 18,04, som stöds av både DH2i DxEnterprise och Microsoft SQL Server.

I den här självstudien kommer vi inte att installera SQL Server på den virtuella Windows-datorn eftersom den här noden inte kommer att ingå i klustret, och används endast för att hantera klustret med hjälp av DxAdmin.

När du har slutfört det här steget bör du ha SQL Server och [SQL Server verktyg](/sql/linux/sql-server-linux-setup-tools) (valfritt) installerat på alla tre Linux-baserade virtuella datorer som ska ingå i tillgänglighets gruppen.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Installera DxEnterprise på alla virtuella datorer och konfigurera klustret

I det här steget ska vi installera DH2i DxEnterprise för Linux på de tre virtuella Linux-datorerna. I följande tabell beskrivs den roll som varje server spelar i klustret:

| Antal virtuella datorer | DH2i DxEnterprise-roll | Roll för Microsoft SQL Server tillgänglighets grupps replik |
|--|--|--|
| 1 | Klusternod – Linux-baserad | Primär |
| 1 | Klusternod – Linux-baserad | Sekundär-synkron incheckning |
| 1 | Klusternod – Linux-baserad | Sekundär-synkron incheckning |
| 1 | DxAdmin-klient | NA |


Om du vill installera DxEnterprise på de tre Linux-baserade noderna följer du dokumentationen för DH2i-DxEnterprise baserat på det operativ system för Linux som du väljer. Installera DxEnterprise med någon av de metoder som anges nedan.

- **Ubuntu**
    - [Lagrings platsen-installation Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Tillägg Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace-avbildning Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Lagrings platsen-installation Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Tillägg Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Marketplace-avbildning Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Om du bara vill installera klient verktyget DxAdmin på den virtuella Windows-datorn följer du [DxAdmin-klientens gränssnitt Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Efter det här steget bör du ha DxEnterprise-klustret skapat på de virtuella Linux-datorerna och DxAdmin-klienten som är installerad på Windows-klientdatorn. 

> [!NOTE]
> Du kan också skapa ett kluster med tre noder där en av noden läggs till som *enbart konfigurations läge*, enligt beskrivningen [här](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) för att aktivera automatisk redundans. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Skapa virtuella värdar för att tillhandahålla stöd för redundans och hög tillgänglighet

I det här steget ska vi skapa en virtuell värd, tillgänglighets grupp och sedan lägga till en databas, allt med hjälp av DxAdmin-ANVÄNDARGRÄNSSNITTET.   

> [!NOTE]
> Under det här steget startas SQL Server instanser om så att de alltid aktive ras. 

Anslut till Windows-klientdatorn som kör DxAdmin för att ansluta till klustret som du skapade i steget ovan. Följ stegen som dokumenteras i [tillgänglighets grupper för MSSQL med DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) för att aktivera Always on och skapa den virtuella värden och tillgänglighets gruppen. 

> [!TIP]
> Innan du lägger till databaserna måste du se till att databasen skapas och säkerhets kopie ras på den primära instansen av SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Skapa den interna Azure Load Balancer för lyssnare (valfritt)

I det här valfria steget kan du skapa och konfigurera den Azure Load Balancer som innehåller IP-adresserna för tillgänglighets gruppens lyssnare. Mer information om Azure Load Balancer finns [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Om du vill konfigurera Azure Load Balancer och tillgänglighets gruppens lyssnare med hjälp av DxAdmin, följer du DxEnterprise [Azure Load Balancer Snabbstartsguide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/).

Efter det här steget bör du ha en lyssnare för tillgänglighets grupp skapad och mappad till den interna Azure Load Balancer.

## <a name="test-manual-or-automatic-failover"></a>Testa manuell eller automatisk redundans

För det automatiska redundansväxlingen kan du gå vidare och ta bort den primära repliken (Stäng av den virtuella datorn från Azure Portal). Detta kommer att replikera den primära nodens plötsliga otillgänglighet. Förväntat beteende är:
- Kluster hanteraren befordrar en av de sekundära replikerna i tillgänglighets gruppen till primär.
- Den misslyckade primära repliken ansluter automatiskt till klustret när den har säkerhetskopierats. Kluster hanteraren höjer den till en sekundär replik.

 
Du kan också utföra en manuell redundansväxling genom att följa anvisningarna nedan:

1. Ansluta till klustret via DxAdmin   
1. Expandera den virtuella värden för tillgänglighets gruppen
1. Högerklicka på målnoden/sekundär replik och välj **Starta värd för medlem** för att initiera redundansväxlingen 

Mer information om fler åtgärder inom DxEnterprise finns i [Administratörs guiden för DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) och [DxEnterprise DxCLI-guide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [tillgänglighets grupper i Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Snabb start: skapa en virtuell Linux-dator i Azure Portal](../../../virtual-machines/linux/quick-create-portal.md)
- [Snabbstart: Skapa en virtuell Windows-dator i Azure Portal](../../../virtual-machines/windows/quick-create-portal.md)
- [Plattformar som stöds för SQL Server 2019 i Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)