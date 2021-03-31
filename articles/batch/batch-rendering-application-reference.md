---
title: Använda åter givnings program
description: Så här använder du åter givnings program med Azure Batch. Den här artikeln innehåller en kort beskrivning av hur du kör varje åter givnings program.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964880"
---
# <a name="rendering-applications"></a>Återge program

Åter givnings program används genom att skapa batch-jobb och uppgifter. Kommando rads egenskapen för aktiviteten anger lämplig kommando rad och parametrar.  Det enklaste sättet att skapa jobb aktiviteter är att använda Batch Explorer mallar som anges i [den här artikeln](./batch-rendering-using.md#using-batch-explorer).  Mallarna kan visas och ändrade versioner skapas vid behov.

Den här artikeln innehåller en kort beskrivning av hur du kör varje åter givnings program.

## <a name="rendering-with-autodesk-3ds-max"></a>Åter givning med Autodesk 3ds Max

### <a name="renderer-support"></a>Stöd för åter givning

Förutom de åter givningar som är inbyggda i 3ds Max, är följande åter givningar tillgängliga på avbildningarna för rendering av virtuella datorer och kan refereras till av en 3DS Max-scen fil:

* Autodesk Arnold
* Kaos Group V-Ray

### <a name="task-command-line"></a>Kommando rad för aktivitet

Anropa `3dsmaxcmdio.exe` programmet för att utföra kommando rads åter givning på en pool-nod.  Det här programmet finns på sökvägen när aktiviteten körs. `3dsmaxcmdio.exe`Programmet har samma tillgängliga parametrar som `3dsmaxcmd.exe` programmet, som dokumenteras i [hjälp dokumentationen för 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (rendering | Command-Line rendering).

Exempel:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Obs!

* Du måste vara mycket försiktig för att se till att till gångs filen hittas.  Se till att Sök vägarna är korrekta och relativa i fönstret **till gångs spårning** , eller Använd `-bitmapPath` parametern på kommando raden.
* Se om det finns problem med renderingen, t. ex. möjligheten att hitta till gångar, genom att kontrol lera `stdout.txt` filen som skrivits av 3ds Max när aktiviteten körs.

### <a name="batch-explorer-templates"></a>Batch Explorer mallar

Pool-och jobbmallar kan nås från **galleriet** i batch Explorer.  Mallens källfiler är tillgängliga i [batch Explorer data centralen på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Rendera med Autodesk Maya

### <a name="renderer-support"></a>Stöd för åter givning

Förutom de åter givningar som är inbyggda i Maya, finns följande åter givningar tillgängliga på avbildningarna för rendering av virtuella datorer och kan refereras till av den 3ds Max-scen filen:

* Autodesk Arnold
* Kaos Group V-Ray

### <a name="task-command-line"></a>Kommando rad för aktivitet

`renderer.exe`Kommando rads åter givning används i aktivitetens kommando rad. Kommando rads renderaren finns dokumenterad i [Maya-hjälpen](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

I följande exempel används en jobb förberedelse uppgift för att kopiera scenens filer och till gångar till arbets katalogen för jobb förberedelse, en målmapp används för att lagra åter givnings avbildningen och bild ruta 10 återges.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För V-Ray-rendering skulle Maya scen-filen normalt ange V-Ray som renderare.  Den kan också anges på kommando raden:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

För Arnold rendering skulle Maya scen filen normalt ange Arnold som renderare.  Den kan också anges på kommando raden:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer mallar

Pool-och jobbmallar kan nås från **galleriet** i batch Explorer.  Mallens källfiler är tillgängliga i [batch Explorer data centralen på GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Nästa steg

Använd pool-och jobbmallar från [Data lagringen i GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) med hjälp av batch Explorer.  När det behövs kan du skapa nya mallar eller ändra en av de angivna mallarna.
