---
title: Hantera bandbredds-mallar för StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du hanterar StorSimple för bandbredd, vilket gör att du kan kontrol lera bandbredds användningen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005902"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Använd tjänsten StorSimple Enhetshanteraren för att hantera mallar för StorSimple-bandbredd

## <a name="overview"></a>Översikt

Med hjälp av Bandwidth templates kan du konfigurera nätverks bandbredds användning över flera tids-och dags scheman för att skikta data från StorSimple-enheten till molnet.

Med scheman för bandbredds begränsning kan du:

* Ange anpassade bandbredds scheman beroende på arbets Belastningens nätverks användning.
* Centralisera hanteringen och återanvänd scheman på flera enheter på ett enkelt och smidigt sätt.

> [!NOTE]
> Den här funktionen är endast tillgänglig för StorSimple fysiska enheter (modellerna 8100 och 8600) och inte för StorSimple-molnappar (modellerna 8010 och 8020).


## <a name="the-bandwidth-templates-blade"></a>Bladet mallar för bandbredd

Bladet **Bandwidth templates** har alla Bandwidth-mallar för din tjänst i tabell format och innehåller följande information:

* **Namn** – ett unikt namn som tilldelats bandbredds mal len när den skapades.
* **Schema** – antalet scheman som ingår i en specifik bandbredds mal len.
* **Används av** – antalet volymer som använder Bandwidth-mallarna.

Du kan också hitta ytterligare information som hjälper dig att konfigurera Bandwidth-mallar i:

