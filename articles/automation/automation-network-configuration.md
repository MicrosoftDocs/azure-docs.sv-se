---
title: Azure Automation information om nätverks konfiguration
description: Den här artikeln innehåller information om nätverks information som krävs för Azure Automation tillstånds konfiguration, Azure Automation Hybrid Runbook Worker, Uppdateringshantering och Ändringsspårning och inventering
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900328"
---
# <a name="azure-automation-network-configuration-details"></a>Azure Automation information om nätverks konfiguration

Den här sidan innehåller nätverksinformation som krävs för [hybrid Runbook Worker och tillstånds konfiguration](#hybrid-runbook-worker-and-state-configuration)och för [uppdateringshantering och ändringsspårning och inventering](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker och tillstånds konfiguration

Följande port och URL-adresser krävs för att Hybrid Runbook Worker och för att [automatiserings tillstånds konfigurationen](automation-dsc-overview.md) ska kunna kommunicera med Azure Automation.

* Port: endast 443 krävs för utgående Internet åtkomst
* Global URL: `*.azure-automation.net`
* Global URL för US Gov, Virginia: `*.azure-automation.us`
* Agent tjänst: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Nätverks planering för Hybrid Runbook Worker

För att en system-eller användar Hybrid Runbook Worker ska kunna ansluta till och registrera med Azure Automation måste den ha åtkomst till port nummer och URL: er som beskrivs i det här avsnittet. Arbets tagaren måste också ha åtkomst till de [portar och URL: er som krävs för att Log Analytics agenten](../azure-monitor/platform/agent-windows.md) ska kunna ansluta till Azure Monitor Log Analytics-arbetsytan.

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa Hybrid Runbook Worker kommunikation till det regionala data centret. Granska [DNS-posterna som används av Azure Automation](how-to/automation-region-dns-records.md) för de DNS-poster som krävs.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Konfiguration av privata nätverk för tillstånds konfiguration

Om noderna finns i ett privat nätverk krävs port och URL: er som definierats ovan. De här resurserna ger nätverks anslutningen till den hanterade noden och tillåter DSC att kommunicera med Azure Automation.

Om du använder DSC-resurser som kommunicerar mellan noder, till exempel [waitfor *-resurserna](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), måste du också tillåta trafik mellan noderna. Se dokumentationen för varje DSC-resurs för att förstå dessa nätverks krav.

Information om klient kraven för TLS 1,2 finns i [TLS 1,2 Enforcement för Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Uppdateringshantering och Ändringsspårning och inventering

Adresserna i den här tabellen är obligatoriska både för Uppdateringshantering och för Ändringsspårning och inventering. Stycket som följer tabellen gäller även för båda.

Kommunikationen med de här adresserna använder **port 443**.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*. ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*. oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

När du skapar säkerhets regler för nätverks grupper eller konfigurerar Azure-brandväggen för att tillåta trafik till Automation-tjänsten och arbets ytan Log Analytics använder du [service tag-](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** och **AzureMonitor**. Detta fören klar den löpande hanteringen av dina nätverks säkerhets regler. Om du vill ansluta till Automation-tjänsten från dina virtuella Azure-datorer på ett säkert och privat sätt kan du läsa [Använd Azure privat länk](./how-to/private-link-security.md). För att hämta den aktuella service tag-koden och intervall informationen som ska ingå som en del av dina lokala brand Väggs konfigurationer, se [nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Automation uppdateringshantering-översikt](update-management\overview.md).
* Läs mer om [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Lär dig mer om [ändringsspårning och inventering](change-tracking\overview.md).
* Läs mer om [konfiguration av automatiserings tillstånd](automation-dsc-overview.md).
