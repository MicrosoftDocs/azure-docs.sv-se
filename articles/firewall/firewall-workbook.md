---
title: Övervaka loggar med hjälp av Azure Firewall-arbetsboken
description: Azure Firewall-arbetsböcker ger en flexibel arbets yta för Azures analys av brand Väggs data och skapandet av rika visuella rapporter i Azure Portal.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831849"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Övervaka loggar med hjälp av Azure Firewall-arbetsboken

Azure Firewall-arbetsboken innehåller en flexibel arbets yta för analys av Azure Firewall-data. Du kan använda den för att skapa omfattande visuella rapporter i Azure Portal. Du kan trycka på flera brand väggar som distribueras i Azure och kombinera dem till enhetliga interaktiva upplevelser.

Du kan få insikter om Azure Firewall-händelser, lära dig om program-och nätverks regler och se statistik för brand Väggs aktiviteter mellan URL: er, portar och adresser. Med Azure Firewall-arbetsböcker kan du filtrera brand väggar och resurs grupper och dynamiskt filtrera per kategori med enkla att läsa data uppsättningar när du undersöker ett problem i loggarna. 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du [Aktivera diagnostisk loggning](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) via Azure Portal. Läs även [Azure Firewall-loggar och-mått](logs-and-metrics.md) för en översikt över de diagnostikloggar och mät värden som är tillgängliga för Azure-brandväggen.

## <a name="get-started"></a>Kom igång

Om du vill distribuera arbets boken går du till [Azure Monitor arbets bok för Azure-brandvägg](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) och följer anvisningarna på sidan. Azure Firewall-arbetsboken är utformad för att fungera över flera klienter, flera prenumerationer och kan filtreras till flera brand väggar.

## <a name="overview-page"></a>Översiktssidan

Översikts sidan ger dig ett sätt att filtrera över arbets ytor, tid och brand väggar. Det visar händelser efter tid i brand väggar och logg typer (program, nätverk, Hot-Intel, DNS-proxy).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Översikt över Azure Firewall-arbetsböcker":::

## <a name="application-rule-log-statistics"></a>Statistik för program regel logg

På den här sidan visas unika källor med IP-adress över tid, användning av antal program regler, nekad/tillåten FQDN över tid och filtrerade data. Du kan filtrera data baserat på IP-adress.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Program regel logg för Azure Firewall-arbetsbok":::

## <a name="network-rule-log-statistics"></a>Statistik för nätverks regel logg

Den här sidan ger en vy efter regel åtgärd – Tillåt/neka, mål Port per IP-adress och DNAT över tid. Du kan också filtrera efter åtgärd, port och måltyp.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Nätverks regel logg för Azure Firewall-arbetsboken":::

Du kan också filtrera loggar baserat på tids period:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Arbets boks fönster för Azure Firewall-arbetsbok logg tid":::

## <a name="investigations"></a>Undersökningar

Du kan titta på loggarna och ta reda på mer om resursen baserat på käll-IP-adressen. Du kan hämta information som namn på virtuell dator och nätverks gränssnitt. Det är enkelt att filtrera efter resursen från loggarna.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Undersökning av Azure Firewall-arbetsböcker":::

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Firewall Diagnostics](firewall-diagnostics.md)
