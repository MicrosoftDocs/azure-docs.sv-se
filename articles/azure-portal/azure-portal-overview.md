---
title: Översikt över Azure Portal
description: Azure Portal är ett grafiskt användar gränssnitt som du kan använda för att hantera dina Azure-tjänster. Lär dig hur du navigerar och söker efter resurser i Azure Portal.
keywords: portal
ms.date: 03/12/2021
ms.topic: overview
ms.openlocfilehash: 8f61934e6af150c18d1331c2b28e2686641967d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564002"
---
# <a name="azure-portal-overview"></a>Översikt över Azure Portal

Den här artikeln beskriver Azure Portal, identifierar portalens sid element och hjälper dig att bekanta dig med Azure Portal hanterings upplevelsen.

## <a name="what-is-the-azure-portal"></a>Vad är Azure-portalen?

Azure-portalen är en webbaserad, enhetlig konsol som ger ett alternativ till kommandoradsverktyg. I Azure-portalen kan du hantera Azure-prenumerationen med hjälp av ett grafiskt användargränssnitt. Du kan skapa, hantera och övervaka allt från enkla webbappar till komplexa molndistributioner. Skapa anpassade instrumentpaneler för en ordnad vy med resurser. Konfigurera hjälpmedelsalternativ för en optimal upplevelse.

Azure-portalen är utformad för återhämtning och kontinuerlig tillgänglighet. Den har en förekomst i varje Azure-datacenter. Den här konfigurationen gör det Azure Portal flexibelt för enskilda Data Center haverier och undviker att nätverket blir långsamt genom att de är nära användare. Azure-portalen uppdateras kontinuerligt och kräver inte någon stilleståndstid vid underhållsåtgärder.

## <a name="azure-portal-menu"></a>Azure Portal-menyn

Du kan välja standard läget för Portal-menyn. Den kan dockas eller fungera som en utfällbar panel.

När Portal menyn är i utfällt läge är den dold tills du behöver den. Välj Meny ikonen för att öppna eller stänga menyn.

