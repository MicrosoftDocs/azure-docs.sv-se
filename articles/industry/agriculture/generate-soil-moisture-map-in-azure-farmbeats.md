---
title: Generera jord fukt termisk karta
description: Beskriver hur du kan generera jord fukt termisk karta i Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: dd8d688355e0f71ce77cdbc6012d788c7b16d825
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173791"
---
# <a name="generate-soil-moisture-heatmap"></a>Generera jord fukt termisk karta

Jord fukt är det vatten som hålls i utrymmet mellan jord partiklar.Jord-och fukt termisk karta hjälper dig att förstå fukt data i djup och med hög upplösning i dina grupper. En enhetlig distribution av sensorer från samma provider krävs för att generera en korrekt och användbar jord fukt termisk karta. Olika leverantörer kommer att ha skillnader i hur jord fukten mäts tillsammans med skillnader i kalibreringen. Termisk karta genereras för ett visst djup med de sensorer som distribueras med det djupet.

Den här artikeln beskriver processen för att skapa en jord-fukt-termisk karta för din server grupp med hjälp av Azure FarmBeats-acceleratorn. I den här artikeln får du lära dig att:

- [Skapa Server grupper](#create-a-farm)
- [Tilldela sensorer till Server grupper](#get-soil-moisture-sensor-data-from-partner)
- [Generera jord fukt termisk karta](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera följande:  

- En Azure-prenumeration.
- En instans av Azure-FarmBeats som körs.
- Minst tre jord fukts sensorer är tillgängliga för Server gruppen.

## <a name="create-a-farm"></a>Skapa en Server grupp

En grupp är ett geografiskt område som du vill skapa en jord-och fukt termisk karta för. Du kan skapa en Server grupp med hjälp av Server grupps- [API: et](https://aka.ms/FarmBeatsDatahubSwagger) eller i [användar gränssnittet för FarmsBeats Accelerator](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Distribuera sensorer

Du bör fysiskt distribuera jord fuktighets sensorer i Server gruppen. Du kan köpa jord fuktighets sensorer från någon av våra godkända partners- [Davis-instrument](https://www.davisinstruments.com/product/enviromonitor-gateway/) och [Teralytic](https://teralytic.com/). Du måste koordinera med sensor leverantören för att göra den fysiska installationen i Server gruppen.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Hämta sensor data för jord fukt från partner

När sensorer börjar strömma data till partner data panelen, aktive ras data till Azure-FarmBeats. Detta kan göras från partner programmet.

Om du till exempel har köpt Davis-sensorer kommer du att logga in på ditt väder länks konto och ange de autentiseringsuppgifter som krävs för att aktivera data strömningen till Azure-FarmBeats. Om du vill hämta de autentiseringsuppgifter som krävs följer du anvisningarna från [Hämta sensor data](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

När du har angett dina autentiseringsuppgifter och väljer **Skicka** i partner programmet kan du låta data flöda in i Azure-FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Tilldela server gruppen jord fuktighets sensorer

När du har länkat ditt sensor konto till Azure FarmBeats måste du tilldela jord vatten bruks sensorer till intresse gruppen.

1.  På sidan start väljer du **Server grupper** på menyn, sidan **Server** grupps lista visas.
2.  Välj **grupp** för att  >  **lägga till enheter**.
3.  Fönstret **Lägg till enheter** visas. Välj en enhet som är länkad till jord fuktens sensorer för din server grupp.

    ![Skärm bild som visar skärmen Lägg till enheter.](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Välj **Lägg till enheter**.     

## <a name="generate-soil-moisture-heatmap"></a>Generera jord fukt termisk karta

Det här steget är att skapa ett jobb eller en tids krävande åtgärd som kommer att generera jord fukt termisk karta för din server grupp.

1.  På Start sidan går du till **Server grupper** på den vänstra navigerings menyn för att visa sidan grupper.
2.  Välj **Server grupp**.
3.  På sidan **Server grupps information** väljer du **generera precisions karta**.
4.  Välj **jord fukt** på den nedrullningsbara menyn.
5.  I fönstret **jord fukt** väljer du **den här veckan**.
6.  Ange det mått som du vill använda för kartan i **måttet** **Välj jord fuktighets** sensor.
    Om du vill hitta sensor måttet i **sensorer** väljer du en jord fuktighets sensor. I **sensor egenskaper** använder du **måttets namn** värde.

    ![Skärm bild som visar jord fukt skärmen.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Välj **generera kartor**.
    Ett bekräftelse meddelande med jobb information visas. Mer information finns i jobb status i jobb.

    >[!NOTE]
    > Jobbet tar cirka tre till fyra timmar att slutföra.

### <a name="download-the-soil-moisture-heatmap"></a>Ladda ned jordens fukt termisk karta

Gör så här:

1. På sidan **jobb** kontrollerar du **jobb status** för det jobb som du skapade i den senaste proceduren.
2. När jobb **statusen visas väljer** du **Maps** på menyn.
3. Sök efter kartan efter den dag då den skapades i formatet <jord-moisture_MyFarm_YYYY-MM-DD>.
4. Välj en karta i kolumnen **namn** . ett popup-fönster visas med förhands granskningen för den valda kartan.
5. Välj **Hämta**. Kartan laddas ned och lagras i den lokala mappen på datorn.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en jord-fukt termisk karta kan du läsa om hur du [genererar sensor placering](generate-maps-in-azure-farmbeats.md#sensor-placement-map) och matar in [historiska telemetridata](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
