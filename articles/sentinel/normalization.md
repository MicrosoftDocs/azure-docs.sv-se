---
title: Data normalisering i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur Azure Sentinel normaliserar data från många olika källor och information om normaliserings schema.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 3d9e436d636fbd5414367efb0e122748a8e9e2cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390818"
---
# <a name="normalization-in-azure-sentinel"></a>Normalisering i Azure Sentinel

I den här artikeln beskrivs normalisering av data schema i Azure Sentinel och särskilt schemat för nätverks anslutningar och sessioner som en länk ingår i.

## <a name="what-is-normalization"></a>Vad är normalisering

Att arbeta med olika data typer och tabeller utgör en utmaning. Du måste känna till många olika data typer och scheman, som måste skriva och använda en unik uppsättning analys regler, arbets böcker och jakt frågor för var och en, även för de som delar commonalities (till exempel som rör brand Väggs enheter). Korrelationen mellan de olika data typerna, som krävs för undersökning och jakt, är också svårt. Azure Sentinel ger en sömlös upplevelse för att hantera data från olika källor i enhetliga, normaliserade vyer.

Den **gemensamma informations modellen** för Azure Sentinel består av tre aspekter:

- **Normaliserade scheman** gäller för vanliga uppsättningar av förutsägbara händelse typer (tabeller) som är enkla att arbeta med och som du kan använda för att skapa enhetliga funktioner på (t. ex. nätverks tabell). Schemat innehåller också en normaliserad kolumn konvention och definitioner för värde-och format standardisering (standardiserad standard representation av data, till exempel IP-adresser).

- **Parser** mappar befintliga data av olika typer till det normaliserade schemat. Enligt modellen kan data parsas till det normaliserade schemat i fråge tiden (med hjälp av funktioner) eller upphämtnings tid. Just nu stöds endast tids analys av frågor.

- **Innehåll för varje normaliserat schema** innehåller analys regler, interaktiva arbets böcker, jakt frågor och ytterligare innehåll.

### <a name="current-release"></a>Aktuell version

I den här versionen är de [normaliserade nätverks anslutningarna och session schema](./normalization-schema.md) (v-1.0.0) tillgängliga för en offentlig för hands version. Schemat beskriver nätverks anslutningar eller sessioner, till exempel sådana som loggats av brand väggar, tråd data, NSG, Netflow, proxy-system och webbsäker gatewayer.  Valda Time-Time-parser och analys regler är tillgängliga tillsammans med schemat och använder dem.

Schemat är för närvarande endast tillgängligt via Time-parser (se avsnittet parser).

