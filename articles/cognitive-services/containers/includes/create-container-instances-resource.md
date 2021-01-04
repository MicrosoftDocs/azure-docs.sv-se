---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Container instance-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 874d44e3900b1edf24c2f6bee95d787c5fc3988d
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704854"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Skapa en Azure Container instance-resurs med hjälp av Azure Portal

1. Gå till sidan [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för container instances.

2. På fliken **grundläggande** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Välj en tillgänglig resurs grupp eller skapa en ny, till exempel `cognitive-services` .|
    |Containerns namn|Ange ett namn som `cognitive-container-instance` . Namnet måste innehålla nedre CAPS.|
    |Plats|Välj en region för distribution.|
    |Avbildningstyp|Om behållar avbildningen lagras i ett behållar register som inte kräver autentiseringsuppgifter väljer du `Public` . Om åtkomsten till behållar avbildningen kräver autentiseringsuppgifter väljer du `Private` . Se [behållar databaser och avbildningar](../../cognitive-services-container-support.md#container-repositories-and-images) för information om huruvida behållar avbildningen är `Public` eller `Private` ("offentlig för hands version). |
    |Avbildningens namn|Ange plats för Cognitive Services behållare. Platsen är det som används som argument till `docker pull` kommandot. Referera till [behållar databaserna och avbildningarna](../../cognitive-services-container-support.md#container-repositories-and-images) för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats.<br><br>Avbildningens namn måste vara fullständigt kvalificerade och ange tre delar. Först, behållar registret, sedan databasen, sist avbildningens namn: `<container-registry>/<repository>/<image-name>` .<br><br>Här är ett exempel som `mcr.microsoft.com/azure-cognitive-services/keyphrase` representerar extrahering av diskussionsämne avbildningen i Microsoft container Registry under Azure Cognitive Services-lagringsplatsen. Ett annat exempel är, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` som representerar tal till text bild i Microsoft-lagringsplatsen för för hands versionen av container-registret. |
    |OS-typ|`Linux`|
    |Storlek|Ändra storlek till föreslagna rekommendationer för din speciella kognitiva tjänst behållare:<br>2 CPU-kärnor<br>4 GB

3. På fliken **nätverk** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Portar|Ange TCP-port till `5000` . Exponerar behållaren på port 5000.|

4. På fliken **Avancerat** anger du de **miljövariabler** som krävs för behållarens fakturerings inställningar för Azure Container instance-resursen:

    | Tangent | Värde |
    |--|--|
    |`ApiKey`|Kopierat från sidan **nycklar och slut punkt** för resursen. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`Billing`| Slut punkts-URL: en kopierad från sidan **nycklar och slut punkt** för resursen.|
    |`Eula`|`accept`|

5. Klicka på **Granska och skapa**
6. När verifieringen är klar klickar du på **skapa** för att slutföra processen som skapas
7. När resursen har distribuerats är den klar
