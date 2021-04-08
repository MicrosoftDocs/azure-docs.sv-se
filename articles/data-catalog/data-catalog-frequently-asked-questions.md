---
title: Vanliga frågor och svar om Azure Data Catalog
description: Vanliga frågor om Azure Data Catalog, inklusive funktioner för identifiering av data källor, anteckningar och hantering.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674690"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Den här artikeln innehåller svar på vanliga frågor som rör tjänsten Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Vad är Azure Data Catalog?
Data Catalog är en fullständigt hanterad tjänst som finns i Microsoft Azure, som fungerar som ett registrerings-och identifierings system för företags data källor. Med Data Catalog kan alla användare, från analytiker till data experter och utvecklare, registrera, identifiera, förstå och använda data källor.

## <a name="what-customer-challenges-does-it-solve"></a>Vilka kund utmaningar löser det?
Data Catalog behandlar utmaningarna med identifiering av data källor och "mörka data" så att användarna kan identifiera och förstå företags data källor.

## <a name="what-are-its-target-audiences"></a>Vad är mål grupper?
Data Catalog är utformad för tekniska och icke-tekniska användare, inklusive:

* Data utvecklare och BI-och analys tekniker: personer som ansvarar för att producera data och analys innehåll som andra kan använda.
* Data behållare: personer som har kunskap om data, vad det innebär och hur det är avsett att användas.
* Data förbrukare: personer som behöver kunna upptäcka, förstå och ansluta till de data som de behöver för att utföra sitt arbete med hjälp av det verktyg som du väljer.
* Centrala IT: personer som behöver göra hundratals data källor synliga för företags användare och som behöver upprätthålla tillsyn över hur data används och av vem.

## <a name="what-is-its-availability-by-region"></a>Vad är dess tillgänglighet per region?
Data Catalog-tjänster är för närvarande tillgängliga i följande data Center:

* USA, västra
* East US
* Europa, västra
* Europa, norra
* Australien, östra
* Sydostasien

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Vilka är gränserna för antalet data till gångar?
Den kostnads fria versionen av Data Catalog är begränsad till 5 000 registrerade data till gångar.

Standard versionen av Data Catalog har stöd för upp till 100 000 registrerade data till gångar.

