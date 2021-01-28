---
title: 'Självstudie: förutsättningar för Cognitive Services i Azure Synapse Analytics'
description: Lär dig hur du konfigurerar förutsättningarna för att använda Cognitive Services i Azure-Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936679"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Självstudie: krav för att använda Cognitive Services i Azure Synapse Analytics

I den här självstudien får du lära dig hur du konfigurerar kraven för att säkert använda Azure Cognitive Services i Azure Synapse Analytics.

Den här självstudiekursen omfattar:
> [!div class="checklist"]
> - Skapa en Cognitive Services resurs som Textanalys eller avvikelse detektor.
> - Lagra en autentiseringsnyckel för att Cognitive Services resurser som hemligheter i Azure Key Vault och konfigurera åtkomst för en Azure Synapse Analytics-arbetsyta.
> - Skapa en Azure Key Vault länkad tjänst i din Azure Synapse Analytics-arbetsyta.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara *data deltagare i Storage BLOB* för det Azure Data Lake Storage Gen2 fil system som du arbetar med.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) innehåller många typer av tjänster. Textanalys och avvikelse detektor är två exempel i självstudierna för Azure-Synapse.

Du kan skapa en [textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) resurs i Azure Portal:

![Skärm bild som visar Textanalys i portalen med knappen Skapa.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Du kan skapa en resurs för [avvikelse detektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Azure Portal:

![Skärm bild som visar avvikelse detektor i portalen med knappen Skapa.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Skapa ett nyckel valv och konfigurera hemligheter och åtkomst

1. Skapa ett [nyckel valv](https://ms.portal.azure.com/#create/Microsoft.KeyVault) i Azure Portal.
2. Gå till **Key Vault**  >  **åtkomst principer** och ge [Azure Synapse-arbetsyte-MSI](../security/synapse-workspace-managed-identity.md) -behörighet att läsa hemligheter från Azure Key Vault.

   > [!NOTE]
   > Kontrol lera att princip ändringarna har sparats. Det här steget är enkelt att sakna.

   ![Skärm bild som visar markeringar för att lägga till en åtkomst princip.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Gå till din Cognitive Services-resurs. Gå till exempel till **avvikelse**  >  **nycklar och slut punkt**. Kopiera sedan någon av de två nycklarna till Urklipp.

4. Gå till **Key Vault**  >  **hemlighet** för att skapa en ny hemlighet. Ange namnet på hemligheten och klistra sedan in nyckeln från föregående steg i fältet **värde** . Välj slutligen **skapa**.

   ![Skärm bild som visar val för att skapa en hemlighet.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Se till att du kommer ihåg eller anteckna detta hemliga namn. Du kommer att använda den senare när du ansluter till Cognitive Services från Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Skapa en Azure Key Vault länkad tjänst i Azure Synapse

1. Öppna din arbets yta i Azure Synapse Studio. 
2. Gå till **Hantera**  >  **länkade tjänster**. Skapa en **Azure Key Vault** länkad tjänst genom att peka på det nyckel valv som du nyss skapade. 
3. Kontrol lera anslutningen genom att välja knappen **Testa anslutning** . Om anslutningen är grön väljer du **skapa** och väljer sedan **publicera allt** för att spara ändringen.

![Skärm bild som visar Azure Key Vault som en ny länkad tjänst.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Nu är du redo att fortsätta med en av självstudierna för att använda Azure Cognitive Services-upplevelsen i Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: sentiment-analys med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: avvikelse identifiering med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse-dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md).
- [Machine Learning funktioner i Azure Synapse Analytics](what-is-machine-learning.md)