---
title: Hantera Server grupper
description: Beskriver hur du hanterar Server grupper
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 050b3b4d67eda9b6c9b4621c014e3e6baad34053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173808"
---
# <a name="manage-farms"></a>Hantera lantbruk

Du kan hantera dina Server grupper i Azure FarmBeats. Den här artikeln innehåller information om hur du skapar grupper, installerar enheter, sensorer och drönare som hjälper dig att hantera dina grupper.

## <a name="create-farms"></a>Skapa Server grupper

Gör så här:

1. Logga in på Server grupps acceleratorn visas sidan **grupper** .
    Sidan **Server grupper** visar listan över Server grupper, om de redan har skapats i prenumerationen.

    Här är exempel bilden:

    ![Skärm bild som visar sidan grupper.](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Välj **skapa Server grupp** och ange **namn**, **grödor** och **adress**.
3. I **definiera server gruppens gränser**(obligatoriskt fält) väljer du antingen **mark på Map** eller **Klistra in interjson-kod**.

Här är två sätt att definiera en server grupps gränser:

1. **Markering på kartan**: Använd kart kontroll verktyget för att rita och markera Server gruppens gränser. För att markera gränserna  ![ visas skärm bilden som visar Penn ikonen för ritnings gränser på kartan ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) och markera exakta gränser.

    ![Skärm bild som visar de ritade gränserna på en karta.](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Klistra in geografiskt JSON-kod**: geografin är ett format för kodning av geografiska data strukturer med hjälp av JavaScript Object Notation (JSON). Det här alternativet visar en text ruta där en interjson-sträng kan anges för att markera Server gruppens gränser. Du kan också skapa en interjson-kod från GeoJSON.io.
Fyll i informationen med hjälp av knapp beskrivningarna.

    ![Skärm bild som visar alternativet Klistra in polyjson-kod på skärmen skapa Server grupp.](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Välj **Skicka** för att skapa en Server grupp. En ny grupp skapas och visas på sidan **Server grupper** .

## <a name="view-farm"></a>Visa Server grupp

Sidan Server grupp lista visar en lista över skapade Server grupper. Välj en grupp för att visa listan över:

 - **Antal enheter** – visar antal och status för enheter som har distribuerats i Server gruppen.
 - **Karta** – karta över Server gruppen med de enheter som distribueras i Server gruppen.
 - **Telemetri** – visar Telemetrin från sensorer som distribuerats i Server gruppen.
 - **Senaste precision Maps** – visar den senaste satellit index kartan (Evi, NDWI), jord-fuktens termisk karta och sensor placerings karta.

## <a name="edit-farm"></a>Redigera Server grupp

Sidan **grupper** visar en lista över skapade Server grupper.

1.  Välj en Server grupp för att visa och redigera Server gruppen.
2.  Välj **Redigera server** grupp för att redigera Server grupps informationen. I fönstret **Server grupps information** kan du redigera fält för **namn**, **grödor**, **adress** och definiera **Server grupps gränser** .

    ![Taktslag i projekt grupp](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Välj **Skicka** för att spara informationen som redige ras.

## <a name="delete-farm"></a>Ta bort Server grupp

Sidan **grupper** visar en lista över skapade grupper. Använd följande steg för att ta bort en Server grupp:

1.  Välj en Server grupp i listan om du vill ta bort Server grupps information.
2.  Välj **ta bort Server grupp** för att ta bort Server gruppen.

    ![Skärm bild som visar sidan ta bort Server grupp och markerar knappen Ta bort.](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > När du tar bort en Server grupp tas inte enheterna och mapparna som är associerade med gruppen bort. All Server grupp information som är kopplad till enheten och kartor är inte relevant. Du kan fortsätta att visa enheter, telemetri och kartor från FarmBeats-tjänsten.


## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din server grupp, lär du dig hur du [hämtar sensor data](get-sensor-data-from-sensor-partner.md) som flödar till Server gruppen.
