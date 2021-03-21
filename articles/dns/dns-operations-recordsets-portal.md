---
title: Hantera DNS-postuppsättningar och-poster med Azure DNS
description: Azure DNS ger möjlighet att hantera DNS-postuppsättningar och-poster när du är värd för din domän.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965739"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Hantera DNS-poster och post uppsättningar med hjälp av Azure Portal

Den här artikeln visar hur du hanterar post uppsättningar och poster för din DNS-zon med hjälp av Azure Portal.

Det är viktigt att förstå skillnaden mellan DNS-postuppsättningar och enskilda DNS-poster. En post uppsättning är en samling poster i en zon som har samma namn och är av samma typ. Mer information finns i [Skapa DNS-postuppsättningar och poster med hjälp av Azure Portal](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Skapa en ny post uppsättning och post

Om du vill skapa en post uppsättning i Azure Portal, se [Skapa DNS-poster med hjälp av Azure Portal](./dns-getstarted-portal.md).

## <a name="view-a-record-set"></a>Visa en post uppsättning

1. Gå till bladet **DNS-zon** i Azure Portal.
2. Sök efter post uppsättningen och markera den. Detta öppnar egenskaperna för post uppsättningen.

    ![Sök efter en post uppsättning](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Lägg till en ny post i en post uppsättning

Du kan lägga till upp till 20 poster i alla post uppsättningar. En post uppsättning får inte innehålla två identiska poster. Tomma post uppsättningar (med noll poster) kan skapas, men visas inte på Azure DNS namnservrar. Post uppsättningar av typen CNAME kan innehålla en post högst.

1. På bladet för **post uppsättningens egenskaper** för din DNS-zon klickar du på den post uppsättning som du vill lägga till en post i.

    ![Välj en post uppsättning](./media/dns-operations-recordsets-portal/selectset500.png)

2. Ange egenskaperna för post uppsättningen genom att fylla i fälten.

    ![Lägga till en post](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Spara inställningarna genom att klicka på **Spara** överst på bladet. Stäng sedan bladet.
4. I hörnet visas att posten sparas.

    ![Sparar uppsättning av poster](./media/dns-operations-recordsets-portal/saving150.png)

När posten har sparats kommer värdena på bladet **DNS-zon** att återspegla den nya posten.

## <a name="update-a-record"></a>Uppdatera en post

När du uppdaterar en post i en befintlig post uppsättning beror de fält som du kan uppdatera beroende på vilken typ av post du arbetar med.

1. Sök efter posten på bladet **post uppsättnings egenskaper** för din post uppsättning.
2. Ändra posten. När du ändrar en post kan du ändra de tillgängliga inställningarna för posten. I följande exempel är fältet **IP-adress** MARKERAT och IP-adressen håller på att ändras.

    ![Ändra en post](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Spara inställningarna genom att klicka på **Spara** överst på bladet. I det övre högra hörnet visas ett meddelande om att posten har sparats.

    ![Sparad uppsättning av poster](./media/dns-operations-recordsets-portal/saved150.png)

När posten har sparats kommer värdena för post uppsättningen på bladet **DNS-zon** att återspegla den uppdaterade posten.

## <a name="remove-a-record-from-a-record-set"></a>Ta bort en post från en post uppsättning

Du kan använda Azure Portal för att ta bort poster från en post uppsättning. Observera att om du tar bort den sista posten från en post uppsättning tas inte post uppsättningen bort.

1. Sök efter posten på bladet **post uppsättnings egenskaper** för din post uppsättning.
2. Klicka på den post som du vill ta bort. Välj sedan **ta bort**.

    ![Ta bort en post](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Spara inställningarna genom att klicka på **Spara** överst på bladet.
4. När posten har tagits bort kommer värdena för posten på bladet **DNS-zon** att återspegla borttagningen.

## <a name="delete-a-record-set"></a><a name="delete"></a>Ta bort en post uppsättning

1. Klicka på **ta bort** på bladet **post uppsättnings egenskaper** för din post uppsättning.

    ![Ta bort en post uppsättning](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Ett meddelande visas där du tillfrågas om du vill ta bort post uppsättningen.
3. Kontrol lera att namnet matchar den post uppsättning som du vill ta bort och klicka sedan på **Ja**.
4. På bladet **DNS-zon** kontrollerar du att post uppsättningen inte längre visas.

## <a name="work-with-ns-and-soa-records"></a>Arbeta med NS-och SOA-poster

NS-och SOA-poster som skapas automatiskt hanteras annorlunda än andra post typer.

### <a name="modify-soa-records"></a>Ändra SOA-poster

Det går inte att lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättningen vid zonens Apex (namn = " \@ "). Du kan dock ändra någon av parametrarna i SOA-posten (förutom "värd") och post uppsättningens TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster vid zonens Apex

NS-postuppsättningen på zon Apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på de Azure DNS namnservrar som har tilldelats zonen.

Du kan lägga till fler namnservrar i den här NS-postuppsättningen för att stödja samvärdbaserade domäner med fler än en DNS-Provider. Du kan också ändra TTL och metadata för den här post uppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS namnservrarna.

Observera att detta endast gäller för NS-postuppsättningen i zonens Apex. Andra NS-postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

### <a name="delete-soa-or-ns-record-sets"></a>Ta bort SOA-eller NS-postuppsättningar

Du kan inte ta bort SOA-och NS-postuppsättningarna i zonens Apex (namn = \@ ) som skapas automatiskt när zonen skapas. De tas bort automatiskt när du tar bort zonen.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure DNS finns i [Översikt över Azure DNS](dns-overview.md).
* Mer information om hur du automatiserar DNS finns i [Skapa DNS-zoner och post uppsättningar med hjälp av .NET SDK](dns-sdk.md).
* Mer information om omvända DNS-poster finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).
* Mer information om Azure DNS Ali Aset-poster finns i [Översikt över Azure DNS Ali Aset-poster](dns-alias.md).