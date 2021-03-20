---
title: Erbjudande för lista över bästa praxis – Microsoft Commercial Marketplace
description: Lär dig mer om metod tips för att gå till marknaden för din Microsoft AppSource och Azure Marketplace-erbjudanden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94492097"
---
# <a name="offer-listing-best-practices"></a>Metodtips för erbjudandelistor

Den här artikeln innehåller förslag på hur du kan skapa och engagera Microsofts erbjudanden om kommersiella marknads platser. Följande tabeller beskriver metod tips för att fylla i erbjudande information i Partner Center. En analys av hur dina erbjudanden fungerar finns på [instrument panelen för Marketplace-insikter](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) i Partner Center. 

## <a name="online-store-offer-details"></a>Information om erbjudanden om onlinebutik

| Inställning | Bästa praxis |
|:--- |:--- |  
| Erbjudandets namn | För appar anger du en tydlig rubrik som innehåller sökord för att hjälpa kunderna att identifiera ditt erbjudande. <br> <br> För konsult tjänster följer du detta format: [erbjudandets namn: [duration] [erbjudande typ] (till exempel contoso: 2-veckors implementering) |
| Beskrivning av erbjudande | Ange en tydlig beskrivning som beskriver ditt erbjudandes värde i de första meningarna.  Tänk på att dessa meningar kan användas i sökmotorns resultat. Kärn komponenterna i dina värde förslag bör innefatta: <ul> <li>Beskrivning av produkten eller lösningen. </li> <li> Användar person som vill dra nytta av produkten eller lösningen. </li> <li> Kunden behöver eller smärta produkt-eller lösnings adresser. </li> </ul> <br> Använd terminologi för bransch standard eller förmånsberättigad formulering när det är möjligt.  Förlita dig inte på funktioner och funktioner för att sälja produkten.  Fokusera i stället på det värde som du levererar. <br> <br> För konsult tjänst listor, anger du tydligt den professionella tjänst som du tillhandahåller. |

> [!IMPORTANT]
> Se till att ditt erbjudande namn och din erbjudande Beskrivning följer **[Microsofts rikt linjer för varumärke och varumärke](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** och andra relevanta, produktspecifika rikt linjer när du refererar till Microsofts varumärken och namnen på Microsofts program varor, produkter och tjänster.

## <a name="online-store-listing-details"></a>Information om onlinebutik i onlinebutik

Kategorier och branscher för en annan onlinebutik kan tillämpas på olika typer av erbjudanden.

| Onlinebutik | Kategorier <br>per onlinebutik | Kategorier <br>per onlinebutik | Branscher <br> för AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Erbjudandetyp**   |  **Azure Marketplace**  | **AppSource**  |
| Azure App | X | |
| Container | X | |
| Rådgivningstjänster | | | X |
| Dynamics 365 kund engagemang & Power Platform | | X | X |
| Dynamics 365 ekonomi & hantering av leverans kedjan | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge moduler | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Virtuell Azure-dator |  X |    |

### <a name="categories"></a>Kategorier

Microsoft AppSource och Azure Marketplace är onlinebutiker som erbjuder olika lösnings typer. Azure Marketplace erbjuder IT-lösningar som bygger på eller för Azure.  Microsoft AppSource erbjuder affärs lösningar som bransch SaaS program, Dynamics 365-tillägg, Microsoft 365-tillägg och Power Platform-appar.

Kategorier och under Kategorier mappas till varje onlinebutik utifrån lösnings typen. Ditt erbjudande publiceras till Microsoft AppSource eller Azure Marketplace beroende på erbjudande typen, transaktions funktioner för val av erbjudande och kategori/under kategori. 

Välj kategorier och under kategorier som bäst överensstämmer med din lösnings typ. Du kan välja:

* Upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
* Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori har valts kan du fortfarande bara identifiera i den valda kategorin.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>VIKTIGT: SaaS erbjuder och Microsoft 365 tillägg

Mer information om hur Transact-funktioner kan påverka hur ditt erbjudande kan visas och köpas av Marketplace-kunder finns i avsnittet om att [agera i den kommersiella marknads platsen](marketplace-commercial-transaction-capabilities-and-considerations.md) . För SaaS-erbjudanden bestämmer erbjudandets transaktions kapacitet samt kategori valet det onlinebutik där ditt erbjudande ska publiceras.


| SaaS-erbjudande    | SaaS-erbjudande   | SaaS-erbjudande  | SaaS-erbjudande   | SaaS-erbjudande   | SaaS-erbjudande   | SaaS-erbjudande    | Tillämpligt onlinebutik| Tillämpligt onlinebutik |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Avgiftsbelagd fakturering | Microsoft 365 tillägg | Kontakta mig | Transact (minst 1 plan) | Privat plan | Endast offentlig plan | Offentliga & privata planer | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1, 2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. Beroende på kategori/under kategori och bransch val
2. Erbjudanden med privata planer publiceras i Azure Portal


### <a name="industries"></a>Branscher

Bransch val gäller endast för erbjudanden som publicerats på AppSource och konsult tjänster som publicerats på Azure Marketplace.  Välj branscher och/eller lodräta om ditt erbjudande uppfyller branschspecifika behov, och anropar branschspecifika funktioner i din erbjudande beskrivning. Du kan välja upp till två (2) branscher och två (2) lodräta per bransch valt.

>[!Note]
>För konsult tjänst erbjudanden i Azure Marketplace finns det inga branscher lodrätt.

| **Branscher** |  **Lodrätt** |
| :------------------- | :----------------|
| **Jordbruk** | |
| **Arkitektur & konstruktion** | |
| **Fordon** | |
| **Distribution** | Maskiner <br> Paket leverans av paket & |  
| **Utbildning** | Högre utbildning <br> Primär & sekundär edu/K-12 <br> Bibliotek & Museums |
| **Ekonomiska tjänster** | Bank & kapital marknader <br> Försäkringsbokf | 
| **Myndigheter** |  Försvars & intelligens <br> Civila myndigheter <br> Allmän säkerhets & rättvisa |
| **Sjukvård** | Hälso betalare <br> Hälso leverantör <br> Pharmaceuticals | 
| **Service & Travel** | Res & transport <br> Hotell & fritid <br> Restauranger & mat tjänster | 
| **& resurser för produktion** | Kemisk & Agrochemical <br> Diskret tillverkning <br> Energi | 
| **Media & kommunikation** | Media & underhållning <br> Telekommunikation | 
| **Andra branscher i offentlig sektor** | Skogsbruk & fiske <br> Ideell verksamhet | 
| **Professionella tjänster** | Partner Professional-tjänster <br> Juridisk information | 
| **Fastigheter** | |

Bransch för endast Microsoft AppSource:

| **Industri** |  **Lodrätt** |
| :------------------- | :----------------|
| **Butiks & konsument varor** | Detaljist <br> Konsument varor |

### <a name="applicable-products"></a>Tillämpliga produkter

Välj de tillämpliga produkter som appen fungerar med för erbjudandet att visas under valda produkter i AppSource.

### <a name="search-keywords"></a>Sök efter nyckelord

Nyckelord kan hjälpa kunder att hitta ditt erbjudande när de söker. Identifiera de bästa Sök nyckelorden för ditt erbjudande, Lägg till dem i din erbjudande Sammanfattning och beskrivning samt i avsnittet nyckelord i avsnittet information om erbjudanden.

## <a name="online-store-marketing-details"></a>Marknadsförings information om onlinebutik
| Inställning | Bästa praxis |
|:--- |:--- |  
| Erbjudande logo typ (PNG-format, från 216 × 216 till 350 x 350 BPT): information om App-sidan | Utforma och optimera din logo typ för ett digitalt medium:<br>Ladda upp logo typen i PNG-format till sidan med information om appar i ditt erbjudande. Partner Center ändrar storlek till de storlekar som krävs för logo typen. |
| Erbjudande logo typ (PNG-format, 48 × 48 bild punkter): Sök sida | Partner Center kommer att generera logo typen från den stora logo typen som du överförde. Du kan också ersätta detta med en annan avbildning senare. |
| "Läs mer"-dokument | Ta med stöd för försäljnings-och marknadsförings till gångar under "Läs mer", några exempel:<ul><li>Fakta blad</li><li> broschyrer</li><li>Check listor eller</li><li> PowerPoint-presentationer</li></ul><br>Spara alla filer i PDF-format. Ditt mål är att utbilda kunder, inte sälja till dem.<br><br>Lägg till en länk till din app landnings sida till alla dina dokument och Lägg till URL-parametrar som hjälper dig att spåra besökare och försök. |
| Videor: AppSource, konsult tjänster och SaaS erbjuder bara | De starkaste videorna förmedlar värdet för ditt erbjudande i form av information:<ul> <li> Gör din kund, inte ditt företag, riktig text. </li> <li> Din video bör åtgärda de huvudsakliga utmaningarna och målen för mål kunden. </li> <li> Rekommenderad längd: 60-90 sekunder.</li> <li> Inkludera nyckelord för nyckelord som använder namnet på videor. </li> <li> Överväg att lägga till ytterligare videor, till exempel en instruktion, komma igång eller kund åsikter. </li> </ul> |
| Skärm bilder (1280 &nbsp; &times; &nbsp; 720) | Lägg till upp till fem skärm bilder:<br>Inkludera ord för nyckels ökning i fil namnen. |

## <a name="link-to-your-offer-page-from-your-website"></a>Länka till din erbjudande sida från din webbplats

När du länkar från AppSource-eller Azure Marketplace-märket på din webbplats till din lista på den kommersiella marknads platsen kan du använda stark analys och rapportering genom att inkludera följande frågeparametrar i slutet av URL: en:
* **src**: ta med källan från vilken trafiken dirigeras till AppSource (till exempel webbplats, LinkedIn eller Facebook).
* **mktcmpid**: ditt marknadsförings kampanj-ID, som kan innehålla upp till 16 tecken i valfri kombination av bokstäver, siffror, under streck och bindestreck (till exempel *blogpost_12*).

Följande exempel-URL innehåller båda ovanstående frågeparametrar: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Genom att lägga till parametrarna till din AppSource-URL kan du granska effektiviteten i din kampanj i analys instrument panelen i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om dina [kommersiella marknads förmåner](./gtm-your-marketplace-benefits.md).

Logga in på [partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa och konfigurera ditt erbjudande.
