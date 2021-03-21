---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning-förbruka slut punkter för pipelines'
description: Integrera pipelines slut punkter med klient program i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565501"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Använda slut punkter för pipelines från klient program

I den här artikeln får du lära dig hur du integrerar klient program med Azure Machine Learning slut punkter. Mer information om hur du skriver program kod finns i [använda en Azure Machine Learning slut punkt](../how-to-consume-web-service.md).

Den här artikeln är en del av Studio (klassisk) för att Azure Machine Learning migrations serien. Mer information om hur du migrerar till Azure Machine Learning finns i [översikts artikeln om migrering](migrate-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Azure Machine Learning-arbetsyta. [Skapa en Azure Machine Learning-arbetsyta](../how-to-manage-workspace.md#create-a-workspace).
- En [Azure Machine Learning real tids slut punkt eller pipeline-slutpunkt](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Använda en slut punkt i real tid 

Om du har distribuerat din modell som en **slut punkt för Real tid** kan du hitta dess REST-slutpunkt och förgenererad förbruknings kod i C#, python och R:

1. Gå till Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Gå till fliken **slut punkter** .
1. Välj din real tids slut punkt.
1. Välj **förbruka**.

> [!NOTE]
> Du kan också hitta Swagger-specifikationen för din slut punkt på fliken **information** . Använd Swagger-definitionen för att förstå ditt slut punkts schema. Mer information om Swagger-definition finns i den [officiella dokumentationen för Swagger](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Använda en pipeline-slutpunkt

Det finns två sätt att använda en pipeline-slut punkt:

- REST API-anrop
- Integrering med Azure Data Factory

### <a name="use-rest-api-calls"></a>Använd REST API-anrop

Anropa REST-slutpunkten från klient programmet. Du kan använda Swagger-specifikationen för din slut punkt för att förstå dess schema:

1. Gå till Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Gå till fliken **slut punkter** .
1. Välj **pipeline-slutpunkter**.
1. Välj din pipeline-slutpunkt.
1. I **översikts fönstret för pipelinens slut punkt** väljer du länken under **rest Endpoint documentation**.

### <a name="use-azure-data-factory"></a>Använda Azure Data Factory

Du kan anropa Azure Machine Learning pipelinen som ett steg i en Azure Data Factory pipeline. Mer information finns i [köra Azure Machine Learning pipelines i Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hittar schema och exempel kod för dina pipelines slut punkter. Mer information om hur du förbrukar slut punkter från klient programmet finns i [använda en Azure Machine Learning slut punkt](../how-to-consume-web-service.md).

Se resten av artiklarna i Azure Machine Learning migration-serien: 
1. [Översikt över migrering](migrate-overview.md).
1. [Migrera data uppsättning](migrate-register-dataset.md).
1. [Återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).
1. [Återskapa en Studio-webbtjänst (klassisk)](migrate-rebuild-web-service.md).
1. **Integrera en Azure Machine Learning-webbtjänst med klient program**.
1. [Migrera kör R-skript](migrate-execute-r-script.md).