---
title: Så här ansluter du till data källor i Azure Data Catalog
description: Instruktions artiklar som visar hur du ansluter till data källor som upptäckts med Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: da3302ac25aaac6202f42cee400b307fc701fe50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674979"
---
# <a name="how-to-connect-to-data-sources"></a>Så här ansluter du till datakällor

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad moln tjänst som fungerar som ett system för registrering och identifierings system för företags data källor. Med andra ord är **Azure Data Catalog** att hjälpa människor att upptäcka, förstå och använda data källor och hjälpa organisationer att få mer värde än befintliga data. En viktig aspekt i det här scenariot använder data – när en användare upptäcker en data källa och förstår syftet, är nästa steg att ansluta till data källan för att kunna använda data.

## <a name="data-source-locations"></a>Platser för data källor
Under registreringen av data källan tar **Azure Data Catalog** emot metadata om data källan. Dessa metadata innehåller information om data källans plats. Information om platsen varierar från data källan till data källan, men den kommer alltid att innehålla den information som krävs för att ansluta. Platsen för en SQL Server tabell innehåller till exempel Server namnet, databas namnet, schema namnet och tabell namnet, medan platsen för en SQL Server Reporting Services rapport innehåller Server namnet och sökvägen till rapporten. Andra typer av data källor har platser som återspeglar strukturen och funktionerna i käll systemet.

## <a name="integrated-client-tools"></a>Integrerade klient verktyg
Det enklaste sättet att ansluta till en data källa är att använda "öppna i..." på **Azure Data Catalog** -portalen. Den här menyn visar en lista över alternativ för att ansluta till den valda data till gången.
När du använder standard panelen visas den här menyn på varje bricka.

 ![Öppna en SQL Server tabell i Excel från panelen data till gång](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

När du använder listvyn är menyn tillgänglig i Sök fältet överst i fönstret Portal.

 ![Öppna en SQL Server Reporting Services-rapport i Rapporthanteraren](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Klient program som stöds
När du använder "öppna i..." meny för data källor i Azure Data Catalog-portalen måste rätt klient program vara installerat på klient datorn.

| Öppna i program | Fil tillägg/protokoll | Program versioner som stöds |
| --- | --- | --- |
| Excel |. ODC |Excel 2010 eller senare |
| Excel (översta 1000) |. ODC |Excel 2010 eller senare |
| Power Query |.xlsx |Excel 2016 eller Excel 2010 eller Excel 2013 med Power Query för Excel-tillägget installerat |
| Power BI Desktop |. pbix |Power BI Desktop juli 2016 eller senare |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 uppdatering 4 eller senare med SQL Server verktyg installerat |
| Rapporthanteraren |http:// |Se [webb läsar krav för SQL Server Reporting Services](/sql/reporting-services/browser-support-for-reporting-services-and-power-view) |

## <a name="your-data-your-tools"></a>Dina data, dina verktyg
Vilka alternativ som är tillgängliga på menyn beror på vilken typ av data till gång som är markerat. Naturligtvis är inte alla möjliga verktyg inkluderade i "öppna i..." -menyn, men det är fortfarande enkelt att ansluta till data källan med hjälp av ett klient verktyg. När en data till gång väljs i **Azure Data Catalog** Portal visas den fullständiga platsen i rutan Egenskaper.

 ![Anslutnings information för en SQL Server tabell](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Informationen om anslutnings informationen skiljer sig från data källans typ till data källans typ, men informationen som ingår i portalen ger dig allt du behöver för att ansluta till data källan i alla klient verktyg. Användare kan kopiera anslutnings informationen för de data källor som de har identifierat med hjälp av **Azure Data Catalog**, så att de kan arbeta med data i det verktyg som du väljer.

## <a name="connecting-and-data-source-permissions"></a>Behörigheter för anslutning och data Källa
Även om **Azure Data Catalog** gör data källor synliga, förblir åtkomsten till själva data under kontrollen av data källans ägare eller administratör. Att identifiera en data källa i **Azure Data Catalog** ger inte användaren några behörigheter att komma åt själva data källan.

För att göra det enklare för användare som identifierar en data källa men inte har behörighet att komma åt sina data, kan användarna ange information i egenskapen Request Access när de kommenterar en data källa. Information som tillhandahålls här – inklusive länkar till processen eller kontakt punkten för att få åtkomst till data källan visas tillsammans med data käll plats informationen i portalen.

 ![Anslutnings information med anvisningar om begär ande åtkomst](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Sammanfattning
Registrering av en data källa med **Azure Data Catalog** gör att data kan identifieras genom att kopiera strukturella och beskrivande metadata från data källan till katalog tjänsten. När en data källa har registrerats och identifierats kan användarna ansluta till data källan från **Azure Data Catalog** -portalen "öppna i..." " meny eller använda de data verktyg som du väljer.

## <a name="see-also"></a>Se även
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) själv studie kurs för steg-för-steg-anvisningar om hur du ansluter till data källor.