---
title: Azure CDN real tids aviseringar | Microsoft Docs
description: Aviseringar i real tid i Microsoft Azure CDN. Aviseringar i real tid ger aviseringar om prestanda för slut punkterna i din CDN-profil.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84887247"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Aviseringar i real tid i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
I det här dokumentet förklaras aviseringar i real tid i Microsoft Azure CDN. Den här funktionen ger meddelanden i real tid om prestanda för slut punkterna i din CDN-profil.  Du kan ställa in e-post eller HTTP-aviseringar baserat på:

* Bandbredd
* Statuskoder
* Cache status
* Anslutningar

## <a name="creating-a-real-time-alert"></a>Skapa en avisering i real tid
1. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera CDN-profil](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
3. Hovra över fliken **analys** och hovra över den utfällbara **statistiken i real tid** .  Klicka på **aviseringar i real tid**.
   
    ![Hanterings Portal för CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Listan över befintliga aviserings konfigurationer (om sådana finns) visas.
4. Klicka på knappen **Lägg till avisering** .
   
    ![Knappen Lägg till avisering](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Ett formulär för att skapa en ny avisering visas.
   
    ![Nytt aviserings formulär](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Om du vill att den här aviseringen ska vara aktiv när du klickar på **Spara** markerar du kryss rutan **avisering aktive rad** .
6. Ange ett beskrivande namn för aviseringen i fältet **namn** .
7. I list rutan **medietyp** väljer du **http-stort objekt**.
   
    ![Medietyp med ett stort HTTP-objekt har valts](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Du måste välja ett **stort http-objekt** som **medietyp**.  De andra alternativen används inte av **Azure CDN från Verizon**.  Om du inte väljer ett **stort http-objekt** utlöses aviseringen aldrig.
   > 
   > 
8. Skapa ett **uttryck** som ska övervakas genom att välja ett **mått**, en **operator** och ett **Utlösar-värde**.
   
   * För **mått** väljer du den typ av villkor som du vill övervaka.  **Bandbredd Mbit/** s är mängden bandbredds användning i megabit per sekund.  **Totalt antal anslutningar** är antalet samtidiga http-anslutningar till våra Edge-servrar.  Definitioner av olika cache-statusar och status koder finns i [Azure CDN cache status koder](/previous-versions/azure/mt759237(v=azure.100)) och [Azure CDN HTTP-statuskod](/previous-versions/azure/mt759238(v=azure.100))
   * **Operatorn** är den matematiska operator som upprättar relationen mellan måttet och Utlösar svärdet.
   * **Utlösarens värde** är tröskelvärdet som måste uppfyllas innan ett meddelande skickas.
     
     I följande exempel anger uttrycket CREATE att ett meddelande skickas när antalet status koder för 404 är större än 25.
     
     ![Exempel uttryck i real tids avisering](./media/cdn-real-time-alerts/cdn-expression.png)
9. För **intervall** anger du hur ofta du vill att uttrycket ska utvärderas.
10. I list rutan **meddela om** väljer du när du vill bli meddelad när uttrycket är sant.
    
    * **Villkors start** anger att ett meddelande skickas när det angivna villkoret identifieras första gången.
    * **Villkors slut** anger att ett meddelande skickas när det angivna villkoret inte längre identifieras. Det här meddelandet kan bara aktive ras efter att vårt nätverks övervaknings system har identifierat att det angivna villkoret har inträffat.
    * **Kontinuerlig** anger att ett meddelande skickas varje stund som nätverks övervaknings systemet identifierar det angivna villkoret. Tänk på att nätverks övervaknings systemet bara kontrollerar en gång per intervall för det angivna villkoret.
    * **Villkors start och slut** anger att ett meddelande skickas första gången det angivna villkoret upptäcks och återigen när villkoret inte längre identifieras.
1. Om du vill ta emot aviseringar via e-post markerar du kryss rutan **meddela per e-post** .  
    
    ![Meddela per e-postformulär](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    I fältet **till** anger du den e-postadress som du vill att meddelanden ska skickas till. För **ämne** och **brödtext** kan du lämna standardvärdet, eller så kan du anpassa meddelandet med hjälp av listan **tillgängliga nyckelord** för att dynamiskt infoga aviserings data när meddelandet skickas.
    
    > [!NOTE]
    > Du kan testa e-postmeddelandet genom att klicka på knappen **testa aviseringen** , men endast efter att aviserings konfigurationen har sparats.
    > 
    > 
12. Om du vill att meddelanden ska publiceras på en webb server markerar du kryss rutan **meddela efter http post** .
    
    ![Meddela efter HTTP post-formulär](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    I fältet **URL** anger du den URL som du vill att http-meddelandet ska skickas till. I text rutan **sidhuvud** anger du de HTTP-huvuden som ska skickas i begäran.  För **brödtext** kan du anpassa meddelandet genom att använda listan **tillgängliga nyckelord** för att dynamiskt infoga aviserings data när meddelandet skickas.  **Sidhuvud** och **brödtext** som standard till en XML-nyttolast som liknar följande exempel:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Du kan testa HTTP post-meddelandet genom att klicka på knappen **testa meddelande** , men endast efter att aviserings konfigurationen har sparats.
    > 
    > 
13. Klicka på knappen **Spara** för att spara aviserings konfigurationen.  Om du har markerat **avisering aktive rad** i steg 5 är din avisering nu aktiv.

## <a name="next-steps"></a>Nästa steg
* Analysera [real tids statistik i Azure CDN](cdn-real-time-stats.md)
* Gräv djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användnings mönster](cdn-analyze-usage-patterns.md)

