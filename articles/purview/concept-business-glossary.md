---
title: Förstå företags ord listans funktioner i Azure avdelningens kontroll (för hands version)
description: I den här artikeln förklaras vilken företags ord lista som finns i Azure avdelningens kontroll.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555159"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Förstå företags ord listans funktioner i Azure avdelningens kontroll

Den här artikeln innehåller en översikt över funktionen företags ord lista i Azure avdelningens kontroll. 

## <a name="business-glossary"></a>Företagsordlistan

En ord lista innehåller vokabulär för företags användare.  Det består av affärs villkor som kan vara relaterade till varandra och som kan kategoriseras så att de kan tolkas i olika sammanhang. Dessa villkor kan sedan mappas till till gångar som en databas, tabeller, kolumner osv. Detta hjälper till att sammanfatta de tekniska jargong som är associerade med data lagringen och gör det möjligt för företags användaren att upptäcka och arbeta med data i ord listan som är mer bekanta.


En företags ord lista är en samling villkor. Varje term representerar ett objekt i en organisation och det är mycket troligt att det finns flera villkor som representerar samma objekt. En kund kan också kallas klient, köpare eller köpare. Dessa flera termer har en relation med varandra. Relationen mellan dessa villkor kan vara något av följande:

- synonymer – olika villkor med samma definition
- relaterat-annat namn med liknande definition

Samma villkor kan också innebära flera affärs objekt. Det är viktigt att varje term är väl definierad och tydligt förstås i organisationen.

## <a name="custom-attributes"></a>Anpassade attribut

Azure avdelningens kontroll stöder åtta out-of-Box-attribut för valfri affärs ord lista:
- Name
- Definition
- Data vård
- Data experter
- Förkortning
- Synonymer
- Relaterade villkor
- Resurser

Attributen kan inte redige ras eller tas bort. Dessa attribut är dock inte tillräckliga för att fullständigt definiera en term i en organisation. För att lösa det här problemet tillhandahåller avdelningens kontroll en funktion där du kan definiera anpassade attribut för ord listan.

## <a name="term-templates"></a>Term mallar

Term mallar innehåller ord listans anpassade attribut som logiskt grupperas i katalogen. Med funktionen kan du gruppera alla relevanta anpassade attribut tillsammans i en mall och sedan tillämpa mallen när du skapar ord frasen. Till exempel alla finansbaserade anpassade attribut som cost center, TB Center och bokförings kod kan grupperas i en term mal len ekonomi mall och ekonomi mal len kan användas för att skapa finansiella ord lista.

Alla standardattribut är grupperade i en system standard-mall. Term mallar som du skapar innehåller dessa attribut tillsammans med eventuella ytterligare anpassade attribut som skapas som en del av processen för att skapa mallar.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Etiketter för ord lista vs-klassificering eller känslighet

Ord listan är en kommentar till en data till gång, men var och en av dem har en annan innebörd i katalogens sammanhang. 

### <a name="glossary"></a>Ordlista

Som anges ovan definierar affärs ord listan affärs terminologi för en organisation och hjälper till att överbrygga klyftan mellan olika avdelningar i företaget.

### <a name="classifications"></a>Klassificeringar

Klassificeringar är kommentarer som kan tilldelas till entiteter. Med klassificeringarna kan du använda dem i flera scenarier som:

- förstå vilken typ av data som lagras i data till gångarna
- definiera principer för åtkomst kontroll

Avdelningens kontroll har fler än 100 system klassificerare idag och du kan definiera egna klassificerare i katalogen. Som en del av skannings processen identifierar vi automatiskt dessa klassificeringar och tillämpar dem på data till gångar och scheman. Men du kan åsidosätta dem när som helst. De mänskliga åsidosättningarna ersätts aldrig av automatiska genomsökningar.

### <a name="sensitivity-labels"></a>Känslighetsetiketter

Känslighets etiketter är en typ av anteckning som gör att du kan klassificera och skydda din organisations data, utan att hindra produktiviteten och samarbetet. Känslighets etiketter används för att identifiera kategorier av klassificerings typer i dina organisations data och gruppera de principer som du vill använda för varje kategori. Avdelningens kontroll använder samma känsliga informations typer som Microsoft 365, vilket gör att du kan sträcka ut dina befintliga säkerhets principer och skydd över hela innehållet och data fastigheten. Samma etiketter kan delas mellan Microsoft Office produkter och data till gångar i avdelningens kontroll.

## <a name="next-steps"></a>Nästa steg

- [Hantera term mallar](how-to-manage-term-templates.md)
- [Bläddra i Data Catalog i Azure avdelningens kontroll](how-to-browse-catalog.md)
