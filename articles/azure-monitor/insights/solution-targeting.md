---
title: Mål för övervaknings lösningar i Azure Monitor | Microsoft Docs
description: Med mål övervaknings lösningar kan du begränsa övervaknings lösningar till en angiven uppsättning agenter.  Den här artikeln beskriver hur du skapar en omfattnings konfiguration och tillämpar den på en lösning.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728577"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Mål för övervaknings lösningar i Azure Monitor (för hands version)
När du lägger till en övervaknings lösning i din prenumeration distribueras den automatiskt som standard till alla Windows-och Linux-agenter som är anslutna till din Log Analytics-arbetsyta.  Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter.  Den här artikeln beskriver hur du använder **lösnings mål** som är en funktion som gör att du kan använda ett omfång för dina lösningar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Så här riktar du en lösning
Det finns tre steg för att rikta en lösning enligt beskrivningen i följande avsnitt. 


### <a name="1-create-a-computer-group"></a>1. skapa en dator grupp
Du anger de datorer som du vill ska ingå i ett omfång genom att skapa en [dator grupp](../logs/computer-groups.md) i Azure Monitor.  Dator gruppen kan baseras på en logg fråga eller importeras från andra källor, till exempel Active Directory eller WSUS-grupper. Som [beskrivs nedan](#solutions-and-agents-that-cant-be-targeted)kommer endast datorer som är direkt anslutna till Azure monitor att ingå i omfånget.

När du har skapat dator gruppen i din arbets yta inkluderar du den i en omfattnings konfiguration som kan tillämpas på en eller flera lösningar.
 
 
### <a name="2-create-a-scope-configuration"></a>2. skapa en omfattnings konfiguration
 En **omfattnings konfiguration** inkluderar en eller flera dator grupper och kan tillämpas på en eller flera lösningar. 
 
 Skapa en omfattnings konfiguration med följande process.  

 1. I Azure Portal navigerar du till **Log Analytics arbets ytor** och väljer din arbets yta.
 2. I egenskaperna för arbets ytan under **arbets ytans data källor** väljer du **scope-konfigurationer**.
 3. Klicka på **Lägg till** för att skapa en ny omfattnings konfiguration.
 4. Ange ett **namn** för omfattnings konfigurationen.
 5. Klicka på **Välj dator grupper**.
 6. Välj den dator grupp som du skapade och eventuellt andra grupper som ska läggas till i konfigurationen.  Klicka på **Välj**.  
 6. Klicka på **OK** för att skapa omfattnings konfigurationen. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Använd omfattnings konfigurationen för en lösning.
När du har en omfattnings konfiguration kan du tillämpa den på en eller flera lösningar.  Observera att även om du kan använda en enda omfattnings konfiguration med flera lösningar kan varje lösning bara använda en omfattnings konfiguration.

Använd en omfattnings konfiguration med följande process.  

 1. I Azure Portal navigerar du till **Log Analytics arbets ytor** och väljer din arbets yta.
 2. I egenskaperna för arbets ytan väljer du **lösningar**.
 3. Klicka på den lösning som du vill använda som definitions område.
 4. I egenskaperna för lösningen under **data källor för arbets ytor** väljer du **lösnings mål**.  Om alternativet inte är tillgängligt [kan den här lösningen inte riktas mot målet](#solutions-and-agents-that-cant-be-targeted).
 5. Klicka på **Lägg till omfattnings konfiguration**.  Om du redan har en konfiguration som tillämpas på den här lösningen kommer det här alternativet att vara otillgängligt.  Du måste ta bort den befintliga konfigurationen innan du lägger till en ny.
 6. Klicka på den omfattnings konfiguration som du skapade.
 7. Se **status** för konfigurationen för att kontrol lera att den visas **korrekt**.  Om statusen indikerar ett fel klickar du på ellipsen till höger om konfigurationen och väljer **Redigera omfattnings konfiguration** för att göra ändringar.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Lösningar och agenter som inte kan riktas till
Följande är kriterier för agenter och lösningar som inte kan användas med lösnings mål.

- Lösnings mål gäller endast för lösningar som distribuerar till agenter.
- Lösnings mål gäller endast för lösningar som tillhandahålls av Microsoft.  Den gäller inte för lösningar [som skapats av dig själv eller partner](./solutions.md).
- Du kan bara filtrera ut agenter som ansluter direkt till Azure Monitor.  Lösningar distribueras automatiskt till alla agenter som ingår i en ansluten Operations Manager hanterings grupp oavsett om de ingår i en omfattnings konfiguration eller inte.

### <a name="exceptions"></a>Undantag
Lösnings mål kan inte användas med följande lösningar även om de uppfyller de angivna kriterierna.

- Agenthälsa utvärdering

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om övervaknings lösningar inklusive de lösningar som är tillgängliga för installation i din miljö genom [att lägga till Azure Log Analytics Monitoring-lösningar på din arbets yta](solutions.md).
- Lär dig mer om att skapa dator grupper [i dator grupper i Azure Monitor logg frågor](../logs/computer-groups.md).
