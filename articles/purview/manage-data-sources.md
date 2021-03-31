---
title: Hantera data källor i Azure avdelningens kontroll (för hands version)
description: Lär dig hur du registrerar nya data källor, hanterar samlingar av data källor och visar källor i Azure avdelningens kontroll (för hands version).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694469"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Hantera data källor i Azure avdelningens kontroll (för hands version)

I den här artikeln får du lära dig hur du registrerar nya data källor, hanterar samlingar av data källor och visar källor i Azure avdelningens kontroll (för hands version)

## <a name="register-a-new-source"></a>Registrera en ny källa

Använd följande steg för att registrera en ny källa.

1. Öppna avdelningens kontroll Studio och välj panelen **Registrera källor** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure avdelningens kontroll Studio":::

1. Välj **Registrera** och välj sedan en källtyp. I det här exemplet används Azure Blob Storage. Välj **Fortsätt**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Välj en typ av data källa på sidan Registrera källor":::

1. Fyll i formuläret på sidan **Registrera källor** . Välj ett namn för källan och ange relevant information. Om du väljer **från Azure-prenumeration** som val av konto visas källorna i din prenumeration i en listruta. Alternativt kan du ange din käll information manuellt.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulär för data käll information":::

1. Välj **Slutför**.

## <a name="view-sources"></a>Visa källor

Du kan visa alla registrerade källor på fliken **källor** i Azure avdelningens kontroll Studio. Det finns två visnings typer: Map-vy och listvy.

### <a name="map-view"></a>Kartvy

I Map-vyn kan du se alla dina källor och samlingar. I följande bild finns en Azure Blob Storage-källa. Från varje käll panel kan du redigera källan, starta en ny skanning eller Visa information om källan.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Översikt över data källa för Azure avdelningens kontroll":::

### <a name="list-view"></a>Listvy

I listvyn kan du se en sorterad lista över källor. Hovra över källan för alternativ att redigera, påbörja en ny skanning eller ta bort.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Listvy för visning av data källor i Azure avdelningens kontroll":::

## <a name="manage-collections"></a>Hantera samlingar

Du kan gruppera dina data källor i samlingar. Om du vill skapa en ny samling väljer du **+ ny samling** på sidan *källor* i Azure avdelningens kontroll Studio. Ge samlingen ett namn och välj *ingen* som överordnad. Den nya samlingen visas i Map-vyn.

Om du vill lägga till källor i en samling väljer du **Redigera** blyertspennan på källan och väljer en samling på list menyn **Välj en samling** .

Om du vill skapa en hierarki med samlingar tilldelar du samlingar på högre nivå som en överordnad till lågnivå samlingar. I följande bild är *Fabrikam* en överordnad till *ekonomi* samlingen som innehåller en data källa för Azure Blob Storage. Du kan komprimera eller expandera samlingar genom att klicka på cirkeln som är kopplad till pilen mellan nivåer.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="En hierarki med samlingar i Azure avdelningens kontroll Studio":::

Du kan ta bort källor från en hierarki genom att välja *ingen* för den överordnade. Ej överordnade källor grupperas i en prickad ruta i Map-vyn utan att några pilar länkar dem till föräldrar.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du registrerar och genomsöker olika data Källor:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI klient](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
