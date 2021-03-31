---
title: Skapa ett Internet Analyzer-test med hjälp av portalen | Microsoft Docs
description: I den här artikeln får du lära dig hur du skapar ditt första test för Internet analys.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "73501773"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Skapa ett Internet Analyzer-test med hjälp av Portal (för hands version)

Det finns två sätt att skapa en Internet Analyzer-resurs – med hjälp av Azure Portal eller med [CLI](internet-analyzer-cli.md). Det här avsnittet hjälper dig att skapa en ny Azure Internet Analyzer-resurs med hjälp av vår portal upplevelse.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga för hands versionen är tillgänglig för användning globalt; data lagring är dock begränsad till *västra USA 2* under för hands versionen.

## <a name="basics"></a>Grundläggande inställningar

1. Få till gång till Internet Analyzer Preview genom att följa **Hur gör jag för att delta i för hands versionen?** instruktioner från [vanliga frågor och svar om Azure Internet Analyzer](internet-analyzer-faq.md).
2. Klicka på **+ skapa en resurs** från start sidan i [Azure Portal](https://preview.portal.azure.com). Internet Analyzer är för närvarande endast tillgängligt från för hands versionen av Azure Portal.
3. På sidan **ny** söker du efter "Internet Analyzer" i fältet *Sök på Marketplace* .
4. Klicka på **Internet Analyzer (för hands version)**. Kontrol lera att utgivaren är *Microsoft* och att kategorin är *nätverk*.
5. På sidan för **Internet Analyzer (förhands granskning)** klickar du på **skapa** för att öppna sidan **skapa en Internet analys** .
6. Ange följande konfigurations inställningar för din Internet Analyzer-resurs:

    * **Prenumeration:** Azure-prenumerationen som är värd för den nya Internet Analyzer-resursen. **_Använd samma prenumerations-ID som används för att begära för hands versions åtkomst._**
    * **Resurs grupp:** Den Azure-resurs grupp som den nya Internet Analyzer-resursen kommer att skapas i. Om du inte har en befintlig resurs grupp kan du skapa en ny.
    * **Namn:** Namnet på den nya resurs profilen för Internet Analyzer.
    * **Region:** Den offentliga Azure-region som resursen ska skapas i. Under för hands versionen är endast *USA, väst 2* , tillgängligt.

7. När du är klar med att ange profil inställningarna klickar du på **Granska + skapa**.

## <a name="configuration"></a>Konfiguration

Du måste slutföra de grundläggande stegen för att konfigurera ett test och bädda in JavaScript-klienten. När du har skapat en profil går du till **inställningar > konfiguration** för att konfigurera ditt första test.

1. Ge testet ett namn i rutan **testa namn** .
2. Lägg till en beskrivning av testet i fältet **Beskrivning** .
3. Klicka på **Konfigurera slut punkt** – en flik kommer att visas från den högra sidan. Välj den typ av slut punkt som du vill konfigurera – en enskild Azure-region, flera Azure-regioner eller en anpassad slut punkt.

    >
    ***Förkonfigurerade slut punkter: enkla och flera kombinationer av Azure-regioner***
    * Välj en region eller uppsättning regioner från en [förkonfigurerad lista över Azure-slutpunkter](internet-analyzer-faq.md).
    * Välj sedan den typ av program eller arkitektur för innehålls leverans som du vill utvärdera.
        * Enskild Azure-region: webbplats acceleration ([Azure-frontend](https://azure.microsoft.com/services/frontdoor/)), statisk cachelagring av innehåll ([Azure CDN för Microsoft](https://azure.microsoft.com/services/cdn/)) eller ingen.
        * Flera Azure-regioner: webbplats acceleration ([Azure-front dörr](https://azure.microsoft.com/services/frontdoor/)), DNS-styrning ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Anpassade slut punkter***
    * Följ instruktionerna på sidan [Skapa anpassad slut punkt](internet-analyzer-custom-endpoint.md) .
    * Klistra in HTTPS URL-platsen för bilden med en bild punkt i portalen.
    >

4. Klicka på **Lägg** till för att lägga till slut punkten i testet.
5. Upprepa steg 1-4 för att konfigurera din andra slut punkt. Slut punkten B mäts alltid i förhållande till slut punkten A – när du konfigurerar slut punkter bör du fundera över vilken slut punkt som ska vara din test kontroll.
6. Spara testet genom att klicka på knappen **Spara** . När du har sparat ett test kan du inte längre redigera slut punkterna för ett visst test.
7. Välj de test (er) som du vill starta och klicka på **Starta test**. Detta ändrar **_status_*_ för dina tester till _*_körs_**. Du kan börja testa när som helst, men JavaScript-klienten måste vara inbäddad för att testet ska börja samla in mått.
8. Lägg till fler tester när som helst. Observera att den unika JavaScript-klienten inte skapas förrän ett test har skapats.

## <a name="embed-client"></a>Bädda in klient

För att kunna påbörja ett test måste JavaScript-klienten bäddas in i ditt webb program. När du har konfigurerat minst ett test klickar du på **Granska + skapa**, går till **Inställningar > konfiguration** och kopierar JavaScript-klienten. Du hittar mer information på sidan [bädda in Internet Analyzer-klienten](internet-analyzer-embed-client.md) .  

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)
* Lär dig mer om att bädda in [Internet Analyzer-klienten](internet-analyzer-embed-client.md) och skapa en [anpassad slut punkt](internet-analyzer-custom-endpoint.md).
