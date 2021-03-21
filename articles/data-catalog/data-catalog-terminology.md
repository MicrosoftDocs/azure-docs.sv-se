---
title: Azure Data Catalog terminologi
description: Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 008d0f106b07d61f0989a479457b12398000b5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674587"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog terminologi

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.

## <a name="catalog"></a>Katalog

Azure Data Catalog är en molnbaserad metadata-lagringsplats där data källor och data till gångar kan registreras. Katalogen fungerar som en central lagrings plats för strukturella metadata som extraheras från data källor och för beskrivande metadata som lagts till av användarna.

## <a name="data-source"></a>Datakälla

En data källa är ett system eller en behållare som hanterar data till gångar. Exempel på detta är SQL Server databaser, Oracle-databaser, SQL Server Analysis Services-databaser (tabell-eller flerdimensionella) och SQL Server Reporting Services-servrar.

## <a name="data-asset"></a>Data till gång

Data till gångar är objekt som finns i data källor som kan registreras med katalogen. Exempel på detta är SQL Server tabeller och vyer, Oracle-tabeller och-vyer, SQL Server Analysis Services mått, dimensioner och KPI: er och SQL Server Reporting Services rapporter.

## <a name="data-asset-location"></a>Plats för data till gång

Katalogen lagrar platsen för en data källa eller data till gång, som kan användas för att ansluta till källan med ett klient program. Platsens format och information varierar beroende på typen av data källa. Till exempel kan en SQL Server tabell identifieras med sitt fyra del namn – server namn, databas namn, schema namn, objekt namn – medan en SQL Server Reporting Services rapport kan identifieras av dess URL.

## <a name="structural-metadata"></a>Strukturella metadata

Strukturella metadata är de metadata som extraheras från en data källa som beskriver strukturen för en data till gång. Detta omfattar platsen för till gångar, dess objekt namn och typ och ytterligare typ särskilda egenskaper. Strukturella metadata för tabeller och vyer innehåller till exempel namn och data typer för objektets kolumner.

## <a name="descriptive-metadata"></a>Beskrivande metadata

Beskrivande metadata är metadata som beskriver syftet med eller avsikten med en data till gång. Vanligt vis beskrivande metadata läggs till av katalog användare som använder Azure Data Catalog Portal, men den kan också extraheras från data källan under registreringen. Azure Data Catalog registrerings verktyget extraherar till exempel beskrivningar från egenskapen Description i SQL Server Analysis Services och SQL Server Reporting Services och från [ms_description utökade egenskapen](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)) i SQL Server-databaser, om dessa egenskaper har fyllts med värden.

## <a name="request-access"></a>Begär åtkomst

En data till gångs beskrivande metadata kan innehålla information om hur du begär åtkomst till data till gången eller data källan. Den här informationen visas på platsen för data till gångar och kan innehålla ett eller flera av följande alternativ:

* E-postadressen till den användare eller det team som ansvarar för att bevilja åtkomst till data källan.
* URL: en för den dokumenterade process som användarna måste följa för att få åtkomst till data källan.
* URL: en för ett identitets-och åtkomst hanterings verktyg (till exempel Microsoft Identity Manager) som kan användas för att få åtkomst till data källan.
* En fritext post som beskriver hur användarna kan få åtkomst till data källan.

## <a name="preview"></a>Förhandsgranskning

En förhands granskning i Azure Data Catalog är en ögonblicks bild av upp till 20 poster som kan extraheras från data källan under registreringen och lagras i katalogen med data till gångens metadata. För hands versionen kan hjälpa användare som identifierar en data till gång bättre att förstå dess funktion och syfte. Med andra ord kan du se exempel data som är mer värdefulla än att bara Visa kolumn namn och data typer.
För hands versioner stöds bara för tabeller och vyer och måste väljas uttryckligen av användaren under registreringen.

## <a name="data-profile"></a>Dataprofil

En data profil i Azure Data Catalog är en ögonblicks bild av metadata på tabell nivå och kolumn nivå om en registrerad data till gång som kan extraheras från data källan under registreringen och lagras i katalogen med data till gångens metadata. Data profilen kan hjälpa användare som identifierar en data till gång bättre att förstå dess funktion och syfte. På liknande sätt som för hands versionerna måste data profilerna väljas uttryckligen av användaren vid registreringen.

> [!NOTE]
> Att extrahera en data profil kan vara en kostsam åtgärd för stora tabeller och vyer och kan avsevärt öka den tid som krävs för att registrera en data källa.


## <a name="user-perspective"></a>Användar perspektiv

I Azure Data Catalog kan alla användare tillhandahålla beskrivande metadata för en registrerad data till gång. Varje användare har ett tydligt perspektiv på data och dess användning. Administratören som ansvarar för en server kan till exempel ange information om dess service nivå avtal (SLA) eller säkerhets kopierings fönster. en data ledning kan tillhandahålla länkar till dokumentation för de affärs processer som data har stöd för. en analytiker kan ge en beskrivning av de villkor som är mest relevanta för andra analytiker och som kan vara mest värdefulla för de användare som behöver identifiera och förstå data.

Vart och ett av dessa perspektiv är mycket värdefullt och med Azure Data Catalog varje användare kan ge den information som är meningsfull för dem, medan alla användare kan använda den informationen för att förstå data och dess syfte.

## <a name="expert"></a>Expert

En expert är en användare som har identifierats med ett informerat "expert perspektiv" för en data till gång. Alla användare kan lägga till sig själva eller en annan användare som expert för en till gång. Att listas som en expert ger inte några ytterligare behörigheter i Azure Data Catalog. Det gör det möjligt för användarna att enkelt hitta de perspektiv som troligen är användbara när de ska granska en till gångs beskrivande metadata.

## <a name="owner"></a>Ägare

En ägare är en användare som har ytterligare behörighet för att hantera en data till gång i Azure Data Catalog. Användare kan bli ägare till registrerade data till gångar och ägare kan lägga till andra användare som medägare. Mer information finns i [Hantera data till gångar](data-catalog-how-to-manage.md)  

> [!NOTE]
> Ägarskap och hantering är endast tillgängligt i standard versionen av Azure Data Catalog.

## <a name="registration"></a>Registrering

Registreringen innebär att data till gångens metadata extraheras från en data källa och kopieras till den Azure Data Catalog tjänsten. Data till gångar som har registrerats kan sedan kommenteras och identifieras.

## <a name="next-steps"></a>Nästa steg

[Snabb start: skapa en Azure Data Catalog](data-catalog-get-started.md)