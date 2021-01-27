---
title: 'Snabbstart: Skapa, distribuera och använda en anpassad modell – Custom Translator'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kan du gå igenom processen för att skapa ett översättningssystem med hjälp av Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: quickstart
ms.openlocfilehash: ae417006903c8d2478126f928f05647361bad1b9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896214"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Snabbstart: Skapa, distribuera och använda en anpassad modell för översättning

Den här artikeln innehåller stegvisa instruktioner för att skapa ett översättningssystem med Custom Translator.

## <a name="prerequisites"></a>Förutsättningar

1. För att använda [Custom Translator](https://portal.customtranslator.azure.ai)-portalen behöver du ett [Microsoft-konto](https://signup.live.com) eller [Azure AD-konto](../../../active-directory/fundamentals/active-directory-whatis.md) (organisationskonto på Azure) för att logga in.

2. En prenumeration på Translator Text API via Azure-portalen. Du behöver prenumerationsnyckeln för Translator Text API för att associera med din arbetsyta i Custom Translator. Se [hur du registrerar dig för Translator Text API](../translator-how-to-signup.md).

3. När du har båda ovanstående loggar du in på den  [anpassade översättnings](https://portal.customtranslator.azure.ai) portalen för att skapa arbets ytor, projekt, ladda upp filer och skapa/distribuera modeller.

>[!Note]
>Anpassad översättare har inte stöd för att skapa arbets yta för Translator Text API resurs som har skapats i det [aktiverade VNet](../../../api-management/api-management-using-with-vnet.md).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du första gången är användare uppmanas du att godkänna villkoren för tjänsten för att skapa en arbets yta och koppla din arbets yta till Microsoft Translator Text API-prenumerationen.

![Skapa arbets yta skapa arbets yta ](media/quickstart/terms-of-service.png)
 ![ avbildning 1 ](media/quickstart/create-workspace-1.png)
 ![ skapa arbets yta bild 2 ](media/quickstart/create-workspace-2.png)
 ![ skapa arbets yta bild 3 ](media/quickstart/create-workspace-3.png)
 ![ skapa arbets yta bild 4 ](media/quickstart/create-workspace-4.png)
 ![ skapa arbets yta bild 5 ](media/quickstart/create-workspace-5.png)
 ![ skapa arbets yta avbildning 6](media/quickstart/create-workspace-6.png)

Vid efterföljande besök på den anpassade översättnings portalen navigerar du till sidan inställningar där du kan hantera din arbets yta, skapa fler arbets ytor, associera din prenumerations nyckel för Microsoft Translator Text API med dina arbets ytor, lägga till medägare och ändra en prenumerations nyckel.

## <a name="create-a-project"></a>Skapa ett projekt

Klicka på Nytt projekt Custom Translator-portalens landningssida. I dialogrutan kan du ange önskat projektnamn, språkpar och kategori samt övriga relevanta fält. Spara sedan ditt projekt. Mer information finns i [Skapa projekt](how-to-create-project.md).

![Skapa projekt](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Ladda upp dokument

Ladda sedan upp dokumentuppsättningarna för [träning](training-and-model.md#training-document-type-for-custom-translator), [justering](training-and-model.md#tuning-document-type-for-custom-translator) och [testning](training-and-model.md#testing-dataset-for-custom-translator). Du kan ladda upp både [parallella](what-are-parallel-documents.md) dokument och kombinationsdokument. Du kan också ladda upp en [ordlista](what-is-dictionary.md).

Du kan ladda upp dokument från dokumentfliken eller från en specifik projektsida.

![Ladda upp dokument](media/quickstart/ct-how-to-upload.png)

När du laddar upp dokument väljer du dokumenttyp (träning, justering eller testning) och språkpar. När du överför parallella dokument måste du också ange ett dokumentnamn. Mer information finns i [Ladda upp dokument](how-to-upload-document.md).

## <a name="create-a-model"></a>Skapa en modell

När alla nödvändiga dokument laddas upp är nästa steg att skapa din modell.

Välj det projekt som du har skapat. Alla dokument du har laddat upp som delar ett språkpar med det här projektet visas. Välj de dokument som du vill inkludera i din modell. Du kan välja [utbildning](training-and-model.md#training-document-type-for-custom-translator), [träning](training-and-model.md#tuning-document-type-for-custom-translator), och [testdata](training-and-model.md#testing-dataset-for-custom-translator) eller välja enbart justeringsdata och låta Custom Translator automatiskt skapa justerings- och testuppsättningar för din modell.

![Skapa en modell](media/quickstart/ct-how-to-train.png)

När du har valt önskade dokument klickar du på knappen Skapa modell för att skapa din modell och starta träningen. Du kan se statusen för din träning och information för alla modeller som du har tränat på fliken Modeller.

Mer information finns i [Skapa en modell](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analysera din modell

När träningen har slutförts kan du granska resultaten. BLEU-poängen är ett mått som anger kvaliteten på din översättning. Du kan också manuellt jämföra översättningar som gjorts med din anpassade modell med översättningarna som tillhandahålls i testuppsättningen genom att gå till fliken ”Test” och klicka på ”Systemresultat”. När du kontrollerar några av översättningarna manuellt får du en uppfattning om kvaliteten på översättningen som genereras av systemet. Läs mer i informationen om [testresultat för system](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Distribuera en tränad modell

När du är redo att distribuera din tränade modell klickar du på knappen ”Distribuera”. Du kan ha en distribuerad modell per projekt och du kan visa statusen för distributionen i kolumnen Status. Mer information finns i [Modelldistribution](how-to-view-system-test-results.md#deploy-a-model)

![Distribuera en tränad modell](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>Växla distribuerad modell

Om du vill byta ut en distribuerad modell med en annan i ett projekt klickar du på knappen "Växla" som visas bredvid önskad modell. Under växlings processen kommer den distribuerade modellen fortsätta att vara tillgänglig för att betjäna översättnings begär Anden. 

![Växla distribuerad modell](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>Använda en distribuerad modell

Du kan nå distribuerade modeller via Microsoft Translator [Text API V3 genom att ange CategoryID](../reference/v3-0-translate.md?tabs=curl). Mer information om Translator Text API kan hittas på webbsidan [API-referens](../reference/v3-0-reference.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du navigerar i [Custom Translator-arbetsytan och hanterar dina projekt](workspace-and-project.md).