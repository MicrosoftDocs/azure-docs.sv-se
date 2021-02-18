---
title: Lägga till utökade mått för virtuella Azure-datorer
description: Den här artikeln hjälper dig att aktivera och konfigurera utökade diagnosmått för virtuella Azure-datorer.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595313"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Lägga till utökade mått för virtuella Azure-datorer

Cloudyn använder Azure-måttdata från dina virtuella Azure-datorer för att visa detaljerad information om deras resurser. Måttdata, som även kallas prestandaräknare, används av Cloudyn för att generera rapporter. Cloudyn samlar dock inte automatiskt in alla Azure-måttdata från virtuella gästdatorer – du måste aktivera måttinsamling. Den här artikeln hjälper dig att aktivera och konfigurera ytterligare diagnosmått för virtuella Azure-datorer.

När du har aktiverat måttinsamling kan du:

- Ta reda på när dina virtuella datorer når sina gränser för minne, disk och CPU.
- Identifiera användningstrender och avvikelser.
- Kontrollera kostnaderna genom att ändra storlek enligt användning.
- Få kostnadseffektiva rekommendationer för storleksoptimering från Cloudyn.

Till exempel vill du kanske övervaka CPU % och minne % för dina virtuella Azure-datorer. Måtten för virtuella Azure-datorer motsvarar _Procent CPU_ och _\Memory\% Allokerade byte som används_.

> [!NOTE]
> Utökad måttdatainsamling stöds endast med Azure-övervakning på gästnivå. Cloudyn är inte kompatibelt med [Log Analytics-agenten](../../azure-monitor/agents/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Ta reda på om utökade mått är aktiverade

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Under **Virtuella datorer** väljer du en virtuell dator. Under **Övervakning** väljer du sedan **Mått**. En lista över tillgängliga mått visas.
3. Välj några mått så visar en graf data för dem.  
    ![Exempelmått – värdprocent-CPU](./media/azure-vm-extended-metrics/metric01.png)

I föregående exempel är en begränsad uppsättning standardmått tillgängliga för dina värdar, men minnesmått är inte det. Minnesmått är en del av utökade mått. I det här fallet har utökade mått inte aktiverats för den virtuella datorn. Du måste utföra några ytterligare steg för att aktivera utökade mått. Följande information hjälper dig att aktivera dem.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Aktivera utökade mått i Azure-portalen

Standardmått är mått för datorvärdar. Måttet _Procent CPU_ är ett exempel. Det finns även grundläggande mått för virtuella gästdatorer, och de kallas också utökade mått. Exempel på utökade mått är _\Memory\% Allokerade byte som används_ och _\Memory\Tillgängliga byte_.

Det är enkelt att aktivera utökade mått. För varje virtuell dator aktiverar du övervakning på gästnivå. När du aktiverar övervakning på gästnivå installeras Azure Diagnostics-agenten på den virtuella datorn. Som standard läggs en grundläggande uppsättning utökade mått till. Följande process är samma för klassiska och vanliga virtuella datorer och samma för virtuella Windows-datorer och Linux-datorer.

Kom ihåg att övervakning på både Windows- och Linux-gästnivå kräver ett lagringskonto. Om du inte väljer ett befintligt lagringskonto när du aktiverar övervakning på gästnivå skapas ett åt dig.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Aktivera övervakning på gästnivå på befintliga virtuella datorer

1. I **Virtual Machines** visar du din lista över virtuella datorer och väljer sedan en virtuell dator.
2. Under **Övervakning** väljer du **Diagnostikinställningar**.
3. På sidan Diagnostikinställningar klickar du på **Aktivera övervakning på gästnivå**.  
    ![Aktivera övervakning på gästnivå på sidan Översikt](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Efter några minuter installeras Azure Diagnostics-agenten på den virtuella datorn. En grundläggande uppsättning mått läggs till. Uppdatera sidan. De nya prestandaräknarna visas på fliken Översikt.
5. Under Övervakning väljer du **Mått**.
6. I måttdiagrammet under **Namnområde för mått** väljer du **Gäst (klassisk)** .
7. I listan Mått kan du visa alla tillgängliga prestandaräknare för den virtuella gästdatorn.  
    ![lista över exempel på utökade mått](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aktivera övervakning på gästnivå på nya virtuella datorer

När du skapar nya virtuella datorer går du till fliken Hantering och väljer **På** för **Diagnostik för gästoperativsystem**.

![ange Diagnostik för gästoperativsystem till På](./media/azure-vm-extended-metrics/new-enable-diag.png)

Mer information om hur du aktiverar utökade mått för virtuella Azure-datorer finns i [Förstå och använda Azure Linux-agenten](../../virtual-machines/extensions/agent-linux.md) och i [översikten av Azure VM-agenten](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Autentiseringsuppgifter för Resource Manager

När du har aktiverat utökade mått kontrollerar du att Cloudyn har åtkomst till dina [autentiseringsuppgifter för Resource Manager](./activate-subs-accounts.md). Dina autentiseringsuppgifter krävs för att Cloudyn ska kunna samla in och visa prestandadata för dina virtuella datorer. De används även för att skapa rekommendationer om kostnadsoptimering. Cloudyn behöver minst tre dagars prestandadata från en instans för att avgöra om den är en kandidat för en rekommendation om nedskalning.

## <a name="enable-vm-metrics-with-a-script"></a>Aktivera VM-mått med ett skript

Du kan aktivera VM-mått med Azure PowerShell-skript. När du har många virtuella datorer som du vill aktivera mått för kan du använda ett skript för att automatisera processen. Exempelskript finns på GitHub på [Aktivera diagnostik i Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Visa Azure-prestandamått

Om du vill visa prestandamått för Azure-instanser i Cloudyn-portalen går du till **Tillgångar** > **Compute** > **Instance Explorer**. I listan över VM-instanser expanderar du en instans och expanderar sedan en resurs för att visa information.

![exempelinformation som visas i Instance Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har aktiverat Azure Resource Manager API-åtkomst för dina konton fortsätter du till [Aktivera Azure-prenumerationer och -konton](./activate-subs-accounts.md).