---
title: Så här begär du en kvot ökning för Azure Cosmos DB resurser
description: Lär dig hur du begär en kvot ökning för Azure Cosmos DB resurser. Du får också lära dig hur du aktiverar en prenumeration för åtkomst till en region.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090043"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Så här begär du en kvot ökning för Azure Cosmos DB resurser
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Resurserna i Azure Cosmos DB har [standard kvoter/gränser](concepts-limits.md). Det kan dock finnas ett fall där arbets belastningen kräver mer kvot än standardvärdet. I sådana fall måste du kontakta Azure Cosmos DBs teamet för att begära en kvot ökning. I den här artikeln beskrivs hur du begär en kvot ökning för Azure Cosmos DB resurser. Du får också lära dig hur du aktiverar en prenumeration för åtkomst till en region.

## <a name="create-a-new-support-request"></a>Skapa en ny supportbegäran

Om du vill begära en kvot ökning måste du skapa en ny support förfrågan med din arbets belastnings information. Azure Cosmos DBs teamet utvärderar sedan din begäran och godkänner det. Använd följande steg för att skapa en ny supportbegäran från Azure Portal:

1. Logga in i Azure-portalen.

1. Välj **Hjälp + Support** på menyn till vänster och välj sedan **ny supportbegäran**.

1. På fliken **grundläggande** fyller du i följande information:

   * För **Problemtyp** väljer du **Tjänst- och prenumerationsgränser (kvoter)**
   * För **prenumeration** väljer du den prenumeration som du vill öka kvoten för.
   * För **typ av kvot** väljer du **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Skapa en ny Cosmos DB support förfrågan om kvot ökning":::

1. På fliken **information** anger du den information som motsvarar din kvot förfrågan. Informationen på den här fliken kommer att användas för att utvärdera problemet och hjälpa Support teknikern att felsöka problemet.

1. Fyll i följande information i det här formuläret:

   * **Beskrivning**: Ange en kort beskrivning av din begäran, till exempel din arbets belastning, varför standardvärdena inte räcker. Baserat på den resurs typ som du vill ha en kvot ökning för, är det obligatoriskt att ange följande information i fältet **Beskrivning** :

     **Regioner-begäranden** Om din begäran motsvarar att lägga till en region i listan över tillåtna, måste du ange följande värden:

        * Region namn
        * Prenumerations-ID:t

     **Begär Anden om data flödes gräns** Om din begäran motsvarar att öka kvoten för data flödet, se till att ange följande värden:

        * Databasnamn
        * Prenumerations-ID:t
        * Ny data flödes gräns

     **Databas konto gräns begär Anden** Om din begäran motsvarar att öka kvoten för antalet databas konton i en prenumeration ska du se till att ange följande värden:

       * Prenumerations-ID:t
       * Ny databas konto gräns

   * **Fil uppladdning**: Ladda upp diagnostikfiler eller andra filer som du tror är relevanta för support förfrågan. Mer information om fil uppladdnings vägledning finns i Support artikeln för [Azure]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) .

   * **Allvarlighets grad**: Välj en av de tillgängliga allvarlighets graderna utifrån påverkan på verksamheten.

   * **Önskad kontakt metod**: du kan antingen välja att kontaktas via **e-post** eller via **telefon**.

1. Fyll i återstående information, till exempel din tillgänglighet, support språk, kontakt information, e-postadress och telefonnummer i formuläret.

1. Välj **Nästa: granska + skapa**. Verifiera den information som anges och välj **skapa** för att skapa en support förfrågan.

Inom 24 timmar utvärderar Azure Cosmos DB support teamet din begäran och kommer tillbaka till dig.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Cosmos DB standard tjänst kvoter](concepts-limits.md)
