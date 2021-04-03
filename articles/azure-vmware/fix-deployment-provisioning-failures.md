---
title: Stöd för distribution av Azure VMware-lösningar eller etablerings problem
description: Hämta information från ditt privata moln i Azure VMware-lösningen för att skicka en tjänstbegäran om distribution av en Azure VMware-lösning eller ett etablerings problem.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542413"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Öppna en supportbegäran för en Azure VMware-lösning distribution eller ett etablerings problem

Den här artikeln visar hur du öppnar en [supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support) och ger nyckelinformation om distribution av Azure VMware-lösningar eller etablerings fel. 

Om du har problem med ditt privata moln måste du öppna en supportbegäran i Azure Portal. Om du vill öppna en supportbegäran hämtar du först viktig information i Azure Portal:

- Korrelations-ID
- ID för Azure ExpressRoute-krets
- Felmeddelanden

## <a name="get-the-correlation-id"></a>Hämta korrelations-ID: t
 
När du skapar ett privat moln eller en resurs i Azure genereras ett korrelations-ID för resursen automatiskt för resursen. Ta med korrelations-ID: t för det privata molnet i din supportbegäran för att snabbt öppna och lösa begäran.

I Azure Portal kan du hämta korrelations-ID: t för en resurs på två sätt:

* **Översikts** fönster
* Distributions loggar
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Hämta korrelations-ID: t från resurs översikten

Här är ett exempel på åtgärds information om en misslyckad distribution av privata moln, med det angivna korrelations-ID: t:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Skärm bild som visar en misslyckad distribution av privata moln med det angivna korrelations-ID: t.":::

Åtkomst till distributions resultat i ett privat moln **översikts** fönster:

1. I Azure Portal väljer du ditt privata moln.

1. På den vänstra menyn väljer du **Översikt**.

När en distribution har initierats visas resultatet av distributionen i **översikts** fönstret för det privata molnet.

Kopiera och spara distributions korrelations-ID: t för det privata molnet som ska ingå i tjänstbegäran.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Hämta korrelations-ID: t från distributions loggen

Du kan hämta korrelations-ID: t för en misslyckad distribution genom att söka i aktivitets loggen för distribution i Azure Portal.

För att få åtkomst till distributions loggen:

1. I Azure Portal väljer du ditt privata moln och väljer sedan ikonen meddelanden.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Skärm bild som visar meddelande ikonen i Azure Portal.":::

1. I fönstret **meddelanden** väljer du **Fler händelser i aktivitets loggen**:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Skärm bild som visar fler händelser i aktivitets logg länken som marker ATS i fönstret meddelanden.":::

1. Du hittar den misslyckade distributionen och dess korrelations-ID genom att söka efter namnet på resursen eller annan information som du använde för att skapa resursen. 

    I följande exempel visas Sök Resultat för en privat moln resurs med namnet pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Skärm bild som visar Sök Resultat för ett exempel på en privat moln resurs och fönstret Skapa eller uppdatera en PrivateCloud.":::
 
1. I Sök resultaten i fönstret **aktivitets logg** väljer du åtgärds namnet för den misslyckade distributionen.

1. I fönstret **skapa eller uppdatera en PrivateCloud** väljer du fliken **JSON** och letar efter `correlationId` i loggen som visas. Kopiera `correlationId` värdet så att det ingår i support förfrågan. 
 
## <a name="copy-error-messages"></a>Kopiera fel meddelanden

Du kan lösa problemet genom att ta med eventuella fel meddelanden som visas i Azure Portal. Välj ett varnings meddelande om du vill visa en sammanfattning av fel:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Skärm bild som visar fel information på fliken Sammanfattning i fönstret fel där kopierings ikonen har valts.":::

Om du vill kopiera fel meddelandet väljer du kopierings ikonen. Spara det kopierade meddelandet som ska ingå i din support förfrågan.
 
## <a name="get-the-expressroute-id-uri"></a>Hämta ExpressRoute-ID (URI)
 
Du kanske försöker skala eller peera ett befintligt privat moln med ExpressRoute-kretsen för det privata molnet och det fungerar inte. I det scenariot behöver du ExpressRoute-ID: t som ska ingå i din support förfrågan.

Så här kopierar du ExpressRoute-ID:

1. I Azure Portal väljer du ditt privata moln.
1. På den vänstra menyn, under **Hantera**, väljer du **anslutning**. 
1. Välj fliken **ExpressRoute** i den högra rutan.
1. Välj kopierings ikonen för **ExpressRoute-ID** och spara värdet som ska användas i support förfrågan.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Kopiera ExpressRoute-ID: t till Urklipp."::: 
 
## <a name="pre-validation-failures"></a>För validerings problem

Om verifieringen av det privata molnet misslyckades (före distributionen) har ett korrelations-ID inte skapats. I det här scenariot kan du ange följande information i support förfrågan:

- Fel-och fel meddelanden. Dessa meddelanden kan vara till hjälp i många fel, till exempel för problem som rör kvoter. Det är viktigt att du kopierar dessa meddelanden och inkluderar dem i support förfrågan, enligt beskrivningen i den här artikeln.
- Information som du använde för att skapa ett privat moln i Azure VMware-lösningen, inklusive:
  - Location
  - Resursgrupp
  - Resursnamn

## <a name="create-your-support-request"></a>Skapa en support förfrågan

Allmän information om hur du skapar en supportbegäran finns i [så här skapar du en](../azure-portal/supportability/how-to-create-azure-support-request.md)support förfrågan för Azure. 

Så här skapar du en support förfrågan om distribution av Azure VMware-lösningar eller ett etablerings problem:

1. I Azure Portal väljer du **Hjälp** ikonen och väljer sedan **ny supportbegäran**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Skärm bild av fönstret Nytt Support ärende i Azure Portal.":::

1. Ange eller Välj den information som krävs:

   1. På fliken **Grunder**:

      1. För **problem typ** väljer du **konfigurations-och installations problem**.

      1. Under **typ av problem** väljer du **etablera ett privat moln**.

   1. På fliken **information** :

      1. Ange eller välj informationen som krävs.

      1. Klistra in ditt korrelations-ID eller ExpressRoute-ID där denna information begärs. Om du inte ser någon speciell text ruta för dessa värden klistrar du in dem i text rutan **ge information om problemet** .

    1. Klistra in fel information, inklusive de fel eller fel meddelanden som du kopierade i text rutan **ge information om problemet** .

1. Granska dina poster och välj sedan **skapa** för att skapa en supportbegäran.