* [Frågor och svar om bandbredds mallar](#questions-and-answers-about-bandwidth-templates)
* [Metod tips för mallar för bandbredd](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Lägg till en bandbredds mall

Utför följande steg för att skapa en ny bandbredds mall.

#### <a name="to-add-a-bandwidth-template"></a>Lägga till en bandbredds mall

1. Gå till din StorSimple Enhetshanteraren-tjänst, klicka på **mallar för bandbredd** och klicka sedan på **+ Lägg till bandbredds mal len**.

    ![Klicka på + Lägg till bandbredd mall](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Utför följande steg på bladet **Lägg till bandbredd** :
   
    1. Ange ett unikt namn för bandbredds mal len.
    2. Definiera ett schema för bandbredd. Så här skapar du ett schema:
   
        1. Välj de vecko **dagar** som schemat har kon figurer ATS för i list rutan. Du kan välja flera dagar.        
        
        2. Ange en **Start tid** i formatet _hh: mm_ . Detta är när schemat ska börja.

        3. Ange en **slut tid** i formatet _hh: mm_ . Detta är när schemat kommer att stoppas.
      
           > [!NOTE]
           > Överlappande scheman är inte tillåtna. Om start-och slut tiderna leder till ett överlappande schema, visas ett fel meddelande om detta.

        4. Ange **bandbredds frekvensen**. Detta är bandbredden i megabit per sekund (Mbit/s) som används av din StorSimple-enhet i åtgärder som involverar molnet (både uppladdning och nedladdning). Ange ett tal mellan 1 och 1 000 för det här fältet.

            ![Definiera schema för bandbredd](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Upprepa stegen ovan för att definiera flera scheman för mallen tills du är färdig.

        5. Klicka på **Lägg till** för att börja skapa en bandbredds mall. Den skapade mallen läggs till i listan över bandbredds mal len.
      

## <a name="edit-a-bandwidth-template"></a>Redigera en bandbredds mall

Utför följande steg för att redigera en bandbredds mall.

### <a name="to-edit-a-bandwidth-template"></a>Redigera en bandbredds mall

1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka på **mallar för bandbredd**.
2. Välj den mall som du vill ta bort i listan med mallar för bandbredd. Högerklicka och välj **ta bort** på snabb menyn.
3. När du uppmanas att bekräfta klickar du på **OK**. Detta bör ta bort bandbredds mal len. 
4. Listan över uppdateringar av Bandwidth-mallar som visar borttagningen.

> [!NOTE]
> Du kan inte spara dina ändringar om det redigerade schemat överlappar ett befintligt schema i bandbredds mal len som du ändrar.

## <a name="delete-a-bandwidth-template"></a>Ta bort en bandbredds mall

Utför följande steg för att ta bort en bandbredds mall.

#### <a name="to-delete-a-bandwidth-template"></a>Ta bort en bandbredds mall

1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka på **mallar för bandbredd**.
2. Välj den mall som du vill ta bort i listan med mallar för bandbredd. Högerklicka och välj Ta bort på snabb menyn.
3. När du uppmanas att bekräfta klickar du på **OK**. Detta bör ta bort bandbredds mal len.
4. Listan över uppdateringar av Bandwidth-mallar som visar borttagningen.

Om mallen används av en eller flera volymer, kommer du inte att kunna ta bort den. Ett fel meddelande visas som anger att mallen används. Ett fel meddelande dialog ruta visas som uppmanar dig att alla referenser till mallen ska tas bort.

Du kan ta bort alla referenser till mallen genom att öppna sidan **volym behållare** och ändra de volym behållare som använder mallen så att de använder en annan mall eller använder en anpassad eller obegränsad bandbredds inställning. När alla referenser har tagits bort kan du ta bort mallen.

## <a name="use-a-default-bandwidth-template"></a>Använd en mall för standard bandbredd

En standardmall för bandbredd anges och används av volym behållare som standard för att genomdriva bandbredds kontroller vid åtkomst till molnet. Standard mal len fungerar också som en färdig referens för användare som skapar egna mallar. Information om den här standard mal len är:

* **Namn** – obegränsade nätter och helger
* **Schema** – ett enda schema från måndag till fredag som tillämpar en bandbredd på 1 Mbit/s mellan 8 och 5 PM-enhets tid. Bandbredden är inställd på obegränsad för resten av veckan.

Standard mal len kan redige ras. Användningen av den här mallen (inklusive redigerade versioner) spåras.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Skapa en mall för bandbredd som startar vid en viss tidpunkt

Följ den här proceduren om du vill skapa ett schema som startar vid en angiven tidpunkt och körs hela dagen. I exemplet börjar schemat kl. 9 i morgon och körs tills kl. 9 är nästa morgon. Det är viktigt att Observera att start-och slut tiderna för ett angivet schema måste båda ingå i samma 24-timmarsformat och kan inte omfatta flera dagar. Om du behöver skapa mallar för bandbredd som sträcker sig över flera dagar måste du använda flera scheman (som visas i exemplet).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Skapa en bandbredds mal len för hela dagar

1. Skapa ett schema som börjar kl. 9 i morgon och körs till midnatt.
2. Lägg till ett annat schema. Konfigurera det andra schemat så att det körs från midnatt till kl. 9 i morgon.
3. Spara bandbredds mal len.

Det sammansatta schemat startar sedan vid en tidpunkt då du väljer och kör hela dagen.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Frågor och svar om bandbredds mallar

**F**. Vad händer med bandbredds kontrollerna när du är mellan scheman? (Ett schema har avslut ATS och ett annat har ännu inte startats.)

**A**. I sådana fall används inga bandbredds kontroller. Det innebär att enheten kan använda obegränsad bandbredd när data skiktas i molnet.

**F**. Kan du ändra bandbredds-mallar på en offline-enhet?

**A**. Du kan inte ändra bandbredds mal rutorna på volym behållare om motsvarande enhet är offline.

**F**. Kan du redigera en bandbredds mal len som är kopplad till en volym behållare när de associerade volymerna är offline?

**A**. Du kan ändra en bandbredds mal len som är associerad med en volym behållare vars volymer är offline. Observera att när volymer är offline kommer inga data att flyttas från enheten till molnet.

**F**. Kan du ta bort en standard mall?

**A**. Även om du kan ta bort en standardmall är det ingen bra idé att göra det. Användningen av en standardmall, inklusive redigerade versioner, spåras. Spårnings data analyseras och under tiden används för att förbättra standard mal len.

**F**. Hur avgör du om du behöver ändra dina bandbredds mallar?

**A**. Ett av de tecken du behöver för att ändra bandbredds malerna är när du börjar se att nätverket saktas ned eller strypningar flera gånger under en dag. I så fall kan du övervaka lagrings-och användnings nätverket genom att titta på diagram för I/O-prestanda och nätverks data flöde.

Identifiera den tid på dygnet och de volym behållare där nätverks Flask halsen inträffar i data flödes data. Om detta inträffar när data på nivå av molnet (hämtar den här informationen från I/O-prestanda för alla volym behållare för enhet till moln) måste du ändra de bandbredds mallar som är kopplade till dina volym behållare.

När de ändrade mallarna används måste du övervaka nätverket igen under en avsevärd fördröjning. Om dessa fortfarande finns måste du gå tillbaka till bandbredds-mallarna.

**F**. Vad händer om flera volym behållare på enheten har scheman som överlappar varandra men olika gränser gäller för var och en?

**A**. Vi antar att du har en enhet med 3 Volym behållare. De scheman som är kopplade till dessa behållare är helt överlappande. För var och en av dessa behållare är de bandbredds gränser som används 5, 10 respektive 15 Mbps. När I/O sker på alla dessa behållare på samma gång, kan minst 3 bandbredds gränser tillämpas: i det här fallet är 5 Mbit/s som dessa utgående I/O-begäranden delar samma kö.

## <a name="best-practices-for-bandwidth-templates"></a>Metod tips för mallar för bandbredd

Följ dessa rekommendationer för din StorSimple-enhet:

* Konfigurera bandbredds-mallar på enheten för att möjliggöra varierande begränsning av nätverks flödet av enheten vid olika tidpunkter på dagen. De här mallarna för bandbredd när de används med säkerhets kopierings scheman kan effektivt utnyttja ytterligare nätverks bandbredd för moln åtgärder under låg belastnings tider.
* Beräkna den faktiska bandbredd som krävs för en viss distribution baserat på distributionens storlek och det nödvändiga målet för återställnings tid (RTO).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

