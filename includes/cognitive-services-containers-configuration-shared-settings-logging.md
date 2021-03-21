---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001227"
---
`Logging`Inställningarna hanterar ASP.net Core loggnings stöd för din behållare. Du kan använda samma konfigurations inställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggnings leverantörer stöds av behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` Logging-providern. Alla ASP.NET Core konfigurations inställningar och standardvärden för den här Logging-providern stöds.|
|[Felsöka](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` Logging-providern. Alla ASP.NET Core konfigurations inställningar och standardvärden för den här Logging-providern stöds.|
|[Disk](#disk-logging)|JSON-Logging-providern. Den här loggnings leverantören skriver loggdata till utgående data montering.|

Det här behållar kommandot lagrar loggnings information i JSON-format till utmatnings monteringen:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Det här behållar kommandot visar fel söknings information, föregåd av `dbug` , medan behållaren körs:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk loggning

`Disk`Logging-providern har stöd för följande konfigurations inställningar:

| Name | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs:** Värdet måste anges till `json` om du vill aktivera loggnings leverantören. Om det här värdet anges utan att även ange en utmatnings montering vid instansiering av en behållare uppstår ett fel. |
| `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) i en loggfil. När storleken på den aktuella logg filen uppfyller eller överskrider det värdet, startas en ny loggfil av Logging-providern. Om-1 anges begränsas logg filens storlek bara av den maximala fil storleken, om det finns någon, för den utgående monteringen. Standardvärdet är 1. |

Mer information om hur du konfigurerar ASP.NET Core loggnings support finns i [Inställningar fil konfiguration](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).