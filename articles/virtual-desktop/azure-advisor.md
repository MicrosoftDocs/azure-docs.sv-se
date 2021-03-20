---
title: Integrera Windows Virtual Desktop med Azure Advisor – Azure
description: Använda Azure Advisor med Windows-distributionen för virtuella datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147657"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Använda Azure Advisor med Windows Virtual Desktop

Azure Advisor kan hjälpa användarna att lösa vanliga problem på egen hand utan att behöva fil support ärenden. Rekommendationerna minskar behovet av att skicka hjälp förfrågningar, vilket sparar tid och kostnader.

I den här artikeln får du lära dig hur du konfigurerar Azure Advisor i Windows-distributionen för virtuella skriv bord som hjälper användarna.

## <a name="what-is-azure-advisor"></a>Vad är Azure Advisor?

Azure Advisor analyserar dina konfigurationer och telemetri för att erbjuda personliga rekommendationer för att lösa vanliga problem. Med dessa rekommendationer kan du optimera dina Azure-resurser för tillförlitlighet, säkerhet, drift kvalitet, prestanda och kostnad. Läs mer på [Azure Advisor webbplats](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Börja använda Azure Advisor

Allt du behöver för att komma igång är ett Azure-konto på Azure Portal. Först öppnar du Azure Portal på <https://portal.azure.com/#home> och väljer **Advisor** under Azure- **tjänster**, som du ser i följande bild. Du kan också ange "Azure Advisor" i Sök fältet i Azure Portal.

> [!div class="mx-imgBorder"]
> ![En skärm bild av Azure Portal. Användaren håller mus pekaren över Azure Advisor länken, vilket gör att en nedrullningsbar meny visas.](media/azure-advisor.png)

När du öppnar Azure Advisor visas fem kategorier:

- Cost
- Säkerhet
- Tillförlitlighet
- Driftseffektivitet
- Prestanda

> [!div class="mx-imgBorder"]
> ![En skärm bild av Azure Advisor som visar de fem menyerna för varje kategori. De fem objekt som visas i deras egna rutor är kostnader, säkerhet, tillförlitlighet, drift kvalitet och prestanda.](media/advisor-categories.png)

När du väljer en kategori går du till sidan med aktiva rekommendationer. På den här sidan kan du se vilka rekommendationer Azure Advisor har för dig, som du ser i följande bild.

> [!div class="mx-imgBorder"]
> ![En skärm bild av listan över aktiva rekommendationer för drift kvalitet. I listan visas sju rekommendationer med varierande prioritets nivåer.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Ytterligare tips för Azure Advisor

- Var noga med att kontrol lera dina rekommendationer ofta, minst mer än en gång i veckan. Azure Advisor uppdaterar sina aktiva rekommendationer flera gånger per dag. Att söka efter nya rekommendationer kan förhindra större problem genom att hjälpa dig att upptäcka och lösa mindre.

- Försök alltid att lösa problemen med högsta prioritets nivå i Azure Advisor. Problem med hög prioritet markeras med rött. Om du lämnar rekommendationer med hög prioritet kan det leda till problem med att lösa problemet.

- Om en rekommendation verkar mindre viktig kan du stänga av den eller skjuta upp den. Om du vill stänga av eller skjuta upp en rekommendation går du till kolumnen **åtgärd** och ändrar objektets status.

- Ignorera inte rekommendationer förrän du vet varför de visas och är säkra på att de inte har någon negativ inverkan på dig eller dina användare. Välj alltid **Läs mer** för att se vad problemet är. Om du löser ett problem genom att följa anvisningarna i Azure Advisor försvinner det automatiskt från listan. Det är bättre att lösa problem än att skjuta upp dem upprepade gånger.

- När du stöter på ett problem i Windows Virtual Desktop bör du alltid kontrol lera Azure Advisor först. Azure Advisor ger dig anvisningar om hur du kan lösa problemet eller minst en resurs som kan hjälpa dig.

## <a name="next-steps"></a>Nästa steg

Information om hur du löser rekommendationer finns i [så här löser du Azure Advisor rekommendationer](azure-advisor-recommendations.md).

Om du har förslag på nya rekommendationer kan du publicera dem i vårt [Azure Advisor User Voice-forum](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
