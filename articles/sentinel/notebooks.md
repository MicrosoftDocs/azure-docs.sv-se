---
title: Använda antecknings böcker med Azure Sentinel för säkerhets jakt
description: Den här artikeln beskriver hur du använder antecknings böcker med funktionerna i Azure Sentinel-jakt.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092201"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Använda Jupyter Notebook för att söka efter säkerhetshot

Stiftelsen i Azure Sentinel är data lagret. den kombinerar högpresterande frågor, dynamiskt schema och skalar till enorma data volymer. Azure Portal och alla Azure Sentinel-verktyg använder ett gemensamt API för att få åtkomst till det här data lagret. Samma API är också tillgängligt för externa verktyg som [Jupyter](https://jupyter.org/) Notebooks och python. Många vanliga uppgifter kan utföras i portalen, och Jupyter utökar omfattningen av vad du kan göra med dessa data. Den kombinerar fullständig programmering med en enorm samling bibliotek för maskin inlärning, visualisering och data analys. Dessa attribut gör Jupyter till ett övertygande verktyg för säkerhets undersökning och jakt.

![exempel antecknings bok](./media/notebooks/sentinel-notebooks-map.png)

Vi har integrerat Jupyter-upplevelsen i Azure Portal, vilket gör det enkelt för dig att skapa och köra antecknings böcker för att analysera dina data. *Kqlmagic* -biblioteket ger ett lim som gör det möjligt att ta frågor från Azure Sentinel och köra dem direkt i en bärbar dator. Frågor använder [Kusto-frågespråket](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Flera antecknings böcker, som har utvecklats av några av Microsofts säkerhetsanalytiker, paketeras med Azure Sentinel. Några av dessa antecknings böcker är utformade för ett visst scenario och kan användas som de är. Andra är avsedda som exempel för att illustrera tekniker och funktioner som du kan kopiera eller anpassa för användning i dina egna antecknings böcker. Andra antecknings böcker kan också importeras från community-GitHub för Azure Sentinel.

Den integrerade Jupyter-miljön använder [Azure Notebooks](https://notebooks.azure.com/) för att lagra, dela och köra antecknings böcker. Du kan också köra dessa antecknings böcker lokalt om du har en python-miljö och Jupyter på din dator eller i andra JupterHub-miljöer som Azure Databricks.

Antecknings böcker har två komponenter:

- Det webbläsarbaserade gränssnittet där du anger och kör frågor och kod och var resultatet av körningen visas.
- En *kernel* som ansvarar för parsning och körning av koden.

Azure Sentinel Notebooks kernel körs på en virtuell Azure-dator (VM). Det finns flera licens alternativ för att utnyttja mer kraftfulla virtuella datorer om dina antecknings böcker innehåller komplexa Machine Learning-modeller.

Azure Sentinel-anteckningsbokarna använder många populära python-bibliotek som Pandas, matplotlib, bokeh och andra. Det finns många andra python-paket som du kan välja bland, som omfattar områden som:

- Visualiseringar och grafik
- Data bearbetning och analys
- Statistik och numerisk data behandling
- Maskininlärning och djupinlärning

Vi har också släppt några Jupyter säkerhets verktyg med öppen källkod i ett paket med namnet [msticpy](https://github.com/Microsoft/msticpy/). Det här paketet används i många av de antecknings böcker som ingår. Msticpy-verktyg är särskilt utformade för att hjälpa dig att skapa antecknings böcker för jakt och undersökning och vi arbetar aktivt med nya funktioner och förbättringar.

[Azure Sentinel community GitHub-lagringsplatsen](https://github.com/Azure/Azure-Sentinel) är platsen för alla framtida Azure Sentinel-anteckningsböcker som skapats av Microsoft eller som har bidragit från communityn.

Om du vill använda antecknings böckerna måste du först skapa en arbets yta för Azure Machine Learning (ML).

## <a name="create-an-azure-ml-workspace"></a>Skapa en Azure ML-arbetsyta

1. Från Azure Portal navigerar du till   >    >  **antecknings böcker** för Azure Sentinel Threat Management och väljer sedan **Starta antecknings bok**.

    > [!div class="mx-imgBorder"]
    > ![Starta Notebook för att starta Azure ml-arbetsytan](./media/notebooks/sentinel-notebooks-launch.png)

1. Under **azureml arbets yta** väljer du **Skapa ny**.

    > [!div class="mx-imgBorder"]
    > ![skapa arbets yta](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. På sidan **Machine Learning** anger du följande information och väljer sedan **Granska + skapa**.

    |Fält|Beskrivning|
    |--|--|
    |Prenumeration|Välj den Azure-prenumeration som du vill använda.|
    |Resursgrupp|Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AzureMLRG**.|
    |Namn på arbetsyta|Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **testworkspace1**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.|
    |Region|Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.|
    |Arbetsyte version|Välj **Basic** som arbets ytans typ i det här exemplet. Typ av arbets yta (Basic & Enterprise) bestämmer vilka funktioner du kommer att ha åtkomst till och prissättning.|

    > [!div class="mx-imgBorder"]
    > ![Ange information om arbets ytan](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Granska informationen, kontrol lera att den är korrekt och välj sedan **skapa** för att starta distributionen av din arbets yta.

    > [!div class="mx-imgBorder"]
    > ![granska information om arbets ytan](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Det kan ta flera minuter att skapa din arbets yta i molnet under vilken tid **översikts** sidan visar den aktuella distributions statusen.

    > [!div class="mx-imgBorder"]
    > ![distribution av arbets yta](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

När distributionen är klar kan du starta antecknings böcker i din nya Azure ML-arbetsyta.

> [!div class="mx-imgBorder"]
> ![arbets ytan har distribuerats](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Starta en bärbar dator med din Azure ML-arbetsyta

1. Från Azure Portal navigerar du till   >    >  **antecknings böcker** för Azure Sentinel Threat Management, där du kan se antecknings böcker som Azure Sentinel tillhandahåller.

    > [!TIP]
    > Välj **guider & feedback** för att öppna ett fönster med ytterligare hjälp och vägledning om antecknings böcker.
    > ![Visa Notebook-handböcker](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Välj enskilda antecknings böcker för att Visa beskrivningar, nödvändiga data typer och data källor.

    > [!div class="mx-imgBorder"]
    > ![Visa information om bärbara datorer](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Välj den antecknings bok som du vill använda och välj sedan **Starta Notebook** för att klona och konfigurera antecknings boken i ett nytt Azure Notebooks projekt som ansluter till din Azure Sentinel-arbetsyta. När processen är klar öppnas antecknings boken i Azure Notebooks som du kan köra.

    > [!div class="mx-imgBorder"]
    > ![Välj antecknings bok](./media/notebooks/sentinel-azure-notebooks-select.png)

1. Under arbets ytan AzureML väljer du din Azure ML-arbetsyta och väljer sedan **Starta**.

    > [!div class="mx-imgBorder"]
    > ![Starta Notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Välj en beräknings instans. Om du inte har en beräknings instans gör du följande:
    1. Välj plus tecknet (+) för att starta guiden **ny beräknings instans** .

        > [!div class="mx-imgBorder"]
        > ![Starta beräknings instans guiden](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Ange den information som krävs på sidan **ny beräknings instans** och välj sedan **skapa**.

        > [!div class="mx-imgBorder"]
        > ![Skapa beräknings instans](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. När din Notebook-Server har skapats, i varje cell, väljer du körnings ikonen för att köra kod i antecknings böckerna.

    > [!div class="mx-imgBorder"]
    > ![Kör antecknings boken](./media/notebooks/sentinel-azure-notebooks-run.png)

Rekommendationer:

- En snabb introduktion till att fråga data i Azure Sentinel finns i [komma igång med Azure ml-anteckningsböcker och Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) -guide.

- Du hittar fler exempel på antecknings böcker i undermappen [**exempel – Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) GitHub. De här exempel antecknings böckerna har sparats med data så att det blir enklare att se avsedda utdata. Vi rekommenderar att du visar dessa antecknings böcker i [nbviewer](https://nbviewer.jupyter.org/).

- Undermappen [**howtos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) GitHub innehåller antecknings böcker, till exempel: ställa in standard-python-version, konfigurera en DSVM, skapa Azure Sentinel-bokmärken från en bärbar dator och andra ämnen.

De antecknings böcker som tillhandahålls är både användbara verktyg och som illustrationer och kod exempel som du kan använda i utvecklingen av dina egna antecknings böcker.

Vi välkomnar feedback, om förslag, förfrågningar om funktioner, antecknings böcker som har bidragit, fel rapporter eller förbättringar och tillägg till befintliga antecknings böcker. Gå till [Azure Sentinel community-GitHub](https://github.com/Azure/Azure-Sentinel) för att skapa ett ärende eller en förgrening och ladda upp ett bidrag.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kommer igång med Jupyter Notebook i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Söker proaktivt efter hot](hunting.md)
- [Använd bok märken för att spara intressant information under jakt](bookmarks.md)
