---
title: Aktivitets loggar i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln innehåller steg för att Visa aktivitets loggar för Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582776"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Visa aktivitets loggar för labb i Azure DevTest Labs 
När du har skapat ett eller flera labb vill du förmodligen övervaka hur och när dina labb används, ändras och hanteras, och av vem. Azure DevTest Labs använder Azure Monitor, särskilt **aktivitets loggar**, för att tillhandahålla information om dessa åtgärder mot labb. 

Den här artikeln förklarar hur du visar aktivitets loggar för ett labb i Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Visa aktivitets loggen för ett labb

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i avsnittet **DEVOPS** . Om du väljer * (Star) bredvid **DevTest Labs** i avsnittet **DEVOPS** . Den här åtgärden lägger till **DevTest Labs** i den vänstra navigerings menyn så att du enkelt kan komma åt dem nästa gång. Sedan kan du välja **DevTest Labs** i den vänstra navigerings menyn.

    ![Alla tjänster – Välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. I listan med labb väljer du ditt labb.
1. På Start sidan för labbet väljer du **konfigurationer och principer** på den vänstra menyn. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Välj konfiguration och principer på den vänstra menyn":::
1. På sidan **konfiguration och principer** väljer du **aktivitets logg** på den vänstra menyn under **Hantera**. Du bör se poster för åtgärder som utförs i labbet. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Aktivitetslogg":::    
1. Välj en händelse om du vill visa information om den. På sidan **Sammanfattning** visas information som åtgärds namn, tidstämpel och vem som gjorde åtgärden. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Stoppa VM-händelse – Sammanfattning":::        
1. Växla till **JSON** -fliken om du vill visa mer information. I följande exempel kan du se namnet på den virtuella datorn och åtgärden som utfördes på den virtuella datorn (Stoppad).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Stoppa VM-händelse – JSON":::           
1. Växla till fliken **ändrings historik (förhands granskning)** om du vill se historiken för ändringar. I följande exempel visas den ändring som gjorts på den virtuella datorn. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Stoppa VM-händelse – ändrings historik":::             
1. Välj ändringen i listan Ändra historik för att se mer information om ändringen. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Stoppa VM-händelse – ändrings information":::             

Mer information om aktivitets loggar finns i [Azure aktivitets logg](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du anger **aviseringar** för aktivitets loggar finns i [skapa aviseringar](create-alerts.md).
- Mer information om aktivitets loggar finns i  [Azure aktivitets logg](../azure-monitor/essentials/activity-log.md).

