---
title: Vanliga frågor och svar
description: Vanliga frågor och svar om tjänsten Azure-spatiala ankare.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: b67dcf038dbe13b8c040f317bb117f3b7a7afdde
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384246"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Vanliga frågor om Azure Spatial Anchors

Azure Spatial Anchors är en hanterad molntjänst och utvecklarplattform som möjliggör rumsligt medvetna upplevelser med mixad verklighet med flera användare på HoloLens-, iOS- och Android-enheter.

Mer information finns i [Översikt över Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Vanliga frågor och svar om Azure Spatial Anchors-produkter

**F: vilka enheter stöder Azure spatiala ankare?**

**A:** Med hjälp av Azures spatiala ankare kan utvecklare bygga appar på HoloLens, på iOS-enheter med ARKit-support och på Android-enheter med ARCore-support. för iOS och Android inkluderar både telefoner och surfplattor.

**F: måste jag vara ansluten till molnet för att använda Azures ankare för spatialdata?**

**A:** För närvarande krävs en nätverks anslutning till Internet för Azure-spatiala ankare. Vi tar gärna emot dina kommentarer på vår [feedbackwebbplats](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**F: vilka är anslutnings kraven för Azure spatiala ankare?**

**A:** Avstånds ankare i Azure fungerar med Wi-Fi och mobila bredbands anslutningar.

**F: hur exakt kan Azure spatiala ankare hitta ankare?**

**A:** Många faktorer påverkar precisionen för att hitta ankare – ljus förhållanden, objekt i miljön och till och med den yta som fäst punkten är placerad på. För att avgöra om precisionen kommer att uppfylla dina behov kan du prova fästpunkterna i miljöer som liknar dem som du planerar att använda fästpunkterna i. Om du stöter på miljöer där precisionen inte uppfyller dina behov kan du läsa avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

**F: hur lång tid tar det att skapa och hitta ankare?**

**A:** Tiden som krävs för att skapa och hitta ankare är beroende av många faktorer – nätverks anslutning, enhetens bearbetning och belastning och den speciella miljön. Vi har kunder som skapar program i många branscher, till exempel tillverkning, detaljhandel och spel, som uppger att tjänsten ger en mycket bra användarupplevelse för deras scenarier.

## <a name="privacy-faq"></a>Vanliga frågor och svar om sekretess

**F: när mitt program placerar ett rums ankare någonstans, har alla appar åtkomst till det?**

**A:** Ankare isoleras av Azure-kontot. Endast appar som du ger åtkomst till ditt konto får åtkomst till fästpunkter i kontot.

**F: Hur lagrar Azure spatiala ankare data?**

**A:** Alla data lagras krypterade med en Microsoft-hanterad data krypterings nyckel och alla data lagras regionalt för var och en av resurserna.

**F: vilken information om en miljö överförs och lagras på tjänsten när du använder Azure spatiala ankare? Överförs och lagras bilder i miljön?**

**A**: när du skapar eller söker efter ankare bearbetas bilder i miljön på enheten i ett härlett format. Det här härledda formatet skickas till och lagras i tjänsten.

För att ge transparens visas nedan en bild av miljön och det härledda molnet med glesa punkter. Punktmolnet visar den geometriska representationen av den miljö som överförs och lagras i tjänsten. För varje punkt i molnet med glesa punkter överför och lagrar vi en hash av den punktens visuella egenskaper. Hashen härleds från, men innehåller inte några pixeldata.

Azure Spatial Anchors följer [villkoren i serviceavtalet för Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) och [Microsofts sekretesspolicy](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![En miljö och dess härledda utlösare för moln ](./media/sparse-point-cloud.png)
 *bild 1: en miljö och dess härledda, härledda punkt moln*

**F: finns det något sätt som jag kan skicka diagnostikinformation till Microsoft?**

**A**: Ja. Azure Spatial Anchors har ett diagnostikläge som utvecklare kan välja att använda via Azure Spatial Anchors API. Det här är användbart om du till exempel påträffar en miljö där du inte kan skapa och hitta fästpunkter på ett förutsägbart sätt. Vi kan komma att fråga om du kan skicka en diagnostikrapport som innehåller information som kan hjälpa oss att felsöka. Mer information finns i avsnittet om [loggning och diagnostik i Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Vanliga frågor och svar om tillgänglighet och prissättning

**F: tillhandahåller du ett service avtal?**

**A:** Som standard för Azure-tjänster riktar vi en tillgänglighet som är större än 99,9%. 

**F: kan jag publicera mina appar med hjälp av Azure spatiala ankare till app-butiker? Kan jag använda Azure spatiala ankare för verksamhets kritiska produktions scenarier?**

**A:** Ja, moln rums ankare är allmänt tillgängliga och har ett standard-SLA för Azure-tjänster. Vi bjuder in dig att utveckla appar för dina produktions distributioner och [dela din feedback](https://feedback.azure.com/forums/919252-azure-spatial-anchors) om produkten med oss.

**F: har du några begränsnings gränser på plats?**

**A**: Ja, vi har begränsningar för begränsning.  Vi förväntar oss inte att du uppnår dem vid typisk utveckling och testning av program. För produktionsdistributioner kan vi ge stöd till våra kunders krav för hög skala. [Kontakta oss](mailto:azuremrscontact@microsoft.com) om du vill diskutera det.

**F: i vilka regioner är tillgängliga Azure-ankare tillgängliga?**

**A:** Azures spatialdata är för närvarande tillgängligt i västra USA 2, östra USA, östra USA 2, södra centrala USA, Västeuropa, Nord Europa, Storbritannien, södra och östra Australien. Ytterligare regioner kommer att vara tillgängliga i framtiden.

Det innebär att både beräkning och lagring av den här tjänsten finns i dessa regioner. Dock finns det inga begränsningar för var klienterna kan finnas. 

**F: debiteras du för Azures spatiala ankare?**

**A:** Du hittar information om priser på vår [prissättnings sida](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Vanliga frågor och svar om teknik

**F: Hur fungerar Azure spatiala ankare?**

**A:** De olika Azure-Ankarena beror på blandade verklighet/förhöjda verklighet-spårare. Dessa spårare ser miljön med kameror och spårar enheten i med sex frihetsgrader (6DoF) när den rör sig i miljön.

Med en 6DoF-spårare som byggblock kan du med Azure Spatial Anchors ange särskilda platser av intresse i den verkliga miljön som ”fästpunkter”. Du kan till exempel använda en fästpunkt för att rendera innehåll på en specifik plats i verkligheten.

När du skapar en fästpunkt registrerar klient-SDK miljöinformation runt den punkten och skickar den till tjänsten. Om en annan enhet söker efter fästpunkten i samma utrymme överförs liknande data till tjänsten. Dessa data matchas mot miljödata som lagrats tidigare. Positionen för fästpunkten i förhållande till enheten skickas sedan tillbaka för användning i programmet.

**F: Hur integrerar Azure spatiala ankare med ARKit och ARCore på iOS och Android?**

**A:** Azures spatiala ankare utnyttjar de interna spårnings funktionerna i ARKit och ARCore. Dessutom ger våra SDK:er för iOS och Android funktioner såsom bevarande av fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att helt enkelt ansluta till tjänsten.

**F: Hur integrerar Azure spatiala ankare med HoloLens?**

**A:** Azures spatiala ankare utnyttjar de interna spårnings funktionerna i HoloLens. Vi tillhandahåller en Azure Spatial Anchors-SDK för skapande av appar på HoloLens. SDK:n integreras med inbyggda HoloLens-funktioner och tillhandahåller ytterligare funktioner. De här funktionerna omfattar tillåtelse för apputvecklare att bevara fästpunkter i en hanterad molntjänst och tillåtelse för dina appar att hitta de fästpunkterna igen genom att ansluta till tjänsten.

**F: vilka plattformar och språk stöder Azure spatiala ankare?**

**A:** Utvecklare kan bygga appar med Azures spatiala ankare med välbekanta verktyg och ramverk för sina enheter:

- Unity över HoloLens, iOS och Android
- Xamarin på iOS och Android
- Swift eller Objective-C i iOS
- Java eller Android NDK i Android
- C++/WinRT på HoloLens

Kom igång med [utveckling här](index.yml).

**F: fungerar den med Unreal?**

**A:** Stöd för Unreal kommer att beaktas i framtiden.

**F: vilka portar och protokoll använder Azure spatiala ankare?**

**A:** De spatiala Ankarena i Azure kommunicerar via TCP-port 443 med hjälp av ett krypterat protokoll. För autentisering används [Azure Active Directory](../active-directory/index.yml), som kommunicerar med https via port 443.