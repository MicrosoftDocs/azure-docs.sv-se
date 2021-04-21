---
title: Ansluta till Azure Analysis Services med Power BI | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Power BI. När de är anslutna kan användarna utforska modelldata.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 4/20/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5a430c5bb24032a2665ad078311dcb5137d2bb9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816042"
---
# <a name="connect-with-power-bi"></a>Anslut med Power BI

När du har skapat en server i Azure och distribuerat en tabellmodell till den är användarna i din organisation redo att ansluta och börja utforska data. 

> [!NOTE]
> Om du publicerar en Power BI Desktop-modell till Power BI-tjänst på Azure Analysis Services-servern ser du till att Case-Sensitive-sorteringsserveregenskapen inte är markerad (standard). Egenskapen Case-Sensitive server kan anges med hjälp av SQL Server Management Studio.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. I Power BI Desktop du på **Hämta data**  >  **Azure** Azure Analysis Services  >  **databas**.

2. I **Server** anger du servernamnet. Se till att inkludera den fullständiga URL:en. till exempel asazure://westcentralus.asazure.windows.net/advworks.

3. Om **du** känner till namnet på tabellmodelldatabasen eller perspektivet som du vill ansluta till i Databas klistrar du in det här. Annars kan du lämna det här fältet tomt och välja en databas eller ett perspektiv senare.

4. Välj ett anslutningsalternativ och tryck sedan på **Anslut.** 

    Både **alternativen Anslut live** **och** Importera stöds. Vi rekommenderar dock att du använder live-anslutningar eftersom importläget har vissa begränsningar. framför allt kan serverprestanda påverkas under importen. Om modellen ska uppdateras i Power BI-tjänst inställningen Tillåt **åtkomst** från Power BI inställningen endast när du väljer **Anslut live**.

5. Ange dina inloggningsuppgifter om du uppmanas att göra det. 

   > [!NOTE]
   > Engångslösenordskonton (OTP) stöds inte. 

6. I **Navigatör** expanderar du servern och väljer sedan den modell eller det perspektiv som du vill ansluta till och klickar sedan på **Anslut.** Klicka på en modell eller ett perspektiv för att visa alla objekt för vyn.

    Modellen öppnas i Power BI Desktop med en tom rapport i rapportvyn. I listan Fält visas alla modellobjekt som inte är dolda. Anslutningsstatus visas i det nedre högra hörnet.

## <a name="connect-in-power-bi-service"></a>Ansluta i Power BI (tjänst)

1. Skapa en Power BI Desktop-fil som har en live-anslutning till din modell på servern.
2. I [Power BI](https://powerbi.microsoft.com)klickar du **på Hämta**  >  **datafiler** och letar sedan upp och väljer pbix-filen.

## <a name="see-also"></a>Se även
[Ansluta till Azure Analysis Services](analysis-services-connect.md)   
[Klientbibliotek](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
