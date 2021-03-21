---
title: Registrera data källor i Azure Data Catalog
description: I den här artikeln beskrivs hur du registrerar data källor i Azure Data Catalog, inklusive de metadatafält som extraheras under registreringen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674809"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrera data källor i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion
Azure Data Catalog är en helt hanterad moln tjänst som fungerar som ett registrerings-och identifierings system för företags data källor. Med andra ord kan Data Catalog hjälpa människor att upptäcka, förstå och använda data källor, och det hjälper organisationer att få mer värde än befintliga data. Det första steget för att göra en data källa som kan upptäckas via Data Catalog är att registrera data källan.

## <a name="register-data-sources"></a>Registrera datakällor
Registreringen är en process där metadata från data källan extraheras och data kopieras till Data Catalog-tjänsten. Informationen ligger kvar där den är och är under kontroll av det aktuella systemets administratörer och principer.

Registrera en data källa genom att göra följande:
1. Starta Data Catalog registrerings verktyget för data källor i Azure Data Catalog-portalen. 
2. Logga in på ditt arbets-eller skolkonto med samma Azure Active Directorys autentiseringsuppgifter som du använder för att logga in på portalen.
3. Välj den data källa som du vill registrera.

Mer information om steg-för-steg-anvisningar finns i själv studie kursen [komma igång med Azure Data Catalog](data-catalog-get-started.md) .

När du har registrerat data källan spårar katalogen sin plats och indexerar dess metadata. Användare kan söka, bläddra i och identifiera data källan och sedan använda sin plats för att ansluta till den med hjälp av det program eller verktyg som du väljer.

## <a name="supported-data-sources"></a>Datakällor som stöds
En lista över data källor som stöds för närvarande finns i [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturella metadata
När du registrerar en data källa hämtar registrerings verktyget information om strukturen för de objekt som du väljer. Den här informationen kallas för strukturella metadata.

För alla objekt innehåller dessa strukturella metadata objektets plats, så att användare som identifierar data kan använda informationen för att ansluta till objektet i de klient verktyg som de väljer. Andra strukturella metadata inkluderar objekt namn, typ, och attribut/kolumn namn och datatyp.

## <a name="descriptive-metadata"></a>Beskrivande metadata
Förutom de grundläggande strukturella metadata som extraheras från data källan, extraherar data källans registrerings verktyg beskrivande metadata. För SQL Server Analysis Services och SQL Server Reporting Services hämtas dessa metadata från beskrivningen egenskaper som exponeras av dessa tjänster. För SQL Server extraheras värden som anges med den \_ utökade egenskapen MS Description. För Oracle Database extraherar registrerings verktyget för data källor KOMMENTARs kolumnen från \_ \_ vyn alla kommentarer.

Förutom de beskrivande metadata som extraheras från data källan kan användarna ange beskrivande metadata med hjälp av registrerings verktyget för data källor. Användare kan lägga till taggar, och de kan identifiera experter för de objekt som registreras. Alla dessa beskrivande metadata kopieras till Data Catalogs tjänsten tillsammans med strukturella metadata.

## <a name="include-previews"></a>Ta med för hands vyer
Som standard extraheras endast metadata från data källor och kopieras till den Data Catalog tjänsten, men förståelse av en data källa är ofta enklare när du kan visa ett exempel på de data som det innehåller.

Genom att använda Data Catalog registrerings verktyget för data källor kan du inkludera en ögonblicks bild för hands version av data i varje tabell och vy som är registrerad. Om du väljer att inkludera för hands versionerna under registreringen innehåller registrerings verktyget upp till 20 poster från varje tabell och vy. Den här ögonblicks bilden kopieras sedan till katalogen tillsammans med struktur och beskrivande metadata.

> [!NOTE]
> Breda tabeller med ett stort antal kolumner kan ha färre än 20 poster som ingår i deras för hands version.
>
>

## <a name="include-data-profiles"></a>Inkludera data profiler
Precis som för för hands versionerna kan du ge värdefull kontext för användare som söker efter data källor i Data Catalog, inklusive en data profil som gör det lättare att förstå identifierade data källor.

Genom att använda Data Catalog registrerings verktyget för data källor kan du inkludera en data profil för varje tabell och vy som har registrerats. Om du väljer att inkludera en data profil under registreringen, innehåller registrerings verktyget sammanställd statistik om data i varje tabell och vy, inklusive:

* Antalet rader och storlek för data i objektet.
* Datumet för den senaste uppdateringen av data och objekt schema.
* Antalet null-poster och distinkta värden för kolumner.
* Värdena för minsta, högsta, genomsnittliga och standard avvikelse för kolumner.

Statistiken kopieras sedan till katalogen tillsammans med struktur och beskrivande metadata.

> [!NOTE]
> Text-och datum kolumner innehåller inte genomsnitts statistik eller standard avvikelser i data profilen.
>
>

## <a name="update-registrations"></a>Uppdatera registreringar
När du registrerar en data källa kan den upptäckas i Data Catalog när du använder metadata och valfri för hands version som extraheras under registreringen. Om data källan behöver uppdateras i katalogen (till exempel om schemat för ett objekt har ändrats, ska tabeller som ursprungligen uteslutits inkluderas eller om du vill uppdatera data som ingår i förhands visningen), kan registrerings verktyget för data källor köras igen.

Omregistrering av en data källa som redan är registrerad utför en sammanfogad "upsert"-åtgärd: befintliga objekt uppdateras och nya objekt skapas. Alla metadata som användare tillhandahåller via Data Catalog-portalen behålls.

## <a name="summary"></a>Sammanfattning
Eftersom det kopierar strukturella och beskrivande metadata från en data källa till katalog tjänsten, gör det lättare att identifiera och förstå data källan i Data Catalog. När du har registrerat data källan kan du kommentera, hantera och identifiera den med hjälp av Data Catalog portalen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du registrerar data källor finns i själv studie kursen [komma igång med Azure Data Catalog](data-catalog-get-started.md) .
