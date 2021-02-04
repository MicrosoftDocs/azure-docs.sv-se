---
title: 'Självstudie: kontroll av regelefterlevnad – Azure Security Center'
description: 'Självstudie: Lär dig hur du kan förbättra din efterlevnad med hjälp av Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550929"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudie: Förbättra regelefterlevnaden

Azure Security Center hjälper till att effektivisera processen för att uppfylla krav på efterlevnad av efterlevnad, med hjälp av **instrument panelen för kontroll av efterlevnad**. 

Security Center kontinuerligt utvärderar din hybrid moln miljö för att analysera risk faktorer enligt de kontroller och bästa metoderna i de standarder som tillämpas på dina prenumerationer. Instrument panelen visar statusen för din efterlevnad av dessa standarder. 

När du aktiverar Security Center på en Azure-prenumeration tilldelas den automatiskt [Azures säkerhets benchmark](../security/benchmarks/introduction.md). Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Instrument panelen för kontroll av efterlevnad visar status för alla utvärderingar i din miljö för de valda standarderna och reglerna. När du arbetar med rekommendationerna och minskar riskfaktorerna i din miljö förbättras position.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Utvärdera efterlevnaden av efterlevnaden med hjälp av instrument panelen för regelefterlevnad
> * Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer
> * Konfigurera aviseringar vid ändringar i position för efterlevnad
> * Exportera dina efterlevnadsprinciper som en kontinuerlig data ström och som veckovis ögonblicks bilder

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom de funktioner som beskrivs i den här självstudien:

- [Azure Defender](azure-defender.md) måste vara aktiverat. Du kan prova Azure Defender kostnads fritt i 30 dagar.
- Du måste vara inloggad med ett konto som har Läs behörighet till policyns efterlevnadsprinciper (**säkerhets läsaren** är inte tillräcklig). Rollen som **global läsare** för prenumerationen kommer att fungera. Du måste minst ha tilldelats rollerna **resurs princip deltagare** och **säkerhets administratör** .

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Instrument panelen för kontroll av efterlevnad visar dina valda efterlevnadsprinciper med alla krav, där kraven som stöds är mappade till tillämpliga säkerhets utvärderingar. Status för dessa utvärderingar visar att standarden är uppfyllt.

Använd instrument panelen för kontroll av efterlevnad för att fokusera på de luckor som följer de standarder och föreskrifter som du har valt. Den här fokuserade vyn gör det också möjligt för dig att kontinuerligt övervaka din efterlevnad över tid inom ett dynamiskt moln och i hybrid miljöer.

1. Från Security Center menyn väljer du regelefterlevnad **.**

    Längst upp på skärmen finns en instrument panel med en översikt över din kompatibilitetsstatus med en uppsättning regler för efterlevnad av stöd. Du ser det övergripande resultatet av efterlevnaden och antalet överförings-och utvärderings resultat som är associerade med varje standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrument panel för regler för efterlevnad" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Välj en flik för en efterlevnadsprincip som är relevant för dig (1). Du ser vilka prenumerationer som standarden tillämpas på (2) och listan över alla kontroller för den standarden (3). För tillämpliga kontroller kan du Visa information om att skicka och stoppa utvärderingar som är associerade med kontrollen (4) samt antalet resurser som påverkas (5). Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center-utvärderingar. Kontrol lera kraven för dessa och utvärdera dem i din miljö på egen hand. Vissa av dessa kan vara processbaserade och inte tekniska.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Utforska informationen om kompatibilitet med en speciell standard":::

1. Välj **Hämta rapport** om du vill generera en PDF-rapport med en sammanfattning av din aktuella kompatibilitetsstatus för en viss standard.

    Rapporten ger en översikt över din kompatibilitetsstatus för den valda standarden baserat på Security Center utvärderings data och organiseras enligt kontrollerna i denna specifika standard. Rapporten kan delas med relevanta intressenter och kan ge bevis på interna och externa revisorer.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Hämta Kompatibilitetsrapport":::

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus

Med hjälp av informationen i instrument panelen för regelefterlevnad kan du förbättra position genom att lösa rekommendationer direkt på instrument panelen.

1.  Klicka dig igenom några icke godkända utvärderingar som visas i instrumentpanelen för att visa information om den här rekommendationen. Varje rekommendation innehåller en uppsättning steg som ska följas för att lösa problemet.

1.  Välj en viss resurs om du vill visa mer information och lösa rekommendationerna för resursen. <br>Till exempel, i **Azure CIS-1.1.0** standard, väljer du rekommendations **disk kryptering ska tillämpas på virtuella datorer**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Välja en rekommendation från en standard leads direkt till rekommendations informations Sidan":::

1. I det här exemplet kommer du att komma åt den virtuella Azure-datorns sidor i Azure Portal, där du kan aktivera kryptering från fliken **säkerhet** , när du väljer **vidta åtgärd** från sidan med rekommendations information:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Knappen för att vidta åtgärder på sidan med rekommendations information leder till reparations alternativen":::

    Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

1.  När du har åtgärdat för att lösa rekommendationer, ser du effekten i instrument panels rapporten för regelefterlevnad eftersom resultatet förbättras.

    > [!NOTE]
    > Utvärderingarna körs ungefär var 12: e timme, så du kommer att se påverkan på dina efterlevnadsprinciper endast efter nästa körning av den relevanta utvärderingen.


## <a name="export-your-compliance-status-data"></a>Exportera dina status data för efterlevnad

Om du vill spåra din kompatibilitetsstatus med andra övervaknings verktyg i din miljö, innehåller Security Center en export metod som gör det enkelt. Konfigurera **kontinuerlig export** för att skicka urvals data till en Azure Event Hub-eller Log Analytics-arbetsyta.

Använd kontinuerliga export data till en Azure Event Hub eller en Log Analytics arbets yta:

- Exportera alla regler för regelefterlevnad i en **kontinuerlig data ström**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportera en data ström med regler för regelefterlevnad kontinuerligt" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportera **vecko Visa ögonblicks bilder** av dina regler för regelefterlevnad:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exportera regelbundet en veckovis ögonblicks bild av regelefterlevnad" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Du kan också exportera en **PDF/CSV-rapport** för dina efterlevnadsprinciper direkt från instrument panelen för kontroll av efterlevnad:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportera dina uppgifter för regelefterlevnad som en PDF-eller CSV-rapport" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Lär dig mer om att [kontinuerligt exportera Security Center data](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Kör automatisering av arbets flöde när det finns ändringar i din efterlevnad

Security Center s funktion för arbets flödes automatisering kan utlösa Logic Apps när någon av de ändrade tillstånden för regelefterlevnad.

Till exempel kanske du vill Security Center e-posta en speciell användare när en kompatibilitetskontroll Miss lyckas. Du måste först skapa Logic app (med [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) och sedan konfigurera utlösaren i en ny arbets flödes automatisering som beskrivs i [automatiserade svar på Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Använda ändringar i regelefterlevnad för att utlösa en arbets flödes automatisering" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Security Centers instrument panel för kontroll av efterlevnad för att:

- Visa och övervaka din position för efterlevnad av de standarder och föreskrifter som är viktiga för dig.
- Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrument panelen för kontroll av efterlevnad kan förenkla processen för efterlevnad och avsevärt minska den tid som krävs för att samla in kompatibiliteten för din Azure-, hybrid-och miljö med flera moln.

Läs mer i följande relaterade sidor:

- [Anpassa uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md) regelefterlevnad – lär dig hur du väljer vilka standarder som visas på instrument panelen för kontroll av efterlevnad. 
- [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur du använder rekommendationer i Azure Security Center för att skydda dina Azure-resurser.