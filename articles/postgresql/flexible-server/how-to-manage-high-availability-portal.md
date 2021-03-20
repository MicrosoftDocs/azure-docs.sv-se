---
title: Hantera redundant zon hög tillgänglighet – Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: I den här artikeln beskrivs hur du aktiverar eller inaktiverar zonens redundanta hög tillgänglighet i Azure Database for PostgreSQL flexibel Server genom Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90937008"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Hantera redundanta zoner med hög tillgänglighet i flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln beskriver hur du kan aktivera eller inaktivera zonens redundanta konfiguration för hög tillgänglighet på din flexibla Server.

Funktionen för hög tillgänglighet etablerar fysiskt separat primär och vänte replik i olika zoner. Mer information finns i [dokumentation om begrepp för hög tillgänglighet](./concepts-high-availability.md). Du kan välja att aktivera hög tillgänglighet när du skapar en flexibel Server eller när den har skapats. Den här sidan innehåller rikt linjer för hur du kan aktivera eller inaktivera hög tillgänglighet. Den här åtgärden ändrar inte dina andra inställningar, inklusive VNET-konfiguration, brand Väggs inställningar och kvarhållning av säkerhets kopior. På samma sätt är aktivering och inaktive ring av hög tillgänglighet en online-åtgärd och påverkar inte dina program anslutningar och-åtgärder.

## <a name="pre-requisites"></a>Förutsättningar

Zon redundant hög tillgänglighet är endast tillgängligt i regioner där flera zoner stöds. 

## <a name="enable-high-availability-during-server-creation"></a>Aktivera hög tillgänglighet när servern skapas

Det här avsnittet innehåller information som är specifik för de fält som är relaterade till. Du kan följa de här stegen för att distribuera hög tillgänglighet när du skapar en flexibel Server.

1.  I [Azure Portal](https://portal.azure.com/)väljer du flexibel Server och klickar på Skapa.  Mer information om hur du fyller i information som **prenumeration**, **resurs grupp**, **Server namn**, **region** och andra fält finns i instruktions dokumentation för att skapa servern.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Visa prenumeration och region":::

2.  Välj din **tillgänglighets zon**. Detta är användbart om du vill samordna ditt program i samma tillgänglighets zon som databasen för att minska svars tiden. Välj **ingen inställning** om du vill att den flexibla servern ska distribueras i valfri tillgänglighets zon.
    ![Val av AZ val av ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="tillgänglighets zon":::  

3.  Klicka på kryss rutan för **zonen redundant hög tillgänglighet** i tillgänglighets alternativet.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Kryss ruta för hög tillgänglighet":::

4.  Klicka på  **Konfigurera Server** om du vill ändra standard beräkningen och lagringen.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Konfigurera Server – beräkning + lagring":::  

5.  Om alternativet för hög tillgänglighet är markerat är den Burstable nivån inte tillgänglig för att välja. Du kan välja antingen **generell användning** eller **minnesoptimerade** beräknings nivåer. Sedan kan du välja **beräknings storlek** för ditt val i list rutan.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Val av beräknings nivå":::  


6.  Välj **lagrings storlek** i GIB med hjälp av glidande stapel och välj **tids perioden för kvarhållning av säkerhets kopior** mellan 7 dagar och 35 dagar.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Säkerhets kopiering av lagring"::: 

7. Klicka på **Spara**. 

## <a name="enable-high-availability-post-server-creation"></a>Aktivera skapande av post server med hög tillgänglighet

Följ dessa steg om du vill aktivera hög tillgänglighet för din befintliga flexibla Server.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den befintliga flexibla postgresql-servern.

2.  På sidan flexibel Server klickar du på **hög tillgänglighet** i den vänstra panelen för att öppna sidan med hög tillgänglighet.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Vänster panel val"::: 

3.  Klicka på kryss rutan **zon redundant hög tillgänglighet** för att **Aktivera** alternativet och klicka på **Spara** för att spara ändringen.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Aktivera hög tillgänglighet"::: 

4.  En bekräftelse dialog ruta visar att om du aktiverar hög tillgänglighet kommer kostnaden att öka på grund av ytterligare Server-och lagrings distribution.

5.  Klicka på knappen **Aktivera ha** för att aktivera hög tillgänglighet.

6.  Ett meddelande visas som talar om att distributionen av hög tillgänglighet pågår.

## <a name="disable-high-availability"></a>Inaktivera hög tillgänglighet

Följ dessa steg om du vill inaktivera hög tillgänglighet för din flexibla server som redan har kon figurer ATS med zon redundans.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den befintliga Azure Database for PostgreSQL-flexibla servern.

2.  På sidan flexibel Server klickar du på **hög tillgänglighet** på Front panelen för att öppna sidan med hög tillgänglighet.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Vänster panel val"::: 

3.  Klicka på kryss rutan **zon redundant hög tillgänglighet** om du vill **inaktivera** alternativet. Klicka sedan på **Spara** för att spara ändringen.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Inaktivera hög tillgänglighet"::: 

4.  En bekräftelse dialog ruta visas där du kan bekräfta att du har inaktiverat hög tillgänglighet.

5.  Klicka på **inaktivera ha** om du vill inaktivera hög tillgänglighet.

6.  Ett meddelande visas som tar upp inaktive ring av distributionen av hög tillgänglighet.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
