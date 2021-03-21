---
title: Åsidosätt EntryPoint i behållar instansen
description: Ange en kommando rad för att åsidosätta EntryPoint i en behållar avbildning när du distribuerar en Azure Container instance
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169637"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ange kommando raden i en behållar instans för att åsidosätta standard kommando rads åtgärden

När du skapar en behållar instans kan du, om du vill, ange ett kommando som åsidosätter standard kommando rads instruktionen bakade till behållar avbildningen. Detta fungerar på samma sätt som `--entrypoint` kommando rads argumentet för `docker run` .

Som att ställa in [miljövariabler](container-instances-environment-variables.md) för behållar instanser, är det praktiskt att ange en start kommando rad för batch-jobb där du måste förbereda varje behållare dynamiskt med en speciell konfiguration.

## <a name="command-line-guidelines"></a>Kommando rads rikt linjer

* Som standard anger kommando raden en *enskild process som startar utan ett gränssnitt* i behållaren. Kommando raden kan till exempel köra ett Python-skript eller en körbar fil. Processen kan ange ytterligare parametrar eller argument.

* Om du vill köra flera kommandon startar du kommando raden genom att ange en gränssnitts miljö som stöds i behållar operativ systemet. Exempel:

  |Operativsystem  |Standard gränssnitt  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpina     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Följ konventionerna i gränssnittet för att kombinera flera kommandon som ska köras i följd.

* Beroende på behållar konfigurationen kan du behöva ange en fullständig sökväg till kommando radens körbara fil eller argument.

* Ange en lämplig [princip för omstart](container-instances-restart-policy.md) för behållar instansen, beroende på om kommando raden anger en tids krävande uppgift eller en körnings aktivitet. Till exempel en omstarts princip för `Never` eller `OnFailure` rekommenderas för en körnings aktivitet. 

* Om du behöver information om standard-EntryPoint som anges i en behållar avbildning använder du kommandot [Docker avbildnings](https://docs.docker.com/engine/reference/commandline/image_inspect/) kontroll.

## <a name="command-line-syntax"></a>Kommandoradssyntax

Kommando rads koden varierar beroende på vilket Azure-API eller verktyg som används för att skapa instanserna. Om du anger en gränssnitts miljö, ser du även syntaxen för kommandosyntaxen i gränssnittet.

* [AZ container Create][az-container-create] -kommando: skicka en sträng med `--command-line` parametern. Exempel: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: skicka en sträng med `-Command` parametern. Exempel: `-Command "echo hello"`.

* Azure Portal: Ange en kommaavgränsad lista med strängar, utan citat tecken, i egenskapen **kommando åsidosättning** för container konfigurationen. Exempel: `python, myscript.py, arg1, arg2` ). 

* Resource Manager-mall eller YAML-fil eller något av Azure SDK: erna: ange kommando rads egenskapen som en sträng mat ris. Exempel: JSON-matrisen `["python", "myscript.py", "arg1", "arg2"]` i en Resource Manager-mall. 

  Om du är bekant med [Dockerfile](https://docs.docker.com/engine/reference/builder/) -syntaxen liknar det här formatet formen *exec* i instruktionen cmd.

### <a name="examples"></a>Exempel

|    |  Azure CLI   | Portalen | Mall | 
| ---- | ---- | --- | --- |
| **Enda kommando** | `--command-line "python myscript.py arg1 arg2"` | **Åsidosätt kommando**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Flera kommandon** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Åsidosätt kommando**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-exempel

Du kan t. ex. ändra beteendet för behållar avbildningen [Microsoft/ACI-WORDCOUNT][aci-wordcount] , som analyserar text i Shakespeares *Hamlet* för att hitta de ord som ofta förekommer. I stället för att analysera *Hamlet* kan du ange en kommando rad som pekar på en annan text källa.

Om du vill se resultatet av behållaren [Microsoft/ACI-WORDCOUNT][aci-wordcount] när den analyserar standard texten, kör du den med följande [AZ container Create][az-container-create] -kommando. Ingen start kommando rad har angetts, så standard behållar kommandot körs. I illustrations syfte anger det här exemplet [miljövariabler](container-instances-environment-variables.md) för att hitta de tre översta orden som är minst fem bokstäver långa:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

När behållarens tillstånd visas som *avslutad* (Använd [AZ container Visa][az-container-show] för att kontrol lera tillstånd) visar du loggen med [AZ container-loggar][az-container-logs] för att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Utdata:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Nu kan du konfigurera en andra exempel behållare för att analysera annan text genom att ange en annan kommando rad. Python-skriptet som körs av behållaren, *WORDCOUNT.py*, accepterar en URL som ett argument och bearbetar sidans innehåll i stället för standardvärdet.

Till exempel för att fastställa de tre översta orden som är minst fem bokstäver långa i *Romeo och Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Igen när behållaren har *avbrutits* visas utdata genom att Visa behållarens loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

Aktivitetsbaserade scenarier, t. ex. batchbearbetning av en stor data uppsättning med flera behållare, kan dra nytta av anpassade kommando rader vid körning. Mer information om att köra uppgiftsbaserade behållare finns i [köra behållare aktiviteter med principer för omstart](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
