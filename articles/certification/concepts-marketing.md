---
title: Marknadsförings egenskaper
description: En beskrivning av de olika marknadsförings fälten som samlas in i portalen och hur de kommer att visas i Azure-certifierad enhets katalog
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 45ecab18451ca0915ee3891278db47feb7469915
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969545"
---
# <a name="marketing-properties"></a>Marknadsförings egenskaper

Under processen med [att lägga till din enhets information](tutorial-02-adding-device-details.md)måste du ange marknadsförings information som ska visas i [katalogen för Azure-certifierade enheter](https://devicecatalog.azure.com). Den här informationen samlas in i Azure-certifierad enhets Portal under processen för att skicka in certifiering och används som filter parametrar i katalogen. Den här artikeln innehåller en mappning mellan fälten som samlas in i portalen och hur de visas i katalogen. När du har läst den här artikeln bör partners bättre förstå vilken information som ska tillhandahållas under certifierings processen för att bäst representera produkten i katalogen.

![Översikt över PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Produkt panel för Azure-certifierad enhets katalog

Besökare i katalogen interagerar först med enheten som en katalog produkt panel på Sök sidan. På så sätt får du en grundläggande översikt över den enhet och de certifieringar som den har tilldelats.

![Mall för produkt panel](./media/concepts-marketing/product-tile.png)

| Fält | Beskrivning                  | Var du ska lägga till i portalen                |
|---------------|-------------------------|----------------------------------|
| Enhetsnamn | Offentligt namn på din certifierade enhet         | Fliken grunder i enhets information|
| Företagsnamn| Företagets offentliga namn  | Det går inte att redigera i portalen. Extraherat från MPN konto namn |
| Produkt foto  | Bild av enheten med minsta upplösning 200p x 200p  | Marknadsförings information |
| Certifierings klassificering  | Obligatorisk Azure-certifierad enhets certifierings etikett och valfria certifierings märken  | Fliken grunder i enhets information. Måste klara testning i avsnittet Anslut & test. |

## <a name="product-description-page-information"></a>Sid information för produkt beskrivning

När en kund har klickat på enhets panelen från Sök sidan för katalogen, kommer de att gå till sidan produkt beskrivning på enheten. Det är här som den information som angavs under certifierings processen kommer att hittas.

Den övre delen av sidan produkt beskrivning visar viktiga egenskaper, varav vissa redan användes för produkt panelen.

![PDP, övre stapel](./media/concepts-marketing/pdp-top.png)

| Fält | Beskrivning                  | Var du ska lägga till i portalen                |
|---------------|-------------------------|----------------------------------|
| Enhets klass | Klassificering av form faktor och primärt syfte för din enhet ([Läs mer](./resources-glossary.md))       | Fliken grunder i enhets information|
| Enhetstyp | Klassificering av enhet baserat på implementeringens beredskap ([Läs mer](./resources-glossary.md)) | Fliken grunder i enhets information |
| Geo-tillgänglighet | Regioner som din enhet kan köpa  | Marknadsförings information |
| Operativsystem  | Operativ system som enheten stöder  | Fliken produkt information för enhets information |
| Mål branscher  | De 3 främsta branscher som din enhet är optimerad för  | Marknadsförings information |
| Produktbeskrivning  | Fritext fält där du kan skriva din marknadsförings Beskrivning av produkten. Detta kan samla in information som inte visas i portalen eller lägga till ytterligare kontext för fördelarna med att använda enheten. | Marknadsförings information|

Resten av sidan fokuserar på att visa de tekniska specifikationerna för enheten i tabell format som hjälper kunden att bättre förstå din produkt. Den information som visas överst på sidan visas också här. Resten av tabellen beskrivs i de komponenter som anges i portalen.

![Sidan PDP, nedre sida](./media/concepts-marketing/pdp-bottom.png)

| Fält | Beskrivning                  | Var du ska lägga till i portalen                |
|---------------|-------------------------|----------------------------------|
| Komponent typ | Klassificering av form faktor och primärt syfte för din enhet ([Läs mer](./resources-glossary.md))       | Produkt information om enhets information|
| Komponent namn| Namnet på komponenten som du beskriver | Produkt information om enhets information |
| Ytterligare komponent information | Ytterligare specifikationer för maskin vara som inkluderade sensorer, anslutningar, acceleratorer osv.  | Ytterligare komponent information om enhets information ([Läs mer](./how-to-using-the-components-feature.md))  |
| Enhets beroende text | Sammansluten text som beskriver de olika beroenden som produkten kräver för att ansluta till Azure ([Läs mer](./how-to-indirectly-connected-devices.md))   | Avsnittet kund riktade kommentarer på fliken beroenden för enhets information |
| Enhets beroende länk  | Länka till en certifierad enhet som den aktuella produkten kräver  | Fliken beroenden för enhets information |

## <a name="shop-links"></a>Köp länkar
Det finns både på sidan produkt-och produkt beskrivning. När du klickar på kunden öppnas ett fönster som gör det möjligt för dem att välja en distributör (du får lista upp till 5 distributörer). När du har valt det här alternativet omdirigeras kunden till den partnerbaserade URL: en.

![Bild av shops pop-up-upplevelse](./media/concepts-marketing/shop.png)

| Fält | Beskrivning                  | Var du ska lägga till i portalen                |
|---------------|-------------------------|----------------------------------|
| Namn på distributör | Namnet på distributören som säljer din produkt | Marknadsförings information|
| Hämta enhet| Länk till extern webbplats för kunden att köpa enheten (eller begära en offert från distributören). Detta kan vara detsamma som tillverkarens sida om distributören är densamma som enhets tillverkaren. Om en inköps sida inte är tillgänglig omdirigeras den till åter försäljarens sida för att kunden ska kunna kontakta dem direkt.  | URL för åter försäljare produkt sida i marknadsförings information. Om ingen inköps sida är tillgänglig får du standard åter försäljarens URL i marknadsförings detalj. |

## <a name="external-links"></a>Externa länkar
På sidan produkt beskrivning finns även länkar som navigerar till webbplatser som tillhandahålls av partner eller filer som hjälper kunden att bättre förstå produkten. De visas överst på sidan under produkt beskrivnings texten. Vilka länkar som visas varierar beroende på olika enhets typer och certifierings program.

| Länk | Beskrivning                  | Var du ska lägga till i portalen                |
|---------------|-------------------------|----------------------------------|
| Kom igång-guide * | PDF-fil med användar instruktioner för att ansluta och använda din enhet med Azure-tjänster | Lägg till guide avsnittet "kom igång" i portalen|
| Tillverkarens sida *|Länk till tillverkarens sida. Den här sidan kan vara den speciella produkt sidan för din enhet eller till företagets start sida om en marknadsförings sida inte är tillgänglig. | Tillverkarens marknadsförings sida i marknadsförings information |
| Enhetsmodell | Offentliga DTDL-modeller för IoT Plug and Play-lösningar  | Det går inte att redigera i portalen. Enhets modellen måste överföras till ([offentlig modell databas](https://aka.ms/modelrepo)  |
| Enhets käll kod | URL till enhets käll kod för enhets typer i dev kit| Fliken grunder i enhets information  |

 **Krävs för alla publicerade enheter*

## <a name="next-steps"></a>Nästa steg
Nu när du har en förståelse för hur vi använder den information som du anger under certifieringen är du nu redo att certifiera din enhet! Starta ditt certifierings projekt eller gå tillbaka till enhets informations stadiet för att lägga till din egen marknadsförings information.

- [Starta certifierings resan](./tutorial-00-selecting-your-certification.md)
- [Lägger till enhets information](./tutorial-02-adding-device-details.md)
