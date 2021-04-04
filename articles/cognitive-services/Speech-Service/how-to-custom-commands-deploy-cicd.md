---
title: Kontinuerlig distribution med Azure DevOps (för hands version)
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du ställer in kontinuerlig distribution för program med anpassade kommandon. Du skapar skripten för att stödja arbets flöden för kontinuerlig distribution.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927593"
---
# <a name="continuous-deployment-with-azure-devops"></a>Kontinuerlig distribution med Azure DevOps

I den här artikeln får du lära dig hur du ställer in kontinuerlig distribution för program med anpassade kommandon. Skripten som stöder CI/CD-arbetsflödet tillhandahålls till dig.

## <a name="prerequisite"></a>Förutsättning
> [!div class = "checklist"]
> * Ett anpassat kommando program för utveckling (DEV)
> * Ett anpassat kommando program för produktion (PROD)
> * Registrera dig för [Azure-pipeliner](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exportera/importera/publicera

Skripten finns i [Cognitive Services röst assistenten-anpassade kommandon](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Klona skripten i bash-katalogen till din lagrings plats. Se till att du behåller samma sökväg.

### <a name="set-up-a-pipeline"></a>Konfigurera en pipeline 

1. Gå till **Azure DevOps – pipelines** och klicka på "ny pipeline"
1. I avsnittet **Anslut** väljer du platsen för din lagrings plats där dessa skript finns
1. I avsnittet **Välj** väljer du din lagrings plats
1. I avsnittet **Konfigurera** väljer du Start pipeline
1. Härnäst får du en redigerare med en YAML-fil, ersätter avsnittet "steg" med det här skriptet.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Observera att dessa skript förutsätter att du använder regionen `westus2` , om det inte är fallet uppdatera argumenten för aktiviteterna i enlighet med detta

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar värdet för regionen i argumenten.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. Öppna List rutan i knappen "Spara och köra" och klicka på "Spara"

### <a name="hook-up-the-pipeline-with-your-application"></a>Koppla samman pipelinen med ditt program

1. Gå till huvud sidan för pipelinen.
1. I list rutan i det övre högra hörnet väljer du **Redigera pipeline**. Du får en YAML-redigerare. 
1. I det övre högra hörnet bredvid knappen Kör väljer du **variabler**. Klicka på **ny variabel**.
1. Lägg till följande variabler:
    
    | Variabel | Beskrivning |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID för DEV-programmet |
    | TargetAppId | ID för produktions programmet |
    | SubscriptionKey | Prenumerations nyckel som används för båda programmen |
    | Kultur | Program kultur (t. ex. en-US) |

    > [!div class="mx-imgBorder"]
    > ![Skicka aktivitetens nytto Last](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Klicka på "kör" och klicka sedan på "jobb" som körs. 

    Du bör se en lista över aktiviteter som kör som innehåller: "Exportera käll program", "importera till mål program" & "träna och publicera mål program"

## <a name="deploy-from-source-code"></a>Distribuera från käll kod

Om du vill behålla definitionen av ditt program på en lagrings plats, tillhandahåller vi skript för distributioner från käll koden. Eftersom skripten är i bash måste du installera [Linux-undersystemet](/windows/wsl/install-win10)om du använder Windows.

Skripten finns i [Cognitive Services röst assistenten-anpassade kommandon](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Klona skripten i bash-katalogen till din lagrings plats. Se till att du behåller samma sökväg.

### <a name="prepare-your-repository"></a>Förbered din lagrings plats

1. Skapa en katalog för ditt program. i vårt exempel skapar du en som kallas "appar".
1. Uppdatera argumenten för bash-skriptet nedan och kör. Appens dialog modell importeras till filen myapp.jspå
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argument | Beskrivning |
    | ------- | --------------- | ----------- |
    | region | regionen för programmet, t. ex. westus2. |
    | subscriptionkey | prenumerations nyckel för din tal resurs. |
    | undanta | de anpassade kommandona program-ID som du vill exportera. |

1. Skicka ändringarna till din lagrings plats.

### <a name="set-up-a-pipeline"></a>Konfigurera en pipeline 

1. Gå till **Azure DevOps – pipelines** och klicka på "ny pipeline"
1. I avsnittet **Anslut** väljer du platsen för din lagrings plats där dessa skript finns
1. I avsnittet **Välj** väljer du din lagrings plats
1. I avsnittet **Konfigurera** väljer du Start pipeline
1. Härnäst får du en redigerare med en YAML-fil, ersätter avsnittet "steg" med det här skriptet.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Dessa skript förutsätter att du använder regionen westus2, om det inte är fallet uppdatera argumenten för uppgifterna i enlighet med detta

1. Öppna List rutan i knappen "Spara och köra" och klicka på "Spara"

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Koppla samman pipelinen med mål programmen

1. Gå till huvud sidan för pipelinen.
1. I list rutan i det övre högra hörnet väljer du **Redigera pipeline**. Du får en YAML-redigerare. 
1. I det övre högra hörnet bredvid knappen Kör väljer du **variabler**. Klicka på **ny variabel**.
1. Lägg till följande variabler:

    | Variabel | Beskrivning |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID för produktions programmet |
    | SubscriptionKey | Prenumerations nyckel som används för båda programmen |
    | Kultur | Program kultur (t. ex. en-US) |

1. Klicka på "kör" och klicka sedan på "jobb" som körs.
    Du bör se en lista över aktiviteter som kör som innehåller: "Importera app" & "träna och publicera app"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)
