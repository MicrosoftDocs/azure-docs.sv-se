---
title: Aktivera VM-insikter med hjälp av Azure Policy
description: Beskriver hur du aktiverar VM Insights för flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a63a647f3d76e3cc2616f05fe96d86dbdd36e74d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707548"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Aktivera VM-insikter med hjälp av Azure Policy
Den här artikeln förklarar hur du aktiverar VM Insights för virtuella Azure-datorer eller en hybrid virtuell dator som är ansluten till Azure ARC (för hands version) med Azure Policy. Med Azure Policy kan du tilldela princip definitioner som installerar de nödvändiga agenterna för VM Insights i Azure-miljön och automatiskt aktivera övervakning för virtuella datorer när varje virtuell dator skapas. VM Insights innehåller en funktion som gör att du kan identifiera och reparera inkompatibla virtuella datorer i din miljö. Använd den här funktionen i stället för att arbeta direkt med Azure Policy.

Om du inte är bekant med Azure Policy får du en kort introduktion vid [distribution Azure Monitor i skala med Azure policy](../deploy-scale.md).

> [!NOTE]
> Om du vill använda Azure Policy med skalnings uppsättningar för virtuella Azure-datorer, eller om du vill arbeta med Azure Policy direkt för att aktivera virtuella Azure-datorer, se [distribuera Azure Monitor i skala med hjälp av Azure policy](../deploy-scale.md#azure-monitor-for-vms).

## <a name="prerequisites"></a>Förutsättningar
- [Skapa och konfigurera en Log Analytics-arbetsyta](./vminsights-configure-workspace.md).
- Se [operativ system som stöds](./vminsights-enable-overview.md#supported-operating-systems) för att säkerställa att operativ systemet för den virtuella datorn eller skalnings uppsättningen för virtuella datorer som du aktiverar stöds. 


## <a name="vm-insights-initiative"></a>Initiativ för VM Insights
VM Insights innehåller inbyggda princip definitioner för att installera Log Analytics agent och beroende agent på virtuella Azure-datorer. Initiativet för **att aktivera VM Insights** inkluderar var och en av dessa princip definitioner. Tilldela det här initiativet till en hanterings grupp, prenumeration eller resurs grupp för att automatiskt installera agenterna på alla virtuella Windows-eller Linux Azure-datorer i det aktuella omfånget.

## <a name="open-policy-coverage-feature"></a>Öppna princip täcknings funktion
Om du vill komma åt **principen för VM Insights-principer** går du till de **virtuella datorerna** på **Azure Monitor** -menyn i Azure Portal. Välj **andra onboarding-alternativ** och **Aktivera** sedan **Aktivera med hjälp av princip**.

[![Fliken för att komma igång med Azure Monitor från virtuella datorer](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Skapa ny tilldelning
Om du inte redan har en tilldelning skapar du en ny genom att klicka på **tilldela princip**.

[![Skapa tilldelning](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Detta är samma sida för att tilldela ett initiativ i Azure Policy förutom att det är hårdkodad med det omfång som du har valt och definitionen för att **Aktivera VM Insights** -initiativ. Du kan också ändra **tilldelnings namnet** och lägga till en **Beskrivning**. Välj **undantag** om du vill ge ett undantag till omfånget. Ditt omfång kan till exempel vara en hanterings grupp och du kan ange en prenumeration i den hanterings gruppen som ska uteslutas från tilldelningen.

[![Tilldela initiativ](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

På sidan **parametrar** väljer du en **Log Analytics arbets yta** som ska användas av alla virtuella datorer i tilldelningen. Om du vill ange olika arbets ytor för olika virtuella datorer måste du skapa flera tilldelningar, var och en med sin egen omfattning. 

   > [!NOTE]
   > Om arbets ytan överskrider tilldelningens omfattning beviljar du *Log Analytics deltagar* behörighet till princip tilldelningens huvud-ID. Om du inte gör det kan du se ett distributions haveri som `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Platsen](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Klicka på **Granska + skapa** för att granska informationen om tilldelningen innan du klickar på **skapa** för att skapa den. Skapa inte en reparations uppgift just nu eftersom du förmodligen behöver flera åtgärder för att aktivera befintliga virtuella datorer. Se [åtgärda efterföljande resultat](#remediate-compliance-results) nedan.

## <a name="review-compliance"></a>Granska efterlevnad
När du har skapat en tilldelning kan du granska och hantera täckning för ditt initiativ för att **Aktivera VM Insights** i dina hanterings grupper och prenumerationer. Detta visar hur många virtuella datorer som finns i varje hanterings grupp eller prenumerationer och deras kompatibilitetsstatus.

[![Sidan hantera princip för VM-insikter](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


Följande tabell innehåller en beskrivning av informationen i den här vyn.

| Funktion | Beskrivning | 
|----------|-------------| 
| **Omfång** | Hanterings grupp och prenumerationer som du har eller ärvt åtkomst till med möjlighet att gå nedåt i hanterings gruppens hierarki.|
| **Role** | Din roll i omfånget, som kan vara läsare, ägare eller deltagare. Detta är tomt om du har åtkomst till prenumerationen men inte till hanterings gruppen som den tillhör. Den här rollen avgör vilka data du kan se och vilka åtgärder du kan utföra när du tilldelar principer eller initiativ (ägare), redigerar dem eller visar efterlevnad. |
| **Totalt antal virtuella datorer** | Totalt antal virtuella datorer i det omfånget oavsett deras status. För en hanterings grupp är detta en total summa för virtuella datorer som är kapslade under prenumerationer eller underordnade hanterings grupper. |
| **Tilldelnings täckning** | Procent av de virtuella datorer som omfattas av initiativet. |
| **Tilldelnings status** | **Lyckades** – alla virtuella datorer i omfånget har de Log Analytics och beroende agenter som distribuerats till dem.<br>**Varning** -prenumerationen ingår inte i en hanterings grupp.<br>**Inte startat** – en ny tilldelning har lagts till.<br>**Lås** – du har inte tillräckliga privilegier för hanterings gruppen.<br>**Tom** -inga virtuella datorer finns eller så har ingen princip tilldelats. |
| **Kompatibla virtuella datorer** | Antal virtuella datorer som är kompatibla, vilket är antalet virtuella datorer som har både Log Analytics agent och beroende agent installerad. Detta kommer att vara tomt om det inte finns några tilldelningar, inga virtuella datorer i omfånget eller inte rätt behörighet. |
| **Efterlevnad** | Det övergripande kompatibilitets numret är summan av distinkta resurser som är kompatibla med summan av alla distinkta resurser. |
| **Kompatibilitetstillstånd** | **Kompatibel** – alla virtuella datorer i omfånget virtuella datorer har de Log Analytics och beroende agenter som distribuerats till dem, eller så har nya virtuella datorer i omfånget som omfattas av tilldelningen ännu inte utvärderats.<br>**Icke-kompatibel** – det finns virtuella datorer som har utvärderats men som inte är aktiverade och som kan kräva reparation.<br>**Inte startat** – en ny tilldelning har lagts till.<br>**Lås** – du har inte tillräckliga privilegier för hanterings gruppen.<br>**Tom** – ingen princip har tilldelats.  |


När du tilldelar initiativet kan omfånget som valts i tilldelningen vara omfånget eller en delmängd av det. Du kanske till exempel har skapat en tilldelning för en prenumeration (princip omfattning) och inte en hanterings grupp (täcknings område). I det här fallet indikerar värdet för **tilldelnings täckning** de virtuella datorerna i initiativ omfånget dividerat med de virtuella datorerna i täcknings omfånget. I ett annat fall kan du ha uteslutit vissa virtuella datorer, resurs grupper eller en prenumeration från princip omfattningen. Om värdet är tomt anger det att antingen principen eller initiativet inte finns eller eftersom du inte har behörighet. Information finns under **tilldelnings status**.


## <a name="remediate-compliance-results"></a>Åtgärda resultat från efterlevnad
Initiativet kommer att tillämpas på virtuella datorer när de skapas eller ändras, men de tillämpas inte på befintliga virtuella datorer. Om tilldelningen inte visar 100%-kompatibilitet, skapar du reparations aktiviteter för att utvärdera och aktivera befintliga virtuella datorer, väljer **Visa kompatibilitet** genom att välja ellipsen (...).

[![Visa efterlevnad](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

Sidan **efterlevnad** listar tilldelningar som matchar det angivna filtret och om de är kompatibla. Klicka på en tilldelning om du vill visa information om den.

[![Efterlevnadsprincip för virtuella Azure-datorer](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

Sidan **krav på initiativ** listar princip definitionerna i initiativet och om var och en uppfyller kraven.

[![Information om efterlevnad](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Klicka på en princip definition för att visa information om den. Scenarier som princip definitionerna visar som de är inte kompatibla är följande:

* Log Analytics agent eller beroende agent har inte distribuerats. Åtgärda problemet genom att skapa en reparations uppgift.
* VM-avbildningen (OS) är inte identifierad i princip definitionen. Villkoren för distributions principen inkluderar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. Läs dokumentationen för att se om det finns stöd för VM-operativsystem.
* Virtuella datorer loggar inte till den angivna Log Analytics-arbetsytan. Vissa virtuella datorer i initiativet initiativ är anslutna till en annan Log Analytics arbets yta än den som anges i princip tilldelningen.

[![Information om efterlevnadsprincip](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Om du vill skapa en reparations uppgift för att åtgärda kompatibilitetsproblem klickar du på **skapa reparations uppgift**. 

[![Ny reparations uppgift](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Klicka på **Reparera** om du vill skapa en reparations uppgift och **Reparera** sedan för att starta den. Du behöver förmodligen skapa flera reparations uppgifter, en för varje princip definition. Du kan inte skapa en reparations uppgift för ett initiativ.

[![Skärm bild som visar fönstret princip reparation för övervakaren | Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


När reparations åtgärderna har slutförts bör de virtuella datorerna vara kompatibla med agenter installerade och aktiverade för VM-insikter. 

## <a name="next-steps"></a>Nästa steg

Nu när övervakning har Aktiver ATS för dina virtuella datorer är den här informationen tillgänglig för analys med VM-insikter. 

- Information om hur du visar identifierade program beroenden finns i [Visa översikt över VM-insikter](vminsights-maps.md). 
- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
