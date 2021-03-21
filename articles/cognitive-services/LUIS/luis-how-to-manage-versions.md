---
title: Hantera versioner – LUIS
titleSuffix: Azure Cognitive Services
description: Med versioner kan du bygga och publicera olika modeller. En bra idé är att klona den aktuella aktiva modellen till en annan version av appen innan du gör ändringar i modellen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018828"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Använda versioner för att redigera och testa utan att påverka mellanlagrings-eller produktions program

Med versioner kan du bygga och publicera olika modeller. En bra idé är att klona den aktuella aktiva modellen till en annan [version](./luis-concept-app-iteration.md) av appen innan du gör ändringar i modellen.

Den aktiva versionen är den version som du redigerar i LUIS Portal **build** -avsnittet med avsikter, entiteter, funktioner och mönster. När du använder redigerings-API: erna behöver du inte ange den aktiva versionen eftersom de versions-/regionsspecifika REST API-anropen innehåller versionen i vägen.

Om du vill arbeta med versioner öppnar du appen genom att välja namnet på sidan **Mina appar** och väljer sedan **Hantera** i det översta fältet och sedan **versioner** i det vänstra navigerings fältet.

I listan över versioner visas vilka versioner som publiceras, var de publiceras och vilken version som för närvarande är aktiv.

## <a name="clone-a-version"></a>Klona en version

1. Välj den version som du vill klona och välj sedan **klona** i verktygsfältet.

2. I dialog rutan **klona version** anger du ett namn för den nya versionen, till exempel "0,2".

   ![Dialog rutan klona version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > Versions-ID får endast bestå av tecken, siffror eller "." och får inte vara längre än 10 tecken.

   En ny version med det angivna namnet skapas och anges som den aktiva versionen.

## <a name="set-active-version"></a>Ange aktiv version

Välj en version i listan och välj sedan **Aktivera** från verktygsfältet.

## <a name="import-version"></a>Importera version

Du kan importera en `.json` eller en `.lu` version av programmet.

1. Välj **Importera** från verktygsfältet och välj sedan formatet.

2. Ange det nya 10-siffriga versions namnet i popup-fönstret **Importera ny version** . Du behöver bara ange ett versions-ID om versionen i filen redan finns i appen.

    ![Hantera avsnitt, versioner, sidan Importera ny version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    När du har importerat en version blir den nya versionen den aktiva versionen.

### <a name="import-errors"></a>Import fel

* Tokenizer-fel: om du får ett **Tokenizer-fel** när du importerar försöker du importera en version som använder en annan [Tokenizer](luis-language-support.md#custom-tokenizer-versions) än appen som för närvarande använder. Information om hur du åtgärdar detta finns i [Migrera mellan tokenizer-versioner](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Andra åtgärder

* Om du vill **ta bort** en version väljer du en version i listan och väljer sedan **ta bort** från verktygsfältet. Välj **OK**.
* Om du vill **byta namn på** en version väljer du en version i listan och väljer sedan **Byt namn** från verktygsfältet. Ange ett nytt namn och välj sedan **OK**.
* Om du vill **Exportera** en version väljer du en version i listan och väljer sedan **Exportera app** i verktygsfältet. Välj JSON eller LU för att exportera för en säkerhets kopia eller Spara i käll kontroll väljer du **Exportera för behållare** för att [använda den här appen i en Luis-behållare](luis-container-howto.md).