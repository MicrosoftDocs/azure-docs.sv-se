---
title: Så här skapar du en konfiguration för en begränsad resurs uppsättning
description: Lär dig hur du skapar en konfigurations regel för en begränsad resurs uppsättning för att skriva över hur till gångar ska visas grupperade i resurs uppsättningar
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 10e925a84dbe187ccdf5e444cb8b3dd4b7bb4676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608010"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Skapa konfigurations regler för omfattnings resurs uppsättning

Vid skalning av data bearbetnings system lagras vanligt vis en enda tabell på en disk som flera filer. Det här konceptet representeras i Azure avdelningens kontroll med hjälp av resurs uppsättningar. En resurs uppsättning är ett enda objekt i data katalogen som representerar ett stort antal till gångar i lagring. Mer information finns i [förstå resurs uppsättningar](concept-resource-sets.md).

När du skannar ett lagrings konto använder Azure avdelningens kontroll en uppsättning definierade mönster för att avgöra om en grupp med till gångar är en resurs uppsättning. I vissa fall kan det hända att Azure avdelningens kontroll-gruppuppsättningen inte korrekt återspeglar din datafastighet. Med regler för begränsad resurs uppsättning kan du anpassa eller åsidosätta hur Azure avdelningens kontroll identifierar vilka till gångar som grupperas som resurs uppsättningar och hur de visas i katalogen.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Så här skapar du en konfiguration för en begränsad resurs uppsättning

Följ stegen nedan för att skapa en ny konfiguration av en omfångs resurs uppsättning:

1. Gå till hanterings centret. Välj **webbprogramsomfattande resurs uppsättningar** på menyn. Välj **+ ny** för att skapa en ny konfigurations regel uppsättning.

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Skapa ny regel för webbprogramsomfattande resurs uppsättning" border="true":::

1. Ange omfånget för konfigurationen av din begränsade resurs uppsättning. Välj din typ av lagrings konto och namnet på det lagrings konto som du vill skapa en regel uppsättning på. Varje uppsättning regler tillämpas i förhållande till en mappsökväg som anges i fältet **mappsökväg** .

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Skapa konfigurationer för begränsad resurs uppsättning" border="true":::

1. Om du vill ange en regel för ett konfigurations omfång väljer du **+ ny regel**.

