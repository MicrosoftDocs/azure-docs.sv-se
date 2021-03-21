---
title: Transformera data i designern
titleSuffix: Azure Machine Learning
description: Lär dig hur du importerar och transformerar data i Azure Machine Learning designer för att skapa dina egna data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 10904275d382c7f9dbf223ded78cab0adf14992a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661060"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformera data i Azure Machine Learning designer


I den här artikeln får du lära dig hur du omformar och sparar data uppsättningar i Azure Machine Learning designer så att du kan förbereda dina egna data för Machine Learning.

Du kommer att använda den [binära klassificerings data uppsättningen för vuxen räknings inkomst](./samples-designer.md) för att förbereda två data uppsättningar: en data uppsättning som innehåller information om vuxen räkning från endast USA och en annan data uppsättning som innehåller information om inventering från icke-USA vuxna.

I den här artikeln kan du se hur du:

1. Omvandla en data uppsättning för att förbereda den för utbildning.
1. Exportera de resulterande data uppsättningarna till ett data lager.
1. Visa resultat.

Den här instruktionen är en förutsättning för artikeln om [hur du omtränar designer-modeller](how-to-retrain-designer.md) . I den artikeln får du lära dig hur du använder transformerade data uppsättningar för att träna flera modeller med pipeline-parametrar.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformera en data uppsättning

I det här avsnittet får du lära dig hur du importerar exempel data uppsättningen och delar upp data i andra data uppsättningar än USA. Mer information om hur du importerar dina egna data till designern finns i [så här importerar du data](how-to-designer-import-data.md).

### <a name="import-data"></a>Importera data

Använd följande steg för att importera exempel data uppsättningen.

1. Logga in på <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>och välj den arbets yta som du vill arbeta med.

1. Gå till designern. Välj **moduler** som är lättanvända för att använda för att skapa en ny pipeline.

1. Välj ett standard beräknings mål för att köra pipelinen.

1. Till vänster om arbets ytan för pipelinen är en palett med data uppsättningar och moduler. Välj **data uppsättningar**. Visa sedan avsnittet **samples** .

1. Dra och släpp den **binära klassificerings data uppsättningen för vuxen räknings inkomst** på arbets ytan.

1. Högerklicka på den data uppsättnings modulen för **vuxen räknings** data och välj **visualisera**  >  **utdata för data uppsättning**

1. Använd fönstret data förhands granskning för att utforska data uppsättningen. Titta på en särskild anteckning om kolumn värden för "ursprungligt land".

### <a name="split-the-data"></a>Dela upp data

I det här avsnittet använder du [modulen dela data](algorithm-module-reference/split-data.md) för att identifiera och dela upp rader som innehåller "United-staterna" i kolumnen "internt land". 

1. I paletten modul till vänster om arbets ytan, expanderar du avsnittet **datatransformering** och letar reda på modulen **dela data** .

1. Dra modulen **dela data** till arbets ytan och släpp modulen under data uppsättnings modulen.

1. Anslut data uppsättnings modulen till modulen **dela data** .

1. Välj modulen **dela data** .

1. I informations fönstret för moduler till höger om arbets ytan anger du **delnings läge** till **reguljärt uttryck**.

1. Ange det **reguljära uttrycket**: `\"native-country" United-States` .

    Det **reguljära uttrycks** läget testar en enskild kolumn för ett värde. Mer information om modulen dela data finns på [referens sidan relaterad algoritm](algorithm-module-reference/split-data.md).

Din pipeline bör se ut så här:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="Skärm bild som visar hur du konfigurerar pipeline och modulen dela data":::


## <a name="save-the-datasets"></a>Spara data uppsättningarna

Nu när din pipeline har kon figurer ATS för att dela data måste du ange var du vill spara data uppsättningarna. I det här exemplet använder du modulen **Exportera data** för att spara din data uppsättning till ett data lager. Mer information om data lager finns i [Anslut till Azure Storage Services](how-to-access-data.md)

1. I paletten modul till vänster om arbets ytan, expanderar du avsnittet **data indata och utdata** och letar reda på modulen **Exportera data** .

1. Dra och släpp två moduler för **export av data** under modulen **dela data** .

