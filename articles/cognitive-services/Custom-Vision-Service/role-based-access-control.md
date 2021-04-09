---
title: Rollbaserad åtkomst kontroll i Azure – Custom Vision
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du konfigurerar rollbaserad åtkomst kontroll i Azure för dina Custom Vision-projekt.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616900"
---
# <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Custom Vision stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC), ett auktoriserings system för att hantera individuell åtkomst till Azure-resurser. Med Azure RBAC tilldelar du olika grupp medlemmar olika behörighets nivåer för dina Custom Vision-projekt. Mer information om Azure RBAC finns i [Azure RBAC-dokumentationen](../../role-based-access-control/index.yml).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Lägg till roll tilldelning i Custom Vision resurs

Azure RBAC kan tilldelas till en Custom Vision-resurs. Om du vill bevilja åtkomst till en Azure-resurs lägger du till en roll tilldelning.
1. I [Azure Portal](https://ms.portal.azure.com/)väljer du **alla tjänster**. 
1. Välj sedan **Cognitive Services** och navigera till din speciella Custom vision utbildnings resurs.
   > [!NOTE]
   > Du kan också konfigurera Azure RBAC för hela resurs grupper, prenumerationer eller hanterings grupper. Det gör du genom att välja önskad omfattnings nivå och sedan navigera till önskat objekt (till exempel välja **resurs grupper** och sedan klicka igenom till önskad resurs grupp).
1. Välj **åtkomst kontroll (IAM)** i det vänstra navigerings fönstret.
1. Välj fliken **roll tilldelningar** för att Visa roll tilldelningarna för det här omfånget.
1. Välj **Lägg till**  ->  **Lägg till roll tilldelning**.
1. I list rutan **roll** väljer du den roll som du vill lägga till.
1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i listan **Välj** . Om du inte ser säkerhets objekt i listan kan du skriva in rutan Välj för att söka i katalogen efter visnings namn, e-postadresser och objekt identifierare.
1. Välj **Spara** för att tilldela rollen.

Inom några minuter tilldelas målet den valda rollen i det valda omfånget.

## <a name="custom-vision-role-types"></a>Custom Vision roll typer

Använd följande tabell för att fastställa åtkomst behoven för dina Custom Vision-resurser.

|Roll  |Behörigheter  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Fullständig åtkomst till projekten, inklusive möjligheten att skapa, redigera eller ta bort ett projekt.        |
|`Cognitive Services Custom Vision Trainer`     | Fullständig åtkomst, förutom möjligheten att skapa eller ta bort ett projekt. Lärare kan visa och redigera projekt och träna, publicera, avpublicera eller exportera modeller.        |
|`Cognitive Services Custom Vision Labeler`     | Möjlighet att ladda upp, redigera eller ta bort utbildnings avbildningar och skapa, lägga till, ta bort eller ta bort taggar. Etiketter kan visa projekt, men kan inte uppdatera något annat än träna bilder och taggar.         |
|`Cognitive Services Custom Vision Deployment`     | Möjlighet att publicera, avpublicera eller exportera modeller. Distribuerare kan visa projekt, men kan inte uppdatera ett projekt, träna bilder eller taggar.        |
|`Cognitive Services Custom Vision Reader`     | Möjlighet att visa projekt. Läsarna kan inte göra några ändringar.        |