1. Ange följande fält för att skapa en regel:

   1. **Regel namn:** Namnet på konfigurations regeln. Det här fältet har ingen inverkan på de till gångar som regeln gäller för.

   1. **Kvalificerat namn:** En kvalificerad sökväg som använder en kombination av text, dynamiska Replacements och statiska Replacements för att matcha till gångar till konfigurations regeln. Den här sökvägen är relativ i förhållande till konfigurations regelns omfattning. I avsnittet [syntax](#syntax) nedan finns detaljerade instruktioner om hur du anger kvalificerade namn.

   1. **Visnings namn:** Visnings namnet för till gången. Det här fältet är valfritt. Använd oformaterad text och statiska omplaceringar för att anpassa hur en till gång visas i katalogen. Mer detaljerade instruktioner finns i avsnittet [syntax](#syntax) nedan.

   1. **Gruppera inte som resurs uppsättning:** Om den är aktive rad kommer den matchade resursen inte att grupperas i en resurs uppsättning.

      :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Skapa en ny konfigurations regel." border="true":::

1. Spara regeln genom att klicka på **Lägg till**.

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Syntax för begränsad resurs uppsättning

När du skapar regler för omfattnings resurs uppsättning använder du följande syntax för att ange vilka till gångs regler som gäller för.

### <a name="dynamic-replacers-single-brackets"></a>Dynamiska repositioner (enkla hakparenteser)

Enkla hakparenteser används som **dynamiska återplacerare** i en regel för begränsad resurs uppsättning. Ange en dynamisk ersättnings plats i det kvalificerade namnet med hjälp av format `{<replacerName:<replacerType>}` . Om den matchas används dynamiska omplaceringar som ett grupperingsintervall som anger att till gångar ska visas som en resurs uppsättning. Om till gångarna grupperas i en resurs uppsättning skulle den kvalificerade sökvägen för resurs uppsättningen innehålla `{replacerName}` var den har angetts.

Om till exempel två till gångar `folder1/file-1.csv` och `folder2/file-2.csv` matchas till regel `{folder:string}/file-{NUM:int}.csv` skulle resurs uppsättningen vara en enda enhet `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Specialfall: dynamiska ersättningar när de inte grupperas i resurs uppsättningen

Om *ingen grupp som resurs uppsättning* är aktive rad för en regel för en begränsad resurs uppsättning, är namnet på omplaceringen ett valfritt fält. `{:<replacerType>}` är en giltig syntax. Skulle till exempel `file-{:int}.csv` kunna matcha `file-1.csv` och `file-2.csv` skapa två olika till gångar i stället för en resurs uppsättning.

### <a name="static-replacers-double-brackets"></a>Statiska ersättnings platser (dubbla hakparenteser)

Dubbla hakparenteser används som **statiska ersättnings platser** i det kvalificerade namnet för en regel för omfattning av en begränsad resurs uppsättning. Ange en statisk ersättnings plats i det kvalificerade namnet med hjälp av format `{{<replacerName>:<replacerType>}}` . Om den matchas kommer varje uppsättning unika statiska Replacement-värden att skapa olika resurs uppsättnings grupper.

Om till exempel två till gångar `folder1/file-1.csv` och `folder2/file-2.csv` matchas med regeln `{{folder:string}}/file-{NUM:int}.csv` , skapas två resurs uppsättningar `folder1/file-{NUM}.csv` och `folder2/file-{NUM}.csv` .

Statiska ersättnings platser kan användas för att ange visnings namnet för en till gång som matchar en regel för en begränsad resurs uppsättning. Om `{{<replacerName>}}` du använder i visnings namnet för en regel används det matchande värdet i till gångs namnet.

### <a name="available-replacement-types"></a>Tillgängliga ersättnings typer

Nedan visas tillgängliga typer som kan användas i statiska och dynamiska omplaceringar:

| Typ | Struktur |
| ---- | --------- |
| sträng | En serie med 1 eller flera Unicode-tecken, inklusive avgränsare som blank steg. |
| int | En serie med 1 eller fler 0-9 ASCII-tecken kan vara 0 före fast (t. ex. 0001). |
| guid | En serie med 32-eller 8-4-4-4-12-sträng representation av en UUID som defineddefa i [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| date | En serie med 6 eller 8 0-9 ASCII-tecken med alternativ avgränsare: ÅÅÅÅMMDD, åååå-mm-dd, yymmdd, yy-mm-dd, som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | En serie med 4 eller 6 0-9 ASCII-tecken med alternativ avgränsare: HHmm, HH: mm, HHmmss, HH: mm: SS som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| timestamp | En serie med 12 eller 14 0-9 ASCII-tecken med alternativ avgränsare: ÅÅÅÅ-MM-ddTHH: mm, yyyymmddhhmm, åååå-mm-ddTHH: mm: SS, yyyymmddHHmmss som anges i [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | Kan innehålla "sant" eller "falskt", inte Skift läges okänsligt. |
| antal | En serie med 0 eller fler 0-9 ASCII-tecken, det kan vara 0 prefix (t. ex. 0001) följt av en punkt "." och en serie med 1 eller flera 0-9 ASCII-tecken, den kan vara 0 som postfix (t. ex. 100) |
| hex | En serie med 1 eller flera ASCII-tecken från set 0-1 och A-F, värdet kan vara 0 före fast |
| locale | En sträng som matchar den syntax som anges i [RFC 5646](https://tools.ietf.org/html/rfc5646). |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Ordning för regler för omfattning av resurs uppsättning som tillämpas

Nedan visas ordningen för åtgärder för att tillämpa regler för begränsad resurs uppsättning:

1. Mer exakta omfattningar får företräde om en till gång matchar två regler. Till exempel gäller regler i ett omfång `container/folder` innan reglerna i omfånget används `container` .

1. Ordning för regler inom ett angivet omfång. Detta kan redige ras i UX.

1. Om en till gång inte matchar någon angiven regel gäller standard uppsättningarna för resurs uppsättningen.

## <a name="examples"></a>Exempel

### <a name="example-1"></a>Exempel 1

SAP-dataextrahering i fullständig och delta belastning

#### <a name="inputs"></a>Indata

Projektfiler

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="scoped-resource-set-rule"></a>Regel för begränsad resurs uppsättning

**Omfattning:**`https://myazureblob.blob.core.windows.net/bar/`

**Visnings namn:** Extern kund

**Kvalificerat namn:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Resurs uppsättning:** True

#### <a name="output"></a>Utdata

En resurs uppsättnings till gång

**Visnings namn:** Extern kund

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Exempel 2

IoT-data i Avro-format

#### <a name="inputs"></a>Indata

Projektfiler

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>Regler för begränsad resurs uppsättning

**Omfattning:**`https://myazureblob.blob.core.windows.net/bar/`

Regel 1

**Visnings namn:** ' Machine-89 '

**Kvalificerat namn:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Resurs uppsättning:** True

Regel 2

**Visnings namn:** ' Machine-90 '

**Kvalificerat namn:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Resurs uppsättning: true*

#### <a name="outputs"></a>Utdata

2 resurs uppsättningar

Resurs uppsättning 1

**Visnings namn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resurs uppsättning 2

**Visnings namn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Exempel 3

IoT-data i Avro-format

#### <a name="inputs"></a>Indata

Projektfiler

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Regel för begränsad resurs uppsättning

**Omfattning:**`https://myazureblob.blob.core.windows.net/bar/`

**Visnings namn:** ' Machine-{{MachineID}} '

**Kvalificerat namn:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Resurs uppsättning:** True

#### <a name="outputs"></a>Utdata

Resurs uppsättning 1

**Visnings namn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resurs uppsättning 2

**Visnings namn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Exempel 4

Gruppera inte i resurs uppsättningar

#### <a name="inputs"></a>Indata

Projektfiler

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Regel för begränsad resurs uppsättning

**Omfattning:**`https://myazureblob.blob.core.windows.net/bar/`

**Visnings namn:**`Machine-{{machineid}}`

**Kvalificerat namn:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Resurs uppsättning:** falskt

#### <a name="outputs"></a>Utdata

4 enskilda till gångar

Till gång 1

**Visnings namn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Till gång 2

**Visnings namn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Till gång 3

**Visnings namn:** machine-89

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Till gång 4

**Visnings namn:** machine-90

**Kvalificerat namn:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Nästa steg

Kom igång genom att [Registrera och skanna ett Azure Data Lake Gen2 lagrings konto](register-scan-adls-gen2.md).