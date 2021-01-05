---
title: Stöd för fysisk server utvärdering i Azure Migrate
description: Läs mer om stöd för fysisk server-utvärdering med Azure Migrate Server-utvärdering
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 2be77a47c4b111dd2f25a8fc9ca35690d1b2d80c
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796762"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Support mat ris för fysisk server-utvärdering 

Den här artikeln sammanfattar krav och support när du bedömer fysiska servrar för migrering till Azure med hjälp av verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Om du vill migrera fysiska servrar till Azure läser du [matrisen migration support](migrate-support-matrix-physical-migration.md).


Om du vill utvärdera fysiska servrar skapar du ett Azure Migrate-projekt och lägger till verktyget Server bedömning i projektet. När du har lagt till verktyget distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar metadata och prestanda data till Azure. När identifieringen är klar samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Information**
--- | ---
**Utvärderings gränser** | Du kan identifiera och utvärdera upp till 35 000 fysiska servrar i ett enda [Azure Migrate projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Förutom fysiska servrar kan ett projekt inkludera virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna för var och en.
**Identifikation** | Azure Migrates apparaten kan identifiera upp till 1000 fysiska servrar.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="physical-server-requirements"></a>Krav för fysisk server

**Distribution av fysisk server:** Den fysiska servern kan vara fristående eller distribuerad i ett kluster.

**Operativ system:** Alla Windows-och Linux-operativsystem kan utvärderas för migrering.

**Behörigheter:**
- För Windows-servrar använder du ett domän konto för domänanslutna datorer och ett lokalt konto för datorer som inte är domänanslutna. Användarkontot bör läggas till i dessa grupper: Fjärrhanteringsanvändare, Användare av prestandaövervakning och Användare av prestandaloggar.
- För Linux-servrar behöver du ett rotkonto på de Linux-servrar som du vill identifiera. Alternativt kan du ange ett icke-rot konto med de funktioner som krävs med hjälp av följande kommandon:

**Kommando** | **Syfte**
--- | --- |
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH + EIP/sbin/fdisk _(om/usr/sbin/fdisk inte finns)_ | Samla in disk konfigurations data
setcap "cap_dac_override, cap_dac_read_search, cap_fowner, cap_fsetid, cap_setuid,<br>cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin,<br>cap_sys_resource, cap_audit_control, cap_setfcap = + EIP "/sbin/LVM | Samla in disk prestanda data
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/dmidecode | Samla in BIOS-serienummer
chmod a + r/sys/Class/DMI/ID/product_uuid | Samla in BIOS-GUID



## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Installationen av fysiska servrar kan köras på en virtuell dator eller på en fysisk dator. 

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---physical) för fysiska servrar.
- Lär dig mer om webb adresser som behövs för att få åtkomst till [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.
- Du ställer in installationen med hjälp av ett [PowerShell-skript](how-to-set-up-appliance-physical.md) som du hämtar från Azure Portal.
I Azure Government distribuerar du enheten [med det här skriptet](deploy-appliance-script-government.md).

## <a name="port-access"></a>Port åtkomst

I följande tabell sammanfattas port kraven för utvärdering.

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för att fjärrans luta till appen för enhets hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Utgående anslutningar på portarna 443 (HTTPS) för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutning på WinRM-port 5985 (HTTP) eller 5986 (HTTPS) för att hämta konfigurations-och prestanda-metadata från Windows-servrar. <br/><br/> **Linux:**  Inkommande anslutningar på port 22 (TCP) för att hämta konfigurations-och prestanda-metadata från Linux-servrar. |

## <a name="agent-based-dependency-analysis-requirements"></a>Krav för agent-baserade beroende analyser

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. I tabellen sammanfattas kraven för att skapa en agent beroende analys. För närvarande stöds endast agentbaserade beroende analyser för fysiska servrar.

**Krav** | **Information** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget för Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för utvärdering av [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Azure Government** | Beroende visualisering är inte tillgänglig i Azure Government.
**Log Analytics** | Azure Migrate använder [tjänstkarta](../azure-monitor/insights/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate-projekt. Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.
**Agenter som krävs** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till Internet måste du ladda ned och installera Log Analytics gateway på dem.<br/><br/> Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics arbets yta** | Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
**Kostnader** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med Azure Migrate projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.
**Internetanslutning** | Om datorerna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.
**Azure Government** | Agent-baserad beroende analys stöds inte.

## <a name="next-steps"></a>Nästa steg

[Förbered för utvärdering av fysisk server](./tutorial-discover-physical.md).