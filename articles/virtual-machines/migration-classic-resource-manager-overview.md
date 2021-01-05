---
title: Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager
description: Gå igenom plattforms oberoende migrering av resurser från klassisk till Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 137670715af8b90d8a867459fa50249cd9be8e70
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897124"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager-in-linux"></a>Plattforms stöd för migrering av IaaS-resurser från klassisk till Azure Resource Manager i Linux

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](classic-vm-deprecation.md#how-does-this-affect-me).



Den här artikeln innehåller en översikt över Migreringsverktyg för plattform som stöds, hur du migrerar resurser från Azure Service Manager (ASM) aka Classic till Resource Manager (ARM) distributions modeller och information om hur du ansluter resurser från de två distributions modeller som finns i din prenumeration med hjälp av virtuella nätverks plats-till-plats-gatewayer. Du kan läsa mer om [Azure Resource Manager funktioner och förmåner](../azure-resource-manager/management/overview.md). 

ASM stöder två olika beräknings produkter, Azure Virtual Machines (klassisk) aka IaaS VM & [Azure Cloud Services (klassisk)](https://docs.microsoft.com/azure/cloud-services/) aka PaaS-VM: ar eller Web/Worker-roller. Det här dokumentet pratar bara om migrering av Azure Virtual Machines (klassisk).

## <a name="goal-for-migration"></a>Mål för migrering
Med Resource Manager kan du distribuera komplexa program via mallar, konfigurera virtuella datorer med hjälp av VM-tillägg och införliva åtkomst hantering och taggning. Azure Resource Manager innehåller skalbar, parallell distribution för virtuella datorer i tillgänglighets uppsättningar. Den nya distributions modellen innehåller också livs cykel hantering av beräkning, nätverk och lagring oberoende av varandra. Slutligen är det en fokus på att aktivera säkerhet som standard med tvång av virtuella datorer i ett virtuellt nätverk.

Nästan alla funktioner från den klassiska distributions modellen stöds för beräkning, nätverk och lagring under Azure Resource Manager. Om du vill dra nytta av de nya funktionerna i Azure Resource Manager kan du migrera befintliga distributioner från den klassiska distributions modellen.

## <a name="supported-resources--configurations-for-migration"></a>Resurser som stöds & konfigurationer för migrering

### <a name="supported-resources-for-migration"></a>Resurser som stöds för migrering
* Virtual Machines
* Tillgänglighetsuppsättningar
* Lagringskonton
* Virtuella nätverk
* VPN-gatewayer
* [Express Route-gatewayer](https://docs.microsoft.com/azure/expressroute/expressroute-howto-move-arm) _(endast i samma prenumeration som Virtual Network)_
* Nätverkssäkerhetsgrupper
* Routningstabeller
* Reserverade ip-adresser

## <a name="supported-configurations-for-migration"></a>Konfigurationer som stöds för migrering
Dessa klassiska IaaS-resurser stöds under migreringen

| Tjänst | Konfiguration |
| --- | --- |
| Azure AD Domain Services | [Virtuella nätverk som innehåller Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Omfång som stöds för migrering
Det finns fyra olika sätt att slutföra migreringen av beräknings-, nätverks-och lagrings resurser:

* [Migrering av virtuella datorer (inte i ett virtuellt nätverk)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrering av virtuella datorer (i ett virtuellt nätverk)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrering av lagrings konton](#migration-of-storage-accounts)
* [Migrering av ej anslutna resurser](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrering av virtuella datorer (inte i ett virtuellt nätverk)
I distributions modellen för Resource Manager tillämpas säkerheten på dina program som standard. Alla virtuella datorer måste finnas i ett virtuellt nätverk i Resource Manager-modellen. Azure-plattformen startar om ( `Stop` , `Deallocate` och `Start` ) de virtuella datorerna som en del av migreringen. Du har två alternativ för de virtuella nätverk som Virtual Machines ska migreras till:

* Du kan begära plattformen för att skapa ett nytt virtuellt nätverk och migrera den virtuella datorn till det nya virtuella nätverket.
* Du kan migrera den virtuella datorn till ett befintligt virtuellt nätverk i Resource Manager.

> [!NOTE]
> I det här migrerings omfånget är både hanterings Plans åtgärderna och data Plans åtgärderna inte tillåtna under en tids period under migreringen.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrering av virtuella datorer (i ett virtuellt nätverk)
För de flesta konfigurationer av virtuella datorer migreras bara metadata mellan de klassiska distributions modellerna för och Resource Manager. De underliggande virtuella datorerna körs på samma maskin vara, i samma nätverk och med samma lagrings utrymme. Hanterings Plans åtgärder kan inte tillåtas under en viss tids period under migreringen. Men data planet fortsätter att fungera. Det vill säga att dina program som körs på de virtuella datorerna (klassisk) inte tar avbrott under migreringen.

Följande konfigurationer stöds inte för närvarande. Om support läggs till i framtiden kan vissa virtuella datorer i den här konfigurationen ådra sig avbrott (gå igenom stoppa, frigör och starta om VM-åtgärder).

* Du har fler än en tillgänglighets uppsättning i en enda moln tjänst.
* Du har en eller flera tillgänglighets uppsättningar och virtuella datorer som inte finns i en tillgänglighets uppsättning i en enda moln tjänst.

> [!NOTE]
> I det här migrerings omfånget kanske inte hanterings planet tillåts under en viss tids period under migreringen. För vissa konfigurationer enligt beskrivningen ovan inträffar data planet avbrotts tid.
>

### <a name="migration-of-storage-accounts"></a>Migrering av lagrings konton
För att möjliggöra sömlös migrering kan du distribuera virtuella Resource Manager-datorer i ett klassiskt lagrings konto. Med den här funktionen kan beräknings-och nätverks resurser och bör migreras oberoende av lagrings konton. När du har migrerat över din Virtual Machines och Virtual Network måste du migrera över dina lagrings konton för att slutföra migreringsprocessen.

Om ditt lagrings konto inte har några associerade diskar eller Virtual Machines data och bara innehåller blobbar, filer, tabeller och köer, kan migreringen till Azure Resource Manager göras som en fristående migrering utan beroenden.

> [!NOTE]
> Distributions modellen för Resource Manager har inte begreppet klassiska avbildningar och diskar. När lagrings kontot migreras visas inte klassiska avbildningar och diskar i Resource Manager-stacken men de virtuella hård diskarna finns kvar i lagrings kontot.

Följande skärm bilder visar hur du uppgraderar ett klassiskt lagrings konto till ett Azure Resource Manager lagrings konto med hjälp av Azure Portal:
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. I avsnittet **Inställningar** klickar du på **migrera till arm**.
4. Klicka på **validera** för att fastställa hur migreringen är genomförbar.
5. Om verifieringen lyckas klickar du på **Förbered** för att skapa ett migrerat lagrings konto.
6. Skriv **Ja** för att bekräfta migreringen och klicka på **genomför** för att slutföra migreringen.

    ![Verifiera lagrings konto](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Förbered lagrings konto](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Slutför migrering av lagrings konto](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migrering av ej anslutna resurser
Lagrings konton utan tillhör ande diskar eller Virtual Machines data kan migreras separat.

Nätverks säkerhets grupper, väg tabeller & reserverade IP-adresser som inte är kopplade till någon Virtual Machines och virtuella nätverk kan också migreras separat.

<br>

## <a name="unsupported-features-and-configurations"></a>Funktioner och konfigurationer som inte stöds
Vissa funktioner och konfigurationer stöds inte för närvarande. i följande avsnitt beskrivs våra rekommendationer runt dem.

### <a name="unsupported-features"></a>Funktioner som inte stöds
Följande funktioner stöds inte för närvarande. Du kan också ta bort de här inställningarna, migrera de virtuella datorerna och sedan återaktivera inställningarna i distributions modellen för Resource Manager.

| Resursprovider | Funktion | Rekommendation |
| --- | --- | --- |
| Compute | Avassocierade virtuella dator diskar. | VHD-blobbar bakom diskarna migreras när lagrings kontot migreras |
| Compute | Avbildningar av virtuella datorer. | VHD-blobbar bakom diskarna migreras när lagrings kontot migreras |
| Nätverk | Slutpunktens åtkomstkontrollistor. | Ta bort ACL för slut punkter och försök att migrera igen. |
| Nätverk | Application Gateway | Ta bort Application Gateway innan du påbörjar migreringen och återskapa sedan Application Gateway när migreringen är klar. |
| Nätverk | Virtuella nätverk som använder VNet-peering. | Migrera Virtual Network till Resource Manager och sedan peer. Läs mer om [VNet-peering](../virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Konfigurationer som inte stöds
Följande konfigurationer stöds inte för närvarande.

| Tjänst | Konfiguration | Rekommendation |
| --- | --- | --- |
| Resource Manager |Role-Based Access Control (RBAC) för klassiska resurser |Eftersom resursens URI ändras efter migreringen, rekommenderar vi att du planerar de RBAC-principinställningar som måste inträffa efter migreringen. |
| Compute |Flera undernät som är kopplade till en virtuell dator |Uppdatera under näts konfigurationen så att den bara refererar till ett undernät. Detta kan kräva att du tar bort ett sekundärt nätverkskort (som refererar till ett annat undernät) från den virtuella datorn och ansluter det igen efter att migreringen har slutförts. |
| Compute |Virtuella datorer som tillhör ett virtuellt nätverk, men som inte har något explicit tilldelat undernät |Du kan också ta bort den virtuella datorn. |
| Compute |Virtuella datorer som har aviseringar, principer för autoskalning |Migreringen går igenom och de här inställningarna tas bort. Vi rekommenderar starkt att du utvärderar din miljö innan du migrerar. Alternativt kan du konfigurera om aviserings inställningarna när migreringen är klar. |
| Compute |VIRTUELLA XML-tillägg (BGInfo 1. *, Visual Studio-felsökning, webb distribution och fjärrfelsökning) |Detta stöds inte. Vi rekommenderar att du tar bort dessa tillägg från den virtuella datorn för att fortsätta migreringen, eller så kommer de att tas bort automatiskt under migreringsprocessen. |
| Compute |Startdiagnostik med Premium Storage |Inaktivera funktionen för startdiagnostik för de virtuella datorerna innan du fortsätter med migreringen. Du kan återaktivera startdiagnostik i Resource Manager-stacken när migreringen är klar. Dessutom bör blobbar som används för skärm bilds-och serie loggar tas bort så att du inte längre debiteras för dessa blobbar. |
| Compute | Moln tjänster som innehåller webb-/arbets roller | Detta stöds inte för närvarande. |
| Compute | Moln tjänster som innehåller mer än en tillgänglighets uppsättning eller flera tillgänglighets uppsättningar. |Detta stöds inte för närvarande. Flytta Virtual Machines till samma tillgänglighets uppsättning innan du migrerar. |
| Compute | Virtuell dator med Azure Security Center-tillägget | Azure Security Center installerar automatiskt tillägg på Virtual Machines för att övervaka deras säkerhet och utlösa aviseringar. Dessa tillägg installeras vanligt vis automatiskt om Azure Security Centers principen är aktive rad för prenumerationen. Om du vill migrera Virtual Machines inaktiverar du Security Center-principen för prenumerationen, som tar bort Security Center övervaknings tillägget från Virtual Machines. |
| Compute | Virtuell dator med säkerhets kopiering eller Snapshot-tillägg | Dessa tillägg installeras på en virtuell dator som har kon figurer ATS med tjänsten Azure Backup. Även om migreringen av de här virtuella datorerna inte stöds, följer du anvisningarna [här](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) för att behålla säkerhets kopior som togs innan migreringen.  |
| Compute | Virtuell dator med Azure Site Recovery-tillägget | Dessa tillägg installeras på en virtuell dator som har kon figurer ATS med tjänsten Azure Site Recovery. Även om migreringen av lagrings utrymme som används med Site Recovery fungerar, påverkas den aktuella replikeringen. Du måste inaktivera och aktivera VM-replikering efter lagringsmigrering. |
| Nätverk |Virtuella nätverk som innehåller virtuella datorer och webb-/arbets roller |Detta stöds inte för närvarande. Flytta webb-/arbets rollerna till sina egna Virtual Network innan du migrerar. När den klassiska Virtual Network har migrerats kan den migrerade Azure Resource Manager Virtual Network vara peer-kopplad med den klassiska Virtual Network för att uppnå liknande konfiguration som tidigare.|
| Nätverk | Klassiska ExpressRoute-kretsar |Detta stöds inte för närvarande. Dessa kretsar måste migreras till Azure Resource Manager innan du påbörjar migreringen av IaaS. Läs mer i [Flytta ExpressRoute-kretsar från den klassiska distributions modellen till Resource Manager](../expressroute/expressroute-move.md).|
| Azure App Service |Virtuella nätverk som innehåller App Service miljöer |Detta stöds inte för närvarande. |
| Azure HDInsight |Virtuella nätverk som innehåller HDInsight-tjänster |Detta stöds inte för närvarande. |
| Microsoft Dynamics Lifecycle-tjänster |Virtuella nätverk som innehåller virtuella datorer som hanteras av Dynamics Lifecycle Services |Detta stöds inte för närvarande. |
| Azure API Management |Virtuella nätverk som innehåller Azure API Management-distributioner |Detta stöds inte för närvarande. Om du vill migrera IaaS VNET ändrar du VNET för API Management distributionen, vilket inte är en drift stillestånds åtgärd. |

## <a name="next-steps"></a>Nästa steg

* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md)