1. Anslut varje utdataport för modulen **dela data** till en annan modul för **export data** .

    Din pipeline bör se ut ungefär så här:

    ![Skärm bild som visar hur du ansluter modulen exportera data](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Välj modulen **Exportera data** som är ansluten till den *vänstra* porten av modulen **dela data** .

    Ordningen för de utgående portarna som är viktiga för modulen **dela data** . Den första utgående porten innehåller raderna där det reguljära uttrycket är sant. I det här fallet innehåller den första porten rader för USA-baserad inkomst och den andra porten innehåller rader för icke-USA-baserad inkomst.

1. I informations fönstret för moduler till höger om arbets ytan anger du följande alternativ:
    
    **Data lager typ**: Azure Blob Storage

    **Data lager**: Välj ett befintligt data lager eller välj "nytt data lager" för att skapa ett nu.

    **Sökväg**: `/data/us-income`

    **Fil format**: CSV

    > [!NOTE]
    > Den här artikeln förutsätter att du har åtkomst till ett data lager som är registrerat på den aktuella Azure Machine Learning-arbetsytan. Instruktioner för hur du konfigurerar ett data lager finns i [Anslut till Azure Storage Services](how-to-connect-data-ui.md#create-datastores).

    Om du inte har ett data lager kan du skapa ett nu. I den här artikeln sparas data uppsättningarna till det standard-Blob Storage-konto som är kopplat till arbets ytan. Den sparar data uppsättningarna i `azureml` behållaren i en ny mapp med namnet `data` .

1.  Välj modulen **Exportera data** som är ansluten till den *högra* porten i modulen **dela data** .

1. I informations fönstret för moduler till höger om arbets ytan anger du följande alternativ:
    
    **Data lager typ**: Azure Blob Storage

    **Data lager**: Välj samma data lager som ovan

    **Sökväg**: `/data/non-us-income`

    **Fil format**: CSV

1. Bekräfta att **export data** -modulen är ansluten till den vänstra porten för **delnings data** har **sökvägen** `/data/us-income` .

1. Bekräfta att modulen för **export data** är ansluten till rätt port har **sökvägen** `/data/non-us-income` .

    Din pipeline och dina inställningar bör se ut så här:
    
    ![Skärm bild som visar hur du konfigurerar modulen exportera data](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Skicka körningen

Nu när din pipeline har kon figurer ATS för att dela och exportera data skickar du en pipeline-körning.

1. Välj **Skicka** på arbets ytans överkant.

1. I dialog rutan **Konfigurera pipeline-körning** väljer du **Skapa nytt** för att skapa ett experiment.

    Experiment som logiskt grupperar relaterade pipeline-körningar. Om du kör den här pipelinen i framtiden bör du använda samma experiment för loggnings-och spårnings syfte.

1. Ange ett beskrivande experiment namn som "Split-inventerings data".

1. Välj **Skicka**.

## <a name="view-results"></a>Visa resultat

När pipelinen är klar kan du Visa dina resultat genom att navigera till blob-lagringen i Azure Portal. Du kan också visa mellanliggande resultat för modulen **dela data** för att bekräfta att dina data har delats på rätt sätt.

1. Välj modulen **dela data** .

1. I informations fönstret för moduler till höger om arbets ytan väljer du **utdata + loggar**. 

1. Välj ikonen visualisera ikon ![ ](media/how-to-designer-transform-data/visualize-icon.png) bredvid **resultat DataSet1**. 

1. Kontrol lera att kolumnen "ursprungligt land" endast innehåller värdet "United-stats".

1. Välj ikonen visualisera ikon ![ ](media/how-to-designer-transform-data/visualize-icon.png) bredvid **resultat DataSet2**. 

1. Kontrol lera att kolumnen "ursprungligt land" inte innehåller värdet "United-stats".

## <a name="clean-up-resources"></a>Rensa resurser

Hoppa över det här avsnittet om du vill fortsätta med del 2 av den här så här [tränar du modeller med Azure Machine Learning designer](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du omvandlar en data uppsättning och sparar den till ett registrerat data lager.

Fortsätt till nästa del av den här instruktions serien med [omträna modeller med Azure Machine Learning designer](how-to-retrain-designer.md) för att använda dina transformerade data uppsättningar och pipeline-parametrar för att träna maskin inlärnings modeller.