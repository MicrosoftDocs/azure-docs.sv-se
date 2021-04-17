---
title: Skapa mönsterregler för resursuppsättning
description: Lär dig hur du skapar en regel för resursuppsättningsmönster för att skriva över hur tillgångar grupperas i resursuppsättningar
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 61de2cf2e3ad9175d97378234d62f72ab3517b51
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587841"
---
# <a name="create-resource-set-pattern-rules"></a>Skapa mönsterregler för resursuppsättning

Databehandlingssystem i stor skala lagrar vanligtvis en enda tabell på en disk som flera filer. Det här konceptet representeras i Azure Purview med hjälp av resursuppsättningar. En resursuppsättning är ett enda objekt i datakatalogen som representerar ett stort antal tillgångar i lagringen. Mer information finns i Förstå [resursuppsättningar.](concept-resource-sets.md)

När du skannar ett lagringskonto använder Azure Purview en uppsättning definierade mönster för att avgöra om en grupp med tillgångar är en resursuppsättning. I vissa fall kanske inte resursuppsättningsgruppen i Azure Purview återspeglar din datae egendom korrekt. Med regler för resursuppsättningsmönster kan du anpassa eller åsidosätta hur Azure Purview identifierar vilka tillgångar som är grupperade som resursuppsättningar och hur de visas i katalogen.

Mönsterregler stöds för närvarande i följande källtyper:
- Azure Data Lake Storage Gen2
- Azure Blob Storage
- Azure Files


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Så här skapar du en regel för resursuppsättningsmönster

Följ stegen nedan för att skapa en ny regel för resursuppsättningsmönster:

1. Gå till hanteringscentret. Välj **Mönsterregler** på menyn under rubriken Resursuppsättningar. Välj **+ Ny för** att skapa en ny regeluppsättning.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Skapa en ny regel för resursuppsättningsmönster" border="true":::

1. Ange omfånget för regeln för resursuppsättningsmönster. Välj typ av lagringskonto och namnet på det lagringskonto som du vill skapa en regeluppsättning för. Varje uppsättning regler tillämpas i förhållande till ett mappsökvägsomfång som anges i **fältet Mappsökväg.**

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Skapa regelkonfigurationer för resursuppsättningsmönster" border="true":::

1. Om du vill ange en regel för ett konfigurationsomfång väljer **du + Ny regel.**

