---
title: 'Självstudie: krav för Cognitive Services i Azure Synapse'
description: Själv studie kurs om hur du konfigurerar krav för att använda Cognitive Services i Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222181"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Självstudie: krav för att använda Cognitive Services i Azure Synapse

I den här självstudien får du lära dig hur du konfigurerar för hands kraven för att på ett säkert sätt utnyttja Cognitive Services i Azure Synapse.

Den här självstudiekursen omfattar:
> [!div class="checklist"]
> - Skapa en Cognitive Services-resurs. Till exempel Textanalys eller avvikelse detektor.
> - Lagra autentiseringsnyckel för att Cognitive Services resurser som hemligheter i Azure Key Vault och konfigurera åtkomst för Azure dataSynapses-arbetsyta.
> - Skapa en länkad Azure Key Vault-tjänst i din Azure Synapse Analytics-arbetsyta.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara **data deltagare i Storage BLOB** för det ADLS Gen2-filsystem som du arbetar med.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Skapa en Cognitive Services resurs

[Azure Cognitive Services](../../cognitive-services/index.yml) innehåller många olika typer av tjänster. Nedan visas några exempel som används i Synapse-självstudierna.

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor
Skapa en [avvikelse detektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Azure Portal.

![Skapa avvikelse detektor](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Skapa en Textanalys resurs
Skapa en [textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) resurs i Azure Portal.

![Skapa text analys](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Skapa Key Vault och konfigurera hemligheter och åtkomst

1. Skapa en [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) i Azure Portal.
2. Gå till **Key Vault-> åtkomst principer** och ge [Azure Synapse-arbetsyte-MSI](../security/synapse-workspace-managed-identity.md) -behörighet att läsa hemligheter från Azure Key Vault.

>Kontrol lera att princip ändringarna har sparats. Det här steget är enkelt att sakna.

![Lägg till åtkomst princip](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Gå till din kognitiva tjänst resurs, till exempel **avvikelse detektor-> nycklar och slut punkt**, kopiera någon av de två nycklarna till Urklipp.

4. Gå till **Key Vault-> hemlighet** för att skapa en ny hemlighet. Ange namnet på hemligheten och klistra sedan in nyckeln från föregående steg i fältet "värde". Klicka på **Skapa**.

![Skapa hemlighet](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Se till att du kommer ihåg eller anteckna det här hemliga namnet! Du kommer att använda den senare när du ansluter till Cognitive Services från Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Skapa en länkad Azure Vault-tjänst i Azure Synapse

1. Öppna din arbets yta i Azure Synapse Studio. Navigera till **Hantera-> länkade tjänster**. Skapa den länkade tjänsten Azure Key Vault AB som pekar på den Key Vault vi nyss skapade. Kontrol lera sedan anslutningen genom att klicka på knappen "Testa anslutning" och kontrol lera om den är grön. Om något fungerar bra klickar du på "skapa" först och sedan på publicera alla för att spara ändringen.
![Länkad tjänst](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Du är nu redo att fortsätta med en av självstudierna för att använda Azure Cognitive Services-upplevelsen i Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: sentiment-analys med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: avvikelse identifiering med Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Självstudie: Machine Learning-modellens poängsättning i Azure Synapse-dedikerade SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md).
- [Machine Learning funktioner i Azure Synapse Analytics](what-is-machine-learning.md)