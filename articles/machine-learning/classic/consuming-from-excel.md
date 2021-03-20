---
title: 'ML Studio (klassisk): använda webb tjänsten i Excel – Azure'
description: Azure Machine Learning Studio (klassisk) gör det enkelt att anropa webb tjänster direkt från Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517866"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Använda en Azure Machine Learning Studio (klassisk) webb tjänst från Excel

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klassisk) gör det enkelt att anropa webb tjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online rekommenderar vi att du använder Excel [Excel Excel-tillägget](excel-add-in-for-web-services.md).



## <a name="steps"></a>Steg
Publicera en webb tjänst. [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md) förklarar hur du gör det. För närvarande stöds inte funktionen Excel-arbetsbok för Request/Response-tjänster som har en enda utmatning (det vill säga en enda bedömnings etikett). 

När du har en webb tjänst klickar du på avsnittet **webb tjänster** till vänster om Studio och väljer sedan den webb tjänst som du vill använda från Excel.

**Klassisk webb tjänst**

1. På fliken **instrument panel** för webb tjänsten finns en rad för tjänsten **Request/Response** . Om tjänsten hade en enda utdata bör du se länken **Hämta Excel-arbetsbok** på den raden.

    ![Hämta Excel-arbetsboken med hjälp av webb tjänst portalen Studio (klassisk)](./media/consuming-from-excel/excellink.png)
2. Klicka på **Hämta Excel-arbetsbok**.

**Ny webb tjänst**

1. I Azure Machine Learning-webbtjänst-portalen väljer du **förbruka**.
2. På sidan konsumera i avsnittet alternativ för **webb tjänst användning** klickar du på Excel-ikonen.

**Använda arbets boken**

1. Öppna arbets boken.
2. En säkerhets varning visas. Klicka på knappen **Aktivera redigering** .

    ![Aktivera redigering för att ta bort säkerhets varningen skyddad vy](./media/consuming-from-excel/enableeditting.png)
3. En säkerhets varning visas. Klicka på knappen **Aktivera innehåll** för att köra makron i kalkyl bladet.

    ![Aktivera innehåll för att stänga säkerhets varningen genom att inaktivera makron](./media/consuming-from-excel/enablecontent.png)
4. När makron har Aktiver ATS genereras en tabell. Kolumner i blått krävs som inmatade i webb tjänsten resurs resurs, eller **parametrar**. Notera resultatet av resurs resurs tjänsten, **förutsägande värden** i grönt. När alla kolumner för en specifik rad är fyllda anropar arbets boken poängsättnings-API: et och visar resultatet.

    ![Tabell för parameter indata och resulterande förutsägande värden](./media/consuming-from-excel/sampletable.png)
5. Om du vill visa mer än en rad fyller du i den andra raden med data och de förväntade värdena skapas. Du kan till och med klistra in flera rader samtidigt.

Du kan använda någon av Excel-funktionerna (grafer, Power Map, villkorsstyrd formatering osv.) med förväntade värden för att visualisera data.

## <a name="sharing-your-workbook"></a>Dela din arbets bok
För att makron ska fungera måste API-nyckeln ingå i kalkyl bladet. Det innebär att du endast bör dela arbets boken med entiteter/individer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
Ett resurs resurs anrop görs i följande två situationer:

1. Första gången en rad har innehåll i alla dess **parametrar**
2. När någon av **parametrarna** ändras i en rad där alla **parametrar** har angetts.