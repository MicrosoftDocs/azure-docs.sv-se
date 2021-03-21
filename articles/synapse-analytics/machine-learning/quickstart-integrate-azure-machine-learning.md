---
title: 'Snabb start: länka en Azure Machine Learning arbets yta'
description: Länka din Synapse-arbetsyta till en Azure Machine Learning-arbetsyta
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 800cbf9b7a7fa415452f29b253347188c8917c52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219461"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Snabb start: skapa en ny Azure Machine Learning länkad tjänst i Synapse

I den här snabb starten ska du länka en Azure Synapse Analytics-arbetsyta till en Azure Machine Learning-arbetsyta. Genom att länka dessa arbets ytor kan du utnyttja Azure Machine Learning från olika upplevelser i Synapse.

Genom att länka till en Azure Machine Learning arbets yta kan du till exempel använda dessa upplevelser:

- Kör Azure Machine Learning pipelines som ett steg i dina Synapse-pipeliner. Läs mer i [köra Azure Machine Learning pipelines](../../data-factory/transform-data-machine-learning-service.md).

- Utöka dina data med förutsägelser genom att ta en maskin inlärnings modell från Azure Machine Learning modell registret och räkna ut modellen i Synapse SQL-pooler. Mer information finns i [Självstudier: bedömnings guiden för Machine Learning-modeller för SYNAPSE SQL-pooler](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
- [Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett ADLS Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara **data deltagare i Storage BLOB** för det ADLS Gen2-filsystem som du arbetar med.
- [Azure Machine Learning-arbetsyta](../../machine-learning/how-to-manage-workspace.md).
- Du behöver behörigheter (eller begäran från någon som har behörighet) för att skapa ett huvud namn för tjänsten och en hemlighet som du kan använda för att skapa den länkade tjänsten. Observera att det här tjänstens huvud namn måste tilldelas deltagar rollen i Azure Machine Learning-arbetsyta.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

I det här steget skapas ett nytt huvud namn för tjänsten. Om du vill använda ett befintligt huvud namn för tjänsten kan du hoppa över det här steget.
1. Öppna Azure-portalen. 

1. Gå till **Azure Active Directory**  ->  **Appregistreringar**.

1. Klicka **Ny registrering**. Följ sedan instruktionerna i användar gränssnittet för att registrera ett nytt program.

1. När programmet har registrerats. Skapa en hemlighet för programmet. Gå till **ditt program**  ->  **certifikat & hemlighet**. Klicka på **Lägg till klient hemlighet** för att generera en hemlighet. Behåll den hemliga säkerheten och kommer att användas senare.

   ![Generera hemlighet](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Skapa ett huvud namn för tjänsten för programmet. Gå till **program**  ->  **översikten** och klicka sedan på **skapa tjänstens huvud namn**. I vissa fall skapas automatiskt tjänstens huvud namn.

   ![Skapa tjänstens huvudnamn](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Lägg till tjänstens huvud namn som "deltagare" i arbets ytan Azure Machine Learning. Observera att detta kräver att det är en ägare till resurs gruppen som Azure Machine Learning arbets ytan tillhör.

   ![Tilldela deltagar roll](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

1. På arbets ytan Synapse där du vill skapa den nya Azure Machine Learning länkade tjänsten går du till **hanterad**  ->  **länkad** tjänst, skapar en ny länkad tjänst med typen "Azure Machine Learning".

   ![Skapa länkad tjänst](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Fyll i formuläret:

   - Tjänstens huvud namns-ID: Detta är **programmets klient-ID** .
  
     > [!NOTE]
     > Detta är inte namnet på programmet. Du hittar detta ID på översikts sidan för programmet. Det bör vara en lång sträng som liknar denna "81707eac-ab38-406u-8f6c-10ce76a568d5".

   - Tjänstens huvud nyckel: hemligheten som du skapade i föregående avsnitt.

3. Kontrol lera att konfigurationen är korrekt genom att klicka på **Testa anslutning** . Om anslutnings testet lyckas klickar du på **Spara**.

   Om anslutnings testet misslyckades ser du till att tjänstens huvud namns-ID och hemlighet är korrekta och försöker igen.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: bedömnings guide för Machine Learning-modell – dedikerad SQL-pool](tutorial-sql-pool-model-scoring-wizard.md)
- [Maskininlärningsfunktioner i Azure Synapse Analytics](what-is-machine-learning.md)