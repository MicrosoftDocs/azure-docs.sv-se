---
title: Produktions beredskap och bästa praxis – Azure
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar och distribuerar live video analys på IoT Edge modul i produktions miljöer.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400564"
---
# <a name="production-readiness-and-best-practices"></a>Produktionsberedskap och bästa praxis

Den här artikeln innehåller rikt linjer för hur du konfigurerar och distribuerar live video analys på IoT Edge modul i produktions miljöer. Du bör också läsa [förbereda för att distribuera din IoT Edge-lösning i produktions](../../iot-edge/production-checklist.md) artikeln om hur du förbereder din IoT Edge-lösning. 

> [!NOTE]
> Du bör kontakta organisationernas IT-avdelningar om aspekter som rör säkerhet.

## <a name="running-the-module-as-a-local-user"></a>Köra modulen som lokal användare

När du distribuerar live video analys på IoT Edge modul till en gräns enhet körs som standard med utökade privilegier. När du gör det, `sudo iotedge logs {name-of-module}` kommer du att se följande om du kontrollerar loggarna på modulen ():

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

I avsnitten nedan diskuterar vi hur du kan åtgärda ovanstående varning.

### <a name="creating-and-using-a-local-user-account"></a>Skapa och använda ett lokalt användar konto

Du kan och köra live video analys på IoT Edge modul i produktion med hjälp av ett konto med så få behörigheter som möjligt. Följande kommandon, till exempel, visar hur du kan skapa ett lokalt användar konto på en virtuell Linux-dator:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

I distributions manifestet kan du ange LOCAL_USER_ID-och LOCAL_GROUP_ID-miljövariablerna till den icke-rot användaren och gruppen:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Bevilja behörigheter till enhets lagring

Live video analys i IoT Edge modul kräver möjlighet att skriva filer till det lokala fil systemet när:

