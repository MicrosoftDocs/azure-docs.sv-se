---
title: Anslut till Azure Data Share
description: I den här artikeln beskrivs hur du ansluter ett Azure Data resurs konto med Azure avdelningens kontroll för att söka efter till gångar och spåra data härkomst.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555363"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Så här ansluter du Azure Data Share och Azure avdelningens kontroll

Den här artikeln beskriver hur du ansluter ditt [Azure Data resurs](../data-share/overview.md) konto med Azure avdelningens kontroll och styr de delade data uppsättningarna (både utgående och inkommande) i din datafastighet. Olika data styrnings personer kan upptäcka och spåra härkomst av data mellan gränser som organisationer, avdelningar och till och med data Center.

## <a name="common-scenarios"></a>Vanliga scenarier

Data Share härkomst syftar till att ge detaljerad information om rotor Saks analys och effekt analys.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Scenario 1:360 vy över data uppsättningar som delas in/ut för en partner organisation eller intern avdelning

Data ansvariga kan se en lista över alla data uppsättningar som är dubbelriktade delade med sina partner organisationer. De kan söka efter och identifiera data uppsättningar efter organisations namn och se en fullständig vy över alla utgående och inkommande resurser.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Scenario 2: rotor Saks analys-beroende av data uppsättningar som kommer till organisationen (konsument visning av inkommande resurser)

En rapport har felaktig information på grund av överordnade data problem från ett externt data resurs konto. Data tekniker kan förstå överordnade fel, vara informerade om orsakerna och ytterligare kontakta resursens ägare för att åtgärda de problem som orsakar data avvikelsen.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Scenario 3: effekt analys på data uppsättningar som går utanför organisationen (leverantörs visning av utgående resurser)

Data producenter vill veta vilka som kommer att påverkas när de gör en ändring i deras data uppsättning. Med härkomst kan en data tillverkare enkelt förstå effekten av de ingångna interna eller externa partners som använder data med Azure Data Share.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share och avdelningens kontroll-ansluten upplevelse

Gör så här för att ansluta din Azure Data-resurs och Azure avdelningens kontroll-konto:

1. Skapa ett avdelningens kontroll-konto. All information om data delar härkomst samlas in av ett avdelningens kontroll-konto. Du kan använda ett befintligt konto eller skapa ett nytt avdelningens kontroll-konto.

1. Anslut din Azure-Dataresurs till ditt avdelningens kontroll-konto.

    1. I avdelningens kontroll-portalen kan du gå till **hanterings Center** och ansluta din Azure-Dataresurs under avsnittet **externa anslutningar** .
    1. Välj **+ ny** i det översta fältet och leta upp din Azure-Dataresurs i popup-fältet och Lägg till data resurs kontot. Kör ett ögonblicks bild jobb när du har anslutit din data resurs till avdelningens kontroll-kontot så att data resursen till gångar och härkomst-information visas i avdelningens kontroll.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Hanterings Center för att länka Azure Data-resurs":::

1. Kör din ögonblicks bild i Azure Data Share.

    - När Azure Data Share-anslutningen har upprättats med Azure-avdelningens kontroll kan du köra en ögonblicks bild för dina befintliga resurser. 
    - Om du inte har några befintliga resurser går du till Azure Data Share-portalen för att [Dela dina data](../data-share/share-your-data.md) [och prenumerera på en data resurs](../data-share/subscribe-to-data-share.md).
    - När resurs ögonblicks bilden har slutförts kan du Visa tillhör ande data resurs till gångar och härkomst i avdelningens kontroll.

1. Identifiera data resurs konton och dela information i ditt avdelningens kontroll-konto.

    - På Start sidan för avdelningens kontroll-konto väljer du **Bläddra efter till gångs typ** och väljer panelen **Azure Data resurs** . Du kan söka efter ett konto namn, resurs namn, delad ögonblicks bild eller partner organisation. Använd annars filter på Sök Resultat sidan för konto namn, resurs typ (skickade eller mottagna resurser).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Azure Data resurs på Sök resultat sida":::

    >[!Important]
    >För data resurs till gångar som ska visas i avdelningens kontroll måste ett ögonblicks bild jobb köras efter att du anslutit data resursen till avdelningens kontroll.

1. Spåra härkomst av data uppsättningar som delas med Azure Data Share.

    - På sidan avdelningens kontroll Sök Resultat väljer du den ögonblicks bild för data resurs (tas emot/skickat) och väljer fliken **härkomst** för att se en härkomst graf med överordnade och underordnade beroenden.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Härkomst av data uppsättningar som delas med Azure Data Share":::

## <a name="next-steps"></a>Nästa steg

- [Användar guide för katalog härkomst](catalog-lineage-user-guide.md)
- [Länk till Azure Data Factory för härkomst](how-to-link-azure-data-factory.md)
