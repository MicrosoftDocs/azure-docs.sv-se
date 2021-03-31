---
title: Tillämpa klassificeringar på till gångar (förhands granskning)
description: I det här dokumentet beskrivs hur du tillämpar klassificeringar på till gångar.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96554871"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Tillämpa klassificeringar på till gångar i Azure avdelningens kontroll

Den här artikeln beskriver hur du tillämpar klassificeringar på till gångar.

## <a name="introduction"></a>Introduktion

Klassificeringar kan vara system-eller anpassade typer. System klassificeringar finns i avdelningens kontroll som standard. Du kan skapa anpassade klassificeringar baserat på ett mönster för reguljära uttryck. Klassificeringar kan tillämpas på till gångar antingen automatiskt eller manuellt.

Det här dokumentet beskriver hur du tillämpar klassificeringar på dina data.

## <a name="prerequisites"></a>Förutsättningar

- Skapa anpassade klassificeringar utifrån dina behov.
- Konfigurera genomsökningen på dina data källor.

## <a name="apply-classifications"></a>Applicera klassificeringar
I Azure avdelningens kontroll kan du använda system eller anpassade klassificeringar på en fil-, tabell-eller kolumn till gång. Den här artikeln beskriver stegen för att manuellt tillämpa klassificeringar på dina till gångar.

### <a name="apply-classification-to-a-file-asset"></a>Tillämpa klassificering på en fil till gång
Azure avdelningens kontroll kan genomsöka och klassificera dokumentations filer automatiskt. Om du t. ex. har en fil med namnet *multiple.docx* och den har ett nationellt ID-nummer i sitt innehåll, lägger Azure avdelningens kontroll till klassificeringen **EU National Identification Number** till filens till gångs informations sida.

I vissa fall kanske du vill lägga till klassificeringar manuellt till din fil till gång. Om du har flera filer som är grupperade i en resurs uppsättning lägger du till klassificeringar på resurs uppsättnings nivå.

Följ dessa steg om du vill lägga till en anpassad eller system klassificering till en partition resurs uppsättning:

1. Sök efter eller bläddra i partitionen och gå till sidan till gångs information.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Skärm bild som visar sidan till gångs information.":::

1. På fliken **Översikt** visar du avsnittet **klassificeringar** för att se om det finns några befintliga klassificeringar. Välj **Redigera**.

1. I list rutan **klassificeringar** väljer du de enskilda klassificeringar som du är intresse rad av. Till exempel **kreditkorts nummer**, som är en system klassificering och **CustomerAccountID**, som är en anpassad klassificering.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Skärm bild som visar hur du väljer klassificeringar som ska läggas till i en till gång.":::

1. Välj **Spara**

1. På fliken **Översikt** bekräftar du att de klassificeringar som du har valt visas under avsnittet **klassificeringar** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Skärm bild som visar hur du bekräftar klassificeringar har lagts till i en till gång.":::

### <a name="apply-classification-to-a-table-asset"></a>Tillämpa klassificering på en tabell till gång

När Azure avdelningens kontroll söker igenom data källorna tilldelar den inte automatiskt klassificeringar till tabell till gångar. Om du vill att din tabell till gång ska ha en klassificering måste du lägga till den manuellt.

Så här lägger du till en klassificering i en tabell till gång:

1. Hitta en tabell till gång som du är intresse rad av. Till exempel **kund** tabell.

1. Bekräfta att inga klassificeringar har tilldelats tabellen. Välj **Redigera**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Skärm bild som visar hur du visar och redigerar klassificeringarna för en tabell till gång.":::

1. Välj en eller flera klassificeringar i list rutan **klassificeringar** . I det här exemplet används en anpassad klassificering med namnet **CustomerInfo**, men du kan välja alla klassificeringar för det här steget.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Skärm bild som visar hur du väljer klassificeringar som ska läggas till i en tabell till gång.":::

1. Spara klassificeringarna genom att välja **Spara** .

1. På sidan **Översikt** kontrollerar du att Azure-avdelningens kontroll har lagt till de nya klassificeringarna.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Skärm bild som visar hur du verifierar att klassificeringar har lagts till i en tabell till gång.":::

### <a name="add-classification-to-a-column-asset"></a>Lägg till klassificering i en kolumn till gång

Azure-avdelningens kontroll genomsöker automatiskt och lägger till klassificeringar i alla kolumn till gångar. Men om du vill ändra klassificeringen kan du göra det på kolumn nivå.

Så här lägger du till en klassificering i en kolumn:

1. Leta upp och markera kolumnen till gång och välj sedan **Redigera** på fliken **Översikt** .

1. Välj fliken **schema**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Skärm bild som visar hur du redigerar schemat för en kolumn.":::

1. Identifiera de kolumner som du är intresse rad av och välj **Lägg till en klassificering**. I det här exemplet läggs en klassificering av **vanliga lösen ord** till i kolumnen **PasswordHash** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Skärm bild som visar hur du lägger till en klassificering i en kolumn.":::

1. Välj **Spara**

1. Välj fliken **schema** och bekräfta att klassificeringen har lagts till i kolumnen.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Skärm bild som visar hur du bekräftar att en klassificering har lagts till i ett kolumn schema.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Effekter av genomsökning på befintliga klassificeringar

Klassificeringar tillämpas första gången baserat på exempel kontroll av dina data och matchar dem mot set regex-mönstret. Vid tidpunkten för genomsökningen, om nya klassificeringar tillämpas, hämtar kolumnen ytterligare klassificeringar. Befintliga klassificeringar finns kvar i kolumnen och måste tas bort manuellt.

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar en anpassad klassificering finns i [skapa en anpassad klassificering](create-a-custom-classification-and-classification-rule.md).