* Genom att använda en modul med dubbla egenskaper [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , där du bör ange en katalog i det lokala fil systemet för lagring av konfigurations data.
* Genom att använda ett medie diagram för att spela in video i molnet kräver modulen att en katalog används på gräns enheten som ett cacheminne (se [kontinuerlig video inspelnings](continuous-video-recording-concept.md) artikel för mer information).
* [Registrera till en lokal fil](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)där du bör ange en fil Sök väg för den inspelade videon.

Om du tänker använda något av ovanstående bör du se till att användar kontot ovan har åtkomst till den relevanta katalogen. Överväg till exempel applicationDataDirectory. Du kan skapa en katalog på gräns enheten och länka enhets lagringen till modul Storage. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

I avsnittet Skapa alternativ för Edge-modulen i distributions manifestet kan du lägga till en bindnings inställning som mappar katalogen ("var/lokal/Media Services/") ovan till en katalog i modulen (till exempel "/var/lib/azuremediaservices/"). Och du använder den senare katalogen som värde för applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Om du tittar på exempel medie diagrammen för snabb start och självstudier, t. ex. [kontinuerlig videoinspelning](continuous-video-recording-tutorial.md), kommer du att Observera att localMediaCachePath (Media cache Directory) använder en under katalog under applicationDataDirectory. Detta är den rekommenderade metoden eftersom cacheminnet innehåller tillfälliga data.

### <a name="naming-video-assets-or-files"></a>Namnge videotillgångar eller filer

Med medie diagram kan du skapa till gångar i molnet eller MP4-filerna på gränsen. Medie till gångar kan genereras av [kontinuerlig video inspelning](continuous-video-recording-tutorial.md) eller av [händelsebaserade videoinspelningar](event-based-video-recording-tutorial.md). Även om dessa till gångar och filer kan namnges som du vill, är den rekommenderade namngivnings strukturen för kontinuerlig videoinspelnings baserad medie till gång " &lt; anytext &gt; -$ {system. GraphTopologyName}-$ {system. GraphInstanceName}".   

Ersättnings mönstret definieras av $-tecknet följt av klammerparenteser: **$ {variableName}**.  

Som exempel kan du ange assetNamePattern på till gångs mottagaren på följande sätt:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Det rekommenderade namngivnings mönstret är " &lt; anytext &gt; -$ {system. DateTime}" för Event-baserad videoinspelning – genererade till gångar. Systemvariabeln säkerställer att till gångar inte skrivs över om händelserna inträffar på samma gång. Som exempel kan du ange assetNamePattern på till gångs mottagaren på följande sätt:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Om du kör flera instanser av samma graf kan du använda grafens Topology-namn och instans namn för att skilja. Som exempel kan du ange assetNamePattern på till gångs mottagaren på följande sätt:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

För händelsebaserade videoinspelning – genererade MP4-videoklipp på gränsen bör det rekommenderade namngivnings mönstret innehålla DateTime och för flera instanser av samma graf rekommenderar att du använder systemvariablerna GraphTopologyName och GraphInstanceName. Som exempel kan du ange filePathPattern på fil mottagare på följande sätt: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Eller 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> I exemplet ovan är variabeln **fileSinkOutputName** ett exempel variabel namn som du definierar i diagram sto pol Ogin. Detta är **inte** en system variabel. 

#### <a name="system-variables"></a>Systemvariabler
Vissa systemdefinierade variabler som du kan använda är:

|System variabel|Beskrivning|Exempel|
|-----------|-----------|-----------|
|System. DateTime|UTC-datum/tid i ISO8601-filkompatibelt format (grundläggande representation YYYYMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|UTC-datum/tid i ISO8601-filkompatibelt format med millisekunder (grundläggande representation av YYYYMMDDThhmmss. SSS).|20200222T 173200.123 Z|
|System. GraphTopologyName|Användarens angivna namn för den körnings diagram sto pol Ogin.|IngestAndRecord|
|System. GraphInstanceName|Användardefinierat namn för den pågående graf-instansen.|camera001|

>[!TIP]
> System. PreciseDateTime kan inte användas när du namnger till gångar på grund av "." i namnet
### <a name="keeping-your-vm-clean"></a>Hålla den virtuella datorn ren

Den virtuella Linux-datorn som du använder som en gräns enhet kan sluta svara om den inte hanteras regelbundet. Det är viktigt att hålla cacheminnena rena, ta bort onödiga paket och även ta bort oanvända behållare från den virtuella datorn. För att göra detta är en uppsättning rekommenderade kommandon som du kan använda på din virtuella dator i Edge.

1. `sudo apt-get clean`

    Kommandot apt-get clean rensar den lokala lagrings platsen för hämtade paket filer som finns kvar i/var/cache. De kataloger som den rensar ut är/var/cache/apt/archives/och/var/cache/apt/archives/partial/. De enda filer som finns kvar i/var/cache/apt/archives är Lås filen och del katalogen del. Kommandot apt-get clean används vanligt vis för att rensa disk utrymme efter behov, vanligt vis som en del av det regelbundet schemalagda underhållet. Mer information finns i [Rensa med apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    Alternativet apt – Hämta autorengör, t. ex. apt, rensar den lokala lagrings platsen för hämtade paketfiler, men tar bara bort filer som inte längre kan hämtas och är inte användbara. Det hjälper till att hålla ditt cacheminne från att växa för stort.
1. `sudo apt-get autoremove1`

    Alternativet ta bort automatiskt tar bort paket som installerades automatiskt på grund av att ett annat paket krävdes, men med att de andra paketen tagits bort behövs de inte längre
1. `sudo docker image ls` – Innehåller en lista över Docker-avbildningar i Edge-systemet
1. `sudo docker system prune`

    Docker tar en försiktig metod för att rensa oanvända objekt (kallas ofta skräp insamling), till exempel bilder, behållare, volymer och nätverk: dessa objekt tas normalt inte bort om du inte uttryckligen ber Dockaren att göra det. Detta kan göra att Docker kan använda extra disk utrymme. För varje typ av objekt ger Dockare ett kommando för att rensa. Dessutom kan du använda Docker system Clean för att rensa flera typer av objekt samtidigt. Mer information finns i [Rensa oanvända Docker-objekt](https://docs.docker.com/config/pruning/).
1. `sudo docker rmi REPOSITORY:TAG`

    När uppdateringar sker i Edge-modulen kan dockaren ha äldre versioner av Edge-modulen som fortfarande finns. I sådana fall är det lämpligt att använda kommandot Docker RMI för att ta bort vissa avbildningar som identifieras av taggen avbildnings version.

## <a name="next-steps"></a>Nästa steg

[Snabb start: kom igång – direktsända video analyser på IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
