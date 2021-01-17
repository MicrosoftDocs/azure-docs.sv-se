---
title: Konfigurations alternativ för tal CLI – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar och hanterar konfigurationsfiler för användning med Azure tal CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540071"
---
# <a name="speech-cli-configuration-options"></a>Konfigurations alternativ för tal CLI

Tal CLI-beteendet kan vara beroende av inställningar i konfigurationsfiler, som du kan använda för att använda en `@` symbol. Tal-CLI sparar en ny inställning i en ny under `./spx/data` katalog som skapas i den aktuella arbets katalogen för tal-cli. När du söker efter ett konfigurations värde söker tal-CLI i din aktuella arbets katalog, sedan i data lagret på `./spx/data` och sedan i andra data lager, inklusive en slutgiltig skrivskyddad data lager hantering i `spx` binärfilen. 

I snabb starten av tal-CLI använde du data lagret för att spara dina `@key` och `@region` -värden, så du behöver inte ange dem med varje `spx` kommando. Tänk på att du kan använda konfigurationsfiler för att lagra dina egna konfigurations inställningar, eller till och med använda dem för att skicka URL: er eller annat dynamiskt innehåll som genereras vid körning.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Skapa och hantera konfigurationsfiler i data lagret

Det här avsnittet visar hur du använder en konfigurations fil i det lokala data lagret för att lagra och hämta kommando inställningar med `spx config` , och lagra utdata från tal CLI med `--output` alternativet.

I följande exempel rensas `@my.defaults` konfigurations filen, nyckel/värde-par för **nyckel** och **region** i filen används, och konfigurationen används i ett anrop till `spx recognize` .

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Du kan också skriva dynamiskt innehåll till en konfigurations fil. Följande kommando skapar till exempel en anpassad tal modell och lagrar URL: en för den nya modellen i en konfigurations fil. Nästa kommando väntar tills modellen på denna URL är klar att användas innan den returneras.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

I följande exempel skrivs två URL: er till `@my.datasets.txt` konfigurations filen. I det här scenariot `--output` kan du **lägga till** ett valfritt nyckelord för att skapa en konfigurations fil eller lägga till det befintliga.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Om du vill ha mer information om datalager-filer, inklusive använda standardfiler ( `@spx.default` , `@default.config` , och `@*.default.config` för kommandobaserade standardinställningar), anger du följande kommando:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Nästa steg 

* [Batch-åtgärder med tal-CLI](./spx-batch-operations.md)