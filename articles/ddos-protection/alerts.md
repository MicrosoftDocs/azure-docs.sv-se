---
title: Visa och konfigurera DDoS-skydds varningar för Azure DDoS Protection standard
description: Lär dig att visa och konfigurera DDoS-skydds varningar för Azure DDoS Protection standard.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: d9b77def3ccefe3c866ccef78684d38da0b8a268
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915155"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Visa och konfigurera aviseringar för DDoS-skydd

Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. Avancerad telemetri exponeras via Azure Monitor inklusive detaljerade mått under ett DDoS-angrepp. Aviseringar kan konfigureras för alla Azure Monitor mått som visas av DDoS Protection. Loggning kan integreras ytterligare med [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Konfigurera aviseringar via Azure Monitor
> * Konfigurera aviseringar via portalen
> * Visa aviseringar i Azure Security Center
> * Verifiera och testa dina aviseringar

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md) och DDoS Protection standard måste aktive ras på ett virtuellt nätverk.
- DDoS övervakar offentliga IP-adresser tilldelade till resurser i ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) som listas i [virtuellt nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inklusive Azure Load Balancer där de virtuella datorerna finns i det virtuella nätverket), förutom för Azure App Service miljöer. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator.     

## <a name="configure-alerts-through-azure-monitor"></a>Konfigurera aviseringar via Azure Monitor

Med dessa mallar kan du konfigurera aviseringar för alla offentliga IP-adresser som du har aktiverat diagnostisk inloggning på. För att kunna använda dessa aviserings mallar måste du först ha en Log Analytics-arbetsyta med aktiverade diagnostiska inställningar. Se [Visa och konfigurera DDoS-diagnostisk loggning](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Monitor varnings regel
Den här [Azure Monitor varnings regeln](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) kör en enkel fråga för att identifiera när en aktiv DDoS-minskning inträffar. Detta tyder på ett potentiellt angrepp. Åtgärds grupper kan användas för att anropa åtgärder som ett resultat av aviseringen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Azure Monitor aviserings regel med Logic app

Den här [mallen](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/DDoS%20Mitigation%20Alert%20Enrichment) distribuerar de nödvändiga komponenterna i en berikad DDoS-lösning för minskning: Azure Monitor varnings regel, åtgärds grupp och Logic-app. Resultatet av processen är en e-postavisering med information om IP-adressen under angrepp, inklusive information om den resurs som är associerad med IP-adressen. Ägaren till resursen läggs till som mottagare av e-postmeddelandet, tillsammans med säkerhets teamet. Ett grundläggande program tillgänglighets test utförs också och resultaten ingår i e-postaviseringen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Konfigurera aviseringar via portalen

Du kan välja något av de tillgängliga DDoS-skydds måtten för att varna dig när det finns en aktiv minskning under ett angrepp med hjälp av Azure Monitor varnings konfiguration. 

1. Logga in på [Azure Portal](https://portal.azure.com/) och bläddra till din DDoS Protection-plan.
2. Gå till **Övervakning** och välj **Mått**.
3. I det grå navigerings fältet väljer du **ny aviserings regel**. 
4. Ange eller Välj dina egna värden eller ange följande exempel värden, acceptera återstående standardvärden och välj sedan **skapa aviserings regel**:

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    | Omfång                   | Välj **Välj resurs**. </br> Välj den **prenumeration** som innehåller den offentliga IP-adress som du vill logga, Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill använda för att logga mått för. </br> Välj **Klar**. | 
    | Villkor | Välj **Välj villkor**. </br> Under signal namn väljer du **under DDoS-attack eller inte**. </br> Under **operator** väljer du **större än eller lika** med. </br> Under **agg regerings typ** väljer du **Max**. </br> Under **tröskel värde** anger du *1*. **0** innebär att du inte befinner dig för **under DDoS-angrepp eller inte** , medan **1** innebär att du är utsatt för angrepp. </br> Välj **Klar**. | 
    | Actions | Välj **Lägg till åtgärds grupper**. </br> Välj **Skapa åtgärdsgrupp**. </br> Under **aviseringar**, under **meddelande typ**, väljer du **e-post/SMS meddelande/push/röst**. </br> Under **namn** anger du _MyUnderAttackEmailAlert_. </br> Klicka på knappen Redigera, Välj **e-post** och så många av följande alternativ du behöver och välj sedan **OK**. </br> Välj **Granska + skapa**. | 
    | Information om aviserings regel | Under **aviserings regelns namn** anger du _MyDdosAlert_. |

Inom några minuter av attack identifiering bör du få ett e-postmeddelande från Azure Monitor mått som liknar följande bild:

![Attack avisering](./media/manage-ddos-protection/ddos-alert.png)

Du kan också lära dig mer om hur du [konfigurerar Webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="view-alerts-in-azure-security-center"></a>Visa aviseringar i Azure Security Center

Azure Security Center innehåller en lista över [säkerhets aviseringar](../security-center/security-center-managing-and-responding-alerts.md)med information som hjälper dig att undersöka och åtgärda problem. Med den här funktionen får du en enhetlig vy över aviseringar, inklusive DDoS-relaterade aviseringar och åtgärder som vidtagits för att minimera angreppet i nära tiden.
Det finns två olika aviseringar som du kan se för identifiering och minskning av DDoS-attacker:

- **DDoS-attack identifierad för offentlig IP**: den här aviseringen skapas när DDoS-skydds tjänsten identifierar att en av dina offentliga IP-adresser är målet för en DDoS-attack.
- **DDoS-attack minimerad för offentlig IP**: den här aviseringen genereras när en attack på den offentliga IP-adressen har minimerats.
Om du vill visa aviseringarna öppnar du **Security Center** i Azure Portal. Under **skydd av hot** väljer du **säkerhets aviseringar**. Följande skärm bild visar ett exempel på DDoS-attackens aviseringar.

![DDoS-avisering i Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Aviseringarna inkluderar allmän information om den offentliga IP-adressen som är under attack, geo-och hot information och åtgärder.

## <a name="validate-and-test"></a>Validera och testa

Information om hur du simulerar en DDoS-attack för att verifiera dina aviseringar finns i [validera DDoS-identifiering](test-through-simulations.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

- Konfigurera aviseringar via Azure Monitor
- Konfigurera aviseringar via portalen
- Visa aviseringar i Azure Security Center
- Verifiera och testa dina aviseringar

Information om hur du testar och simulerar en DDoS-attack finns i test guide för simulering:

> [!div class="nextstepaction"]
> [Testa genom simuleringar](test-through-simulations.md)