1. Ange i följande fält för att skapa en regel:

   1. **Regelnamn:** Namnet på konfigurationsregeln. Det här fältet har ingen effekt på de tillgångar som regeln gäller för.

   1. **Kvalificerat namn:** En kvalificerad sökväg som använder en kombination av text, dynamiska ersättare och statiska ersättare för att matcha tillgångar med konfigurationsregeln. Den här sökvägen är relativ till konfigurationsregelns omfång. Se [syntaxavsnittet](#syntax) nedan för detaljerade anvisningar om hur du anger kvalificerade namn.

   1. **Visningsnamn:** Tillgångens visningsnamn. Det här fältet är valfritt. Använd oformaterad text och statiska ersättare för att anpassa hur en tillgång visas i katalogen. Mer detaljerade anvisningar finns i [syntaxavsnittet](#syntax) nedan.

   1. **Gruppera inte som resursuppsättning:** Om den här inställningen är aktiverad grupperas inte matchade resurser i en resursuppsättning.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Skapa en ny konfigurationsregel." border="true":::

1. Spara regeln genom att klicka på Lägg **till**.

> [!NOTE]
> När en mönsterregel har skapats tillämpar alla nya genomsökningar regeln under inmatningen. Befintliga tillgångar i datakatalogen uppdateras via en bakgrundsprocess som kan ta upp till några timmar. 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Syntax för mönsterregel

När du skapar regler för resursuppsättningsmönster använder du följande syntax för att ange vilka tillgångsregler som gäller för.

### <a name="dynamic-replacers-single-brackets"></a>Dynamiska ersättare (enkla hakparenteser)

Enkla hakparenteser används **som dynamiska ersättare** i ett mönster. Ange en dynamisk ersättning i det kvalificerade namnet med formatet `{<replacerName:<replacerType>}` . Om de matchas används dynamiska ersättare som ett grupperingsvillkor som anger att tillgångar ska representeras som en resursuppsättning. Om tillgångarna är grupperade i en resursuppsättning skulle den kvalificerade sökvägen för resursuppsättningen innehålla `{replacerName}` den plats där ersättaren har angetts.

Om till exempel två tillgångar och `folder1/file-1.csv` `folder2/file-2.csv` matchas mot regeln `{folder:string}/file-{NUM:int}.csv` är resursuppsättningen en enda entitet `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Specialfall: Dynamiska ersättare när de inte grupperas i resursuppsättningen

Om *Gruppera inte som resursuppsättning har* aktiverats för en mönsterregel är ersättarnamnet ett valfritt fält. `{:<replacerType>}` är giltig syntax. Till exempel skulle `file-{:int}.csv` matcha för och och skapa två olika tillgångar i stället för en `file-1.csv` `file-2.csv` resursuppsättning.

### <a name="static-replacers-double-brackets"></a>Statiska ersättare (dubbla hakparenteser)

Dubbla hakparenteser används **som statiska ersättare** i det kvalificerade namnet på en mönsterregel. Ange en statisk ersättning i det kvalificerade namnet med formatet `{{<replacerName>:<replacerType>}}` . Om de matchas skapar varje uppsättning unika statiska ersättningarvärden olika resursuppsättningsgrupper.

Om till exempel två tillgångar och `folder1/file-1.csv` `folder2/file-2.csv` matchas mot regeln `{{folder:string}}/file-{NUM:int}.csv` skapas två resursuppsättningar `folder1/file-{NUM}.csv` och `folder2/file-{NUM}.csv` .

Statiska ersättare kan användas för att ange visningsnamnet för en tillgång som matchar en mönsterregel. Om `{{<replacerName>}}` du använder i visningsnamnet för en regel används det matchade värdet i tillgångsnamnet.

### <a name="available-replacement-types"></a>Tillgängliga ersättningstyper

Nedan visas tillgängliga typer som kan användas i statiska och dynamiska ersättare:

| Typ | Struktur |
| ---- | --------- |
| sträng | En serie med 1 eller fler Unicode-tecken, inklusive avgränsare som blanksteg. |
| int | En serie med 1 eller fler 0–9 ASCII-tecken kan vara 0 prefix (t.ex. 0001). |
| guid | En serie med 32 eller 8-4-4-4-12 strängrepresentation av ett UUID enligt definitionendefa i [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| date | En serie med 6 eller 8 0–9 ASCII-tecken med valfritt avgränsare: yyyymmdd, yyyy-mm-dd, yymmdd, yy-mm-dd, som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | En serie med 4 eller 6 0–9 ASCII-tecken med valfritt avgränsare: HHmm, HH:mm, HHmmss, HH:mm:ss som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| timestamp | En serie med 12 eller 14 0–9 ASCII-tecken med valfritt avgränsare: yyyy-mm-ddTHH:mm, yyyymmddhhmm, yyyy-mm-ddTHH:mm:ss, yyyymmddHHmmss som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | Kan innehålla "true" eller "false", om det inte är känsligt. |
| antal | En serie med 0 eller fler 0–9 ASCII-tecken, kan det vara 0 prefix (t.ex. 0001) följt av en punkt (om du vill) och en serie med 1 eller fler 0–9 ASCII-tecken kan den vara 0 postfixerad (t.ex. .100) |
| hex | En serie med 1 eller fler ASCII-tecken från uppsättningen 0-1 och A-F, värdet kan vara 0 prefix |
| locale | En sträng som matchar den syntax som anges i [RFC 5646](https://tools.ietf.org/html/rfc5646). |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Ordningsföljd för regler för resursuppsättningsmönster som tillämpas

Nedan visas ordningen på åtgärder för att tillämpa mönsterregler:

1. Mer specifika omfång prioriteras om en tillgång matchar två regler. Regler i ett omfång gäller till `container/folder` exempel före regler i omfånget `container` .

1. Ordningsföljd för regler inom ett specifikt omfång. Detta kan redigeras i UX.

1. Om en tillgång inte matchar någon angiven regel gäller standardresursuppsättningens heuristik.

## <a name="examples"></a>Exempel

### <a name="example-1"></a>Exempel 1

SAP-datauttrahering till fullständiga inbelastningar och deltabelastningar

#### <a name="inputs"></a>Indata

ﬁler:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Mönsterregel

**Omfång:**`https://myazureblob.blob.core.windows.net/bar/`

**Visningsnamn:** Extern kund

**Kvalificerat namn:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Resursuppsättning:** true

#### <a name="output"></a>Utdata

En resursuppsättningstillgång

**Visningsnamn:** Extern kund

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Exempel 2

IoT-data i avro-format

#### <a name="inputs"></a>Indata

ﬁler:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Mönsterregler

**Omfång:**`https://myazureblob.blob.core.windows.net/bar/`

Regel 1

**Visningsnamn:** "machine-89"

**Kvalificerat namn:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Resursuppsättning:** true

Regel 2

**Visningsnamn:** "machine-90"

**Kvalificerat namn:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Resursuppsättning:** true

#### <a name="outputs"></a>Utdata

2 resursuppsättningar

Resursuppsättning 1

**Visningsnamn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resursuppsättning 2

**Visningsnamn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Exempel 3

IoT-data i avro-format

#### <a name="inputs"></a>Indata

ﬁler:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Mönsterregel

**Omfång:**`https://myazureblob.blob.core.windows.net/bar/`

**Visningsnamn:** "Machine-{{machineid}}"

**Kvalificerat namn:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Resursuppsättning:** true

#### <a name="outputs"></a>Utdata

Resursuppsättning 1

**Visningsnamn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resursuppsättning 2

**Visningsnamn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Exempel 4

Gruppera inte i resursuppsättningar

#### <a name="inputs"></a>Indata

ﬁler:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Mönsterregel

**Omfång:**`https://myazureblob.blob.core.windows.net/bar/`

**Visningsnamn:**`Machine-{{machineid}}`

**Kvalificerat namn:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Resursuppsättning:** falskt

#### <a name="outputs"></a>Utdata

4 enskilda tillgångar

Tillgång 1

**Visningsnamn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Tillgång 2

**Visningsnamn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Tillgång 3

**Visningsnamn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Tillgång 4

**Visningsnamn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Nästa steg

Kom igång genom [att registrera och skanna ett Azure Data Lake Gen2-lagringskonto.](register-scan-adls-gen2.md)