Du kan parsa data till ytterligare representationer och använda [OSSEM entiteter som namngivnings modell](https://ossemproject.com/cdm/entities/intro.html#) för att skapa kolumner som är konsekventa med befintliga och framtida normaliserade tabeller.

## <a name="normalized-schema-and-parsing"></a>Normaliserat schema och parsning

### <a name="how-our-normalized-schemas-are-defined"></a>Hur våra normaliserade scheman definieras

Azure Sentinel justeras med den gemensamma informations modellen för [OSSEM (Security Events) med öppen källkod](https://ossemproject.com/intro.html) , vilket gör det möjligt att förutsäga entiteter i de normaliserade tabellerna. OSSEM är ett community-projekt som fokuserar främst på dokumentation och standardisering av säkerhets händelse loggar från olika data källor och operativ system. Dessutom tillhandahåller projektet en common information Model (CIM) som kan användas för data tekniker under data normaliserings metoder som gör det möjligt för säkerhetsanalytiker att fråga och analysera data över olika data källor.

[OSSEM CIM](https://ossemproject.com/cdm/intro.html) definierar en uppsättning entiteter (till exempel: File, Host, IP, process) och definierar en uppsättning attribut för varje entitet. Dessutom definierar CIM en uppsättning tabeller (t. ex. en [nätverksgateway](https://ossemproject.com/cdm/tables/network_session.html) ) som använder relevanta attribut från dessa entiteter, vilket möjliggör sömlös och förutsägbar korrelation. Observera att entiteter kan kapslas (till exempel kan källentiteten innehålla en filentitet som har ett namnattribut).

Om du vill veta mer om OSSEM Entity-struktur kan du gå till den [officiella OSSEM-referensen](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Hur de normaliserade scheman implementeras i Azure Sentinel

I implementeringen av OSSEM CIM i Azure Sentinel projicerar vi OSSEM-representationen till en Log Analytics tabell representation, oavsett om den här representationen är en inbyggd tabell eller har skapats med hjälp av eller funktioner som mappar befintliga data till den här representationen. För tabell representationen sammanfogar vi namnen på OSSEM och attributnamn och mappar dem sammantaget till ett enda kolumn namn. Till exempel kommer en källentitet som innehåller en filentitet som innehåller en hash-entitet som innehåller ett MD5-attribut att implementeras som följande Log Analytics kolumn: SrcFileHashMd5. (OSSEM använder *snake_case* som standard, medan Azure Sentinel och Log Analytics använder *PascalCase*. I OSSEM är sådana kolumner src_file_hash_md5.)

Ytterligare anpassade fält kan finnas i Azure Sentinel-implementeringen, på grund av Log Analytics plattforms krav och användnings fall som är särskilda för Azure Sentinel-kunder.

### <a name="schema-reference"></a>Schema referens

Läs mer i [schema referens dokumentet](./normalization-schema.md), samt den officiella [OSSEM-projekt dokumentationen](https://ossemproject.com/cdm/intro.html).

Schema referensen innehåller också värde-och format standardisering. Käll fälten, ursprungligt eller parsat, kanske inte är i standardformat eller använder inte schema standard listan över värden, och därför måste konverteras till schema standard representationen för att bli helt normaliserad.

## <a name="parsers"></a>Tolkar

- [Vad är parsning](#what-is-parsing)
- [Använda parsar för Query Time](#using-query-time-parsers)

### <a name="what-is-parsing"></a>Vad är parsning

Med en grundläggande uppsättning definierade normaliserade tabeller tillgängliga måste du omvandla (parsa/mappa) dina data till dessa tabeller. Det innebär att du kommer att extrahera vissa data från dess RAW-form till välkända kolumner i det normaliserade schemat. Parsning i Azure Sentinel sker vid **fråge tids** -tolkare skapas som Log Analytics användar funktioner (med Kusto Query Language-KQL) som transformerar data i befintliga tabeller (till exempel CommonSecurityLog, anpassade loggnings tabeller, syslog) till schemat för normaliserade tabeller.

Den andra typen av parsning, som ännu inte stöds i Azure Sentinel, är vid inläsnings **tid** – så att du kan samla in data direkt i de normaliserade tabellerna som de matas in från dess data källor. Hämtnings tids parsning ger bättre prestanda när data modellen efter frågas direkt utan att du behöver använda funktioner.

### <a name="using-query-time-parsers"></a>Använda parsar för Query Time

- [Installera en parser](#installing-a-parser)
- [Använda parsern](#using-the-parsers)
- [Anpassa tolkare](#customizing-parsers)

#### <a name="installing-a-parser"></a>Installera en parser

De tillgängliga parsarna för frågeintervall är tillgängliga i den [officiella GitHub-lagringsplatsen](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))för Azure Sentinel. Varje parser har versions hantering som gör det enkelt för kunder att använda och övervaka framtida uppdateringar. Så här installerar du en parser:

1. Kopiera relevant parser-innehåll från varje relevant KQL-fil i länken ovan GitHub till Urklipp

1. På Azure Sentinel-portalen öppnar du sidan loggar och klistrar in innehållet i KQL-filen på skärmen loggar och klickar på **Spara**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Installera en ny parser":::

1. I dialog rutan Spara fyller du i fälten enligt följande:
    1. **Namn**: du rekommenderas att använda samma värde som används i fältet **funktions Ali Aset** (i exemplet ovan, *CheckPoint_Network_NormalizedParser*)
    
    1. **Spara som**: Välj **funktion**

    1. **Funktions Ali Aset**: ska namnges i följande namngivnings konvention – *PRODUCT_Network_NormalizedParser* (i exemplet ovan *CheckPoint_Network_NormalizedParser*).

    1. **Kategori**: du kan välja en befintlig kategori eller skapa en ny kategori (till exempel *NormalizedNetworkSessionsParsers*)
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Spara parsern":::

För att kunna använda parsern korrekt måste du också installera den tomma nätverks schema parsern (som skapar en tom tabellvy av alla schema fält för nätverks sessioner) och nätverkets meta-parser (som sammanställer alla aktiverade tolkare för att skapa en enskild vy av data från olika källor i nätverks schemat). Installation av de här två parsarna görs på samma sätt som i ovanstående steg.

När du sparar en fråge funktion kan du behöva stänga Query Explorer och öppna den igen för att den nya funktionen ska visas.

#### <a name="using-the-parsers"></a>Använda parsern

När den är aktive rad kan du använda meta-parsern för att fråga en enhetlig vy för alla aktiverade parser. Det gör du genom att gå till sidan Azure Sentinel-loggar och fråga meta-parser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Fråga parsern":::
 
Du kan också komma åt meta-parsern eller enskilda tolkare med Query Explorer på sidan loggar genom att klicka på "fråga Utforskaren":

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Query Explorer":::

I fönstret till höger expanderar du avsnittet "sparade frågor" och letar reda på mappen "NormalizedNetworkParsers" (eller kategori namnet som du har valt när du skapade parsarna):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Hitta din parser":::

Du kan klicka på varje enskild parser och se den underliggande funktionen den använder och köra den (eller komma åt den direkt via dess alias, enligt beskrivningen ovan). Observera att vissa tolkare kan behålla de ursprungliga fälten sida vid sida till de normaliserade fälten för bekvämlighet. Detta kan enkelt redige ras i parser-frågans fråga.

> [!TIP]
> Du kan använda dina sparade funktioner i stället för Azure Sentinel-tabeller i alla frågor, inklusive frågor om jakt och identifiering. Mer information finns i:
>
> - [Data normalisering i Azure Sentinel](normalization.md#parsers)
> - [Tolka text i Azure Monitor loggar](/azure/azure-monitor/log-query/parse-text)
>
#### <a name="customizing-parsers"></a>Anpassa tolkare

Du kan upprepa stegen ovan (hitta parsern i Query Explorer), klicka på relevant parser och se dess funktions implementering.
Du kan till exempel välja att redigera meta-parsern för att lägga till/ta bort enskilda tolkare.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Anpassa din parser":::
 
När funktionen har ändrats klickar du på Spara igen och använder samma namn, alias och kategori. En dialog ruta för åsidosättning öppnas – tryck på OK:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Är du säker":::

#### <a name="additional-information"></a>Ytterligare information

JSON, XML och CSV är särskilt praktiska för parsning vid tidpunkt. Azure Sentinel har inbyggda analys funktioner för JSON, XML och CSV, samt ett JSON-parsningsfel.  Mer information finns i [använda JSON-fält i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blogg). 

Läs mer om [sparade frågor](../azure-monitor/log-query/example-queries.md) (implementeringen av fråge tids parser) i Log Analytics.


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig om Azure Sentinel-normaliserings schema. För själva referens schemat, se [schema referens för Azure Sentinel-datanormalisering](./normalization-schema.md).

* [Azure Sentinel-blogg](https://aka.ms/azuresentinelblog). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.