![Azure Portal-menyn i utfällt läge](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Om du väljer dockat läge för Portal-menyn visas det alltid. Du kan komprimera menyn för att få mer arbets yta.

![Azure Portal-menyn i dockat läge](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure Home

Som en ny prenumerant på Azure-tjänster är det första du ser när du har [loggat](https://portal.azure.com) in på portalen **Azure Home**. Den här sidan sammanställer resurser som hjälper dig att få ut mesta möjliga av din Azure-prenumeration. Vi har inkluderat länkar till kostnads fria onlinekurser, dokumentation, kärn tjänster och användbara webbplatser för att hålla aktuella och hantera ändringar i din organisation. För snabb och enkel åtkomst till arbete som pågår, visar vi också en lista över dina senast besökta resurser. Du kan inte anpassa den här sidan, men du kan välja om du vill se **Azures start** -eller **Azure-instrumentpanelen** som standardvy. Första gången du loggar in visas en uppfråga längst upp på sidan där du kan spara dina inställningar.

![Skärm bild som visar var du sparar inställningarna.](./media/azure-portal-overview/azure-portal-default-view.png)

Både Azure Portal-menyn och vyn Azure-standardvy kan ändras i **Portal inställningar**. Om du ändrar ditt val tillämpas ändringen omedelbart.

![Skärm bild som visar standard visnings väljaren](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure-instrumentpanel

Instrument paneler ger en fokuserad vy över de resurser i din prenumeration som är viktigast för dig. Vi har fått en standard instrument panel som hjälper dig att komma igång. Du kan anpassa den här instrument panelen för att se till att de resurser som du använder ofta används i en enda vy. Alla ändringar du gör i standardvyn påverkar bara din upplevelse. Du kan dock skapa ytterligare instrument paneler för din egen användning eller publicera dina anpassade instrument paneler och dela dem med andra användare i din organisation. Mer information finns i [skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Komma runt portalen

Det är bra att förstå den grundläggande portalens layout och hur du interagerar med den. Här presenterar vi komponenterna i användar gränssnittet och några av terminologin som vi använder för att ge instruktioner. En mer detaljerad genom gång av portalen finns i kurs lektionen [navigera i portalen](/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure Portal-menyn och sidhuvudet är globala element som alltid är tillgängliga. De här permanenta funktionerna är "Shell" för användar gränssnittet som är kopplat till varje enskild tjänst eller funktion och rubriken ger till gång till globala kontroller. Konfigurations sidan (kallas ibland "blad") för en resurs kan också ha en resurs meny som hjälper dig att flytta mellan funktioner.

Figuren nedan etiketter de grundläggande elementen i Azure Portal, som beskrivs i följande tabell.

![Skärm bild som visar vyn över hel skärms portalen och nyckel till GRÄNSSNITTs element](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Skärm bild som visar den expanderade Portal menyn](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Nyckel|Beskrivning
|:---:|---|
|1|Sidhuvud. Visas överst på varje Portal sida och innehåller globala element.|
|2| Global sökning. Använd Sök fältet för att snabbt hitta en speciell resurs, tjänst eller dokumentation.|
|3|Globala kontroller. Precis som alla globala element behålls dessa funktioner i portalen och inkluderar: Cloud Shell, prenumerations filter, meddelanden, Portal inställningar, hjälp och support och skicka oss feedback.|
|4|Ditt konto. Visa information om ditt konto, växla kataloger, logga ut eller logga in med ett annat konto.|
|5|Portal-menyn. Portal-menyn är ett globalt element som hjälper dig att navigera mellan tjänster. Detta kallas ibland för sid panelen, och det går att ändra portalens meny läge i **Portal inställningarna**.|
|6|Resurs menyn. Många tjänster innehåller en resurs meny som hjälper dig att hantera tjänsten. Det kan hända att det här elementet visas som det vänstra fönstret.|
|7|Kommando fält. Kontrollerna i kommando fältet är sammanhangsbaserade för ditt aktuella fokus.|
|8|Arbets fönster.  Visar information om den resurs som för närvarande är i fokus.|
|9|Navigering. Du kan använda länkarna för dynamiska länkar för att flytta tillbaka en nivå i ditt arbets flöde.|
|10|Huvud kontroll för att skapa en ny resurs i den aktuella prenumerationen. Visa eller öppna Portal-menyn för att hitta **+ skapa en resurs**. Sök efter eller bläddra i Azure Marketplace efter den resurs typ som du vill skapa.|
|11|Listan Favoriter. Se [Lägg till, ta bort och sortera favoriter](../azure-portal/azure-portal-add-remove-sort-favorites.md) för att lära dig hur du anpassar listan.|

## <a name="get-started-with-services"></a>Kom igång med tjänster

Om du är en ny prenumerant måste du skapa en resurs innan det finns något att hantera. Välj **+ skapa en resurs** för att visa de tjänster som är tillgängliga på Azure Marketplace. Du hittar hundratals program och tjänster från många leverantörer här, allt certifierat för att köras på Azure.

Vi fyllde i dina favoriter i sido panelen med länkar till vanliga tjänster.  Om du vill visa alla tillgängliga tjänster väljer du **alla tjänster** från sido panelen.

> [!TIP]
> Det snabbaste sättet att hitta en resurs, tjänst eller dokumentation är att använda *Sök* i den globala rubriken. Använd länkarna för dynamiska länkar om du vill gå tillbaka till föregående sidor.
>
Titta på den här videon om du vill ha en demonstration om hur du använder global sökning i Azure Portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Så här använder du global sökning i Azure Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Nästa steg

* Läs mer om var du kan köra Azure Portal i [webbläsare och enheter som stöds](../azure-portal/azure-portal-supported-browsers-devices.md)
* Håll dig uppdaterad när du är igång med [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Publicera och konfigurera din moln miljö med [Azure snabb starts Center](../azure-portal/azure-portal-quickstart-center.md)