Alla objekt som är registrerade i Data Catalog, till exempel tabeller, vyer, filer och rapporter, räknas som en data till gång.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Vilka data källor och resurs typer som stöds?
En lista över data källor som stöds för närvarande finns i [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hur gör jag för att begär support för en annan data Källa?
Om du vill skicka funktions förfrågningar och annan feedback går du till [Data Catalog i Azure feedback-forumet](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Varför finns det redan en fel *katalog* när jag försöker skapa en ny katalog?

När du köper Office 365 E5 med Power BI Pro licens skapar Microsoft en standard katalog i prenumerationens region automatiskt. Den här katalogen använder den kostnads fria SKU: n. Användar licensen för Office 365/Power BI hanteras på administrations sidan. 

Den här typen av data katalog har dock inget **Administratörs alternativ** och visas inte i **Azure Portal**. Du kan inte ta bort den här typen av data katalog. På samma sätt har du inte behörighet att byta namn på data katalogen och du kan inte flytta den till en annan region. 

Användar konton som tilldelas en Power BI Pro licens automatiskt har åtkomst till Data Catalog på grund av licens avtal när de registrerade sig för Office 365 E5 med Power BI Pro-licensen. Den här typen av användare har fullständig åtkomst till data katalog till gångar utan administratörs behörighet. Den typen av användare ingår *inte* i **katalogens användar** roll i Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hur gör jag för att kom igång med Data Catalog?
Det bästa sättet att komma igång med är att gå till [komma igång med Data Catalog](data-catalog-get-started.md). Den här artikeln är en slut punkt till slut punkt – en översikt över funktionerna i tjänsten.

## <a name="how-do-i-register-my-data"></a>Hur gör jag för att registrera mina data?
Så här registrerar du data i Data Catalog:
1. I Azure Data Catalog Portal, i **publicerings** avsnittet, startar du Azure Data Catalog registrerings verktyget. 
2. Logga in med samma autentiseringsuppgifter som du använder för att få åtkomst till Data Catalog portalen i Data Catalog registrerings verktyget för data källor.
3. Välj data källan och de speciella till gångar som du vill registrera.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Vilka egenskaper extraheras för data till gångar som har registrerats?
De angivna egenskaperna skiljer sig från data källan till data källan, men i allmänhet extraherar Data Catalog Publishing Service följande information:

* Till gångs namn
* Tillgångstyp
* Till gångs Beskrivning
* Attribut/kolumn namn
* Attribut/kolumn data typer
* Beskrivning av attribut/kolumn

> [!IMPORTANT]
> Att registrera data till gångar med Data Catalog flyttar eller kopierar inte dina data till molnet. När du registrerar till gångar från en data källa kopieras till gångens metadata till Azure, men data finns kvar på den befintliga data käll platsen. Undantaget till den här regeln är om du väljer att ladda upp för hands poster eller en data profil när du registrerar till gångarna. När du inkluderar en förhands granskning kopieras upp till 20 poster från varje till gång och lagras som en ögonblicks bild i Data Catalog. När du inkluderar en data profil beräknas sammanställd information och tas med i metadata som lagras i katalogen. Sammanställd information kan innehålla tabell storlek, procent andelen null-värden per kolumn, eller minimi-, max-och genomsnitts värden för kolumner. 
>
>

> [!NOTE]
> För data källor som SQL Server Analysis Services som har en första klass **beskrivnings** egenskap, extraherar verktyget för registrering Data Catalog av data källor det egenskap svärdet. För den *lokala* SQL Server Relations databaser som saknar en första klass **beskrivnings** egenskap extraherar verktyget Data Catalog data källans registrerings verktyg värdet från den **ms_description** utökade egenskapen för objekt och kolumner. Den här egenskapen stöds inte för SQL Azure. Mer information finns i [använda utökade egenskaper på databas objekt](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hur lång tid tar det innan nyligen registrerade till gångar visas i katalogen?
När du har registrerat till gångar med Data Catalog kan det finnas en period på 5 till 10 sekunder innan de visas i Data Catalog portalen.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hur gör jag för att kommentera och utöka metadata för mina registrerade data till gångar?
Det enklaste sättet att tillhandahålla metadata för registrerade till gångar är att välja till gången i Data Catalog portalen och sedan ange värdena i fönstret Egenskaper eller schema fönstret för det valda objektet.

Du kan också ange vissa metadata, till exempel experter och taggar under registrerings processen. Värdena som du anger i Data Catalog Publishing-tjänsten gäller för alla till gångar som registreras vid denna tidpunkt. Om du vill visa de nyligen registrerade objekten i portalen för ytterligare anteckning väljer du knappen **Visa Portal** på den sista skärmen i Data Catalog registrerings verktyget för data källor.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hur gör jag för att ta bort mina registrerade data objekt?
Du kan ta bort ett objekt från Data Catalog genom att markera objektet i portalen och sedan klicka på knappen **ta bort** . Att ta bort objektet tar bort dess metadata från Data Catalog, men påverkar inte den underliggande data källan.

## <a name="what-is-an-expert"></a>Vad är en expert?
En expert är en person som har ett informerat perspektiv om ett data objekt. Ett objekt kan ha flera experter. En expert behöver inte vara "ägare" för ett objekt, men är bara någon som känner till hur data kan och ska användas.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hur gör jag för att dela information med Data Catalog-teamet om jag stöter på problem?
Om du vill rapportera problem, dela information och ställa frågor, går du till [Azure Data Catalog-forumet](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Fungerar katalogen med en annan data källa som jag är intresse rad av?
Vi arbetar aktivt med att lägga till fler data källor till Data Catalog. Om du vill se en speciell data källa som stöds, föreslår du (eller röstar support om det redan har föreslagits) genom att gå till [Data Catalog i Azure feedback-forumet](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Vilka behörigheter behöver jag för att registrera till gångar med Data Catalog?
Om du vill köra registrerings verktyget för Data Catalog måste du ha behörighet till data källan som gör att du kan läsa metadata från källan. Om du också vill ha en förhands granskning måste du ha behörighet som gör att du kan läsa data från de objekt som registreras.

Data Catalog kan också katalog administratörer begränsa vilka användare och grupper som kan lägga till metadata i katalogen. Mer information finns i [Skydda åtkomsten till Data Catalog och data till gångar](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Kommer Data Catalog att göras tillgängliga för lokal distribution också?
Data Catalog är en moln tjänst som kan arbeta med både molnbaserade och lokala data källor för att leverera en hybrid identifierings lösning för data källor. Det finns för närvarande inga planer för en version av tjänsten Data Catalog som körs lokalt.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kan jag extrahera fler eller fler rikare metadata från de data källor jag registrerar?
Vi arbetar aktivt för att utöka funktionerna i Data Catalog. Om du vill att ytterligare metadata ska extraheras från data källan under registreringen, rekommenderar vi att de (eller rösta på den, om det redan har föreslagits) i [Data Catalog i Azure feedback-forumet](https://feedback.azure.com/forums/906052-data-catalog). 

Om du vill inkludera kolumn/schema-metadata, för hands vyer eller data profiler för data källor där dessa metadata inte extraheras av data källans registrerings verktyg kan du använda Data Catalog API för att lägga till dessa metadata. Mer information finns i [Azure Data Catalog REST API](/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hur gör jag för att begränsa synligheten för registrerade data till gångar så att endast vissa personer kan upptäcka dem?
Välj data till gångarna i Data Catalog och klicka sedan på knappen **bli ägare** . Ägare av data till gångar i Data Catalog kan ändra Synlighets inställningarna så att alla användare kan identifiera de ägda till gångarna eller begränsa synligheten för vissa användare. Mer information finns i [Hantera data till gångar i Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hur gör jag för att uppdatera registreringen för en data till gång så att ändringar i data källan avspeglas i katalogen?
Om du vill uppdatera metadata för data till gångar som redan har registrerats i katalogen registrerar du bara den data källa som innehåller till gångarna. Eventuella ändringar i data källan, till exempel kolumner som läggs till eller tas bort från tabeller eller vyer, uppdateras i katalogen, men alla anteckningar som anges av användarna bevaras.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Min fråga besvaras inte här. Var kan jag skaffa svar?
Gå till [Azure Data Catalog-forumet](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Frågor som ställs där kommer att se hur de fungerar.