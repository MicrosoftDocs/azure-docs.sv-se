---
title: Skicka Data Factory-ursprungsdata till Azure Purview
description: Lär dig mer om att push-överföra Data Factory härkomst-data till Azure avdelningens kontroll
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361459"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Push-Data Factory härkomst data till Azure avdelningens kontroll (för hands version)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Data Factory användar gränssnitt för att skapa en pipeline som kör aktiviteter och rapportera härkomst-data till Azure avdelningens kontroll-kontot. Sedan kan du Visa all härkomst-information i ditt Azure avdelningens kontroll-konto.

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Data Factory**. Om du inte har ett Azure Data Factory, se [skapa ett Azure Data Factory](./quickstart-create-data-factory-portal.md).
* **Azure avdelningens kontroll-konto**. Avdelningens kontroll-kontot fångar alla härkomst-data som genereras av Data Factory. Om du inte har ett Azure avdelningens kontroll-konto kan du läsa [skapa en Azure-avdelningens kontroll](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Köra Data Factory-aktiviteter och skicka härkomst-data till Azure avdelningens kontroll
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Steg 1: Anslut Data Factory till ditt avdelningens kontroll-konto
Logga in på ditt avdelningens kontroll-konto i avdelningens kontroll-portalen och gå till **Management Center**. Välj **Data Factory** i **externa anslutningar** och klicka på knappen **nytt** för att skapa en anslutning till en ny Data Factory. 
[![Skärm bild för att skapa en anslutning mellan Data Factory-och avdelningens kontroll-kontot ](./media/data-factory-purview/connect-adf-to-purview.png)](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

På popup-sidan kan du välja den Data Factory som du vill ansluta till det här avdelningens kontroll-kontot. 
![Skärm bild för en ny anslutning](./media/data-factory-purview/new-adf-purview-connection.png)

Du kan kontrol lera statusen när du har skapat anslutningen. **Connected** innebär att anslutningen mellan Data Factory och att avdelningens kontroll har anslutits. 
> [!NOTE]
> Du måste tilldelas någon av följande roller i avdelningens kontroll-kontot och rollen Data Factory **Contributor** för att skapa anslutningen mellan Data Factory och Azure avdelningens kontroll.
> - Ägare
> - Administratör för användaråtkomst

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Steg 2: kör kopierings-och data flödes aktiviteter i Data Factory
Du kan skapa pipelines, kopiera aktiviteter och data flödes aktiviteter i Data Factory. Du behöver ingen ytterligare konfiguration för härkomst Data Capture. Härkomst-data kommer automatiskt att samlas in under aktivitets körningen.
![Skärm bild av aktiviteten Kopiera och data flöde ](./media/data-factory-purview/adf-activities-for-lineage.png) om du inte vet hur du ska skapa kopierings-och data flödes aktiviteter, se [Kopiera data från Azure Blob Storage till en databas i Azure SQL Database genom att använda Azure Data Factory](./tutorial-copy-data-portal.md) och [transformera data med hjälp av mappnings data flöden](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Steg 3: kör kör SSIS-paket aktiviteter i Data Factory
Du kan skapa pipeliner, köra SSIS-paket aktiviteter i Data Factory. Du behöver ingen ytterligare konfiguration för härkomst Data Capture. Härkomst-data kommer automatiskt att samlas in under aktivitets körningen.
![Skärm bild av aktiviteten kör SSIS-paket](./media/data-factory-purview/ssis-activities-for-lineage.png)

Om du inte vet hur du skapar kör SSIS-paket-aktiviteter, se [kör SSIS-paket i Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Steg 4: Visa information om härkomst i ditt avdelningens kontroll-konto
Gå tillbaka till ditt avdelningens kontroll-konto. På sidan start väljer du **Bläddra till gångar**. Välj den resurs som du vill använda och klicka på fliken härkomst. Du kommer att se all härkomst-information.
[![Skärm bild av avdelningens kontroll-konto ](./media/data-factory-purview/view-dataset.png)](./media/data-factory-purview/view-dataset.png#lightbox)

Du kan se härkomst-data för kopierings aktivitet.
[![Skärm bild av kopiera härkomst ](./media/data-factory-purview/copy-lineage.png)](./media/data-factory-purview/copy-lineage.png#lightbox)

Du kan också se härkomst data för data Ströms aktivitet.
[![Skärm bild av data Ströms härkomst ](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> För härkomst av data flödes aktivitet stöder vi bara källa och mottagare. Transformeringen härkomst for dataström stöds inte ännu.

Du kan också se härkomst-data för aktiviteten kör SSIS-paket.
[![Skärm bild av SSIS-härkomst ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> För härkomst av aktiviteten kör SSIS-paket stöder vi bara källa och mål. Härkomst för omvandling stöds inte ännu.

## <a name="next-steps"></a>Nästa steg
[Användar guide för katalog härkomst](../purview/catalog-lineage-user-guide.md)

[Ansluta Data Factory till Azure-avdelningens kontroll](connect-data-factory-to-azure-purview.md)