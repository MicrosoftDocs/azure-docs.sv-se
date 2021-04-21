---
title: Åsidosätt startpunkten i containerinstansen
description: Ange en kommandorad för att åsidosätta startpunkten i en containeravbildning när du distribuerar en Azure-containerinstans
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771091"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ange kommandoraden i en containerinstans för att åsidosätta standardkommandoradsåtgärden

När du skapar en containerinstans kan du ange ett kommando för att åsidosätta standardkommandot för kommandoraden som finns i containeravbildningen. Det här beteendet liknar `--entrypoint` kommandoradsargumentet som `docker run` .

Precis som [när du anger miljövariabler](container-instances-environment-variables.md) för containerinstanser är det praktiskt att ange en startkommandorad för batchjobb där du måste förbereda varje container dynamiskt med uppgiftsspecifik konfiguration.

## <a name="command-line-guidelines"></a>Riktlinjer för kommandorad

* Som standard anger kommandoraden en enda *process som startar utan gränssnitt i* containern. Kommandoraden kan till exempel köra ett Python-skript eller en körbar fil. Processen kan ange ytterligare parametrar eller argument.

* Om du vill köra flera kommandon startar du kommandoraden genom att ange en gränssnittsmiljö som stöds i containeroperativsystemet. Exempel:

  |Operativsystem  |Standardgränssnitt  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Följ gränssnittskonventionerna för att kombinera flera kommandon som ska köras i följd.

* Beroende på containerkonfigurationen kan du behöva ange en fullständig sökväg till den körbara kommandoraden eller argumenten.

* Ange en lämplig [omstartsprincip](container-instances-restart-policy.md) för containerinstansen, beroende på om kommandoraden anger en långvarig uppgift eller en run-once-uppgift. En omstartsprincip för eller `Never` rekommenderas till exempel för en `OnFailure` "run-once"-uppgift. 

* Om du behöver information om standardinmatningspunkten som angetts i en containeravbildning använder du [kommandot docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Kommandoradssyntax

Kommandoradssyntaxen varierar beroende på vilket Azure-API eller verktyg som används för att skapa instanserna. Om du anger en gränssnittsmiljö ska du även observera gränssnittets kommandosyntaxkonventioner.

* [az container create][az-container-create] command: Skicka en sträng med `--command-line` parametern . Exempel: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: Skicka en sträng med `-Command` parametern . Exempel: `-Command "echo hello"`.

* Azure Portal: I egenskapen  Åsidosätt kommando för containerkonfigurationen anger du en kommaavgränsad lista med strängar, utan citattecken. Exempel: `python, myscript.py, arg1, arg2` ). 

* Resource Manager eller YAML-fil, eller någon av Azure-SDK:erna: Ange kommandoradsegenskapen som en matris med strängar. Exempel: JSON-matrisen `["python", "myscript.py", "arg1", "arg2"]` i en Resource Manager mall. 

  Om du är bekant med [Dockerfile-syntaxen](https://docs.docker.com/engine/reference/builder/) liknar det här formatet *exec-formatet* för CMD-instruktionen.

### <a name="examples"></a>Exempel

|    |  Azure CLI   | Portalen | Mall | 
| ---- | ---- | --- | --- |
| **Enskilt kommando** | `--command-line "python myscript.py arg1 arg2"` | **Åsidosättning av kommando:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Flera kommandon** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Åsidosättning av kommando:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-exempel

Du kan till exempel ändra beteendet för containeravbildningen [microsoft/aci-wordcount,][aci-wordcount] som analyserar text i Mereds *Gör* för att hitta de ord som förekommer oftast. I stället för att *analyseraDokument* kan du ange en kommandorad som pekar på en annan textkälla.

Om du vill se utdata från [containern microsoft/aci-wordcount][aci-wordcount] när den analyserar standardtexten kör du den med följande [az container create-kommando.][az-container-create] Ingen kommandorad för start har angetts, så standardkommandot för containern körs. I illustrationssyfte anger det här exemplet [miljövariabler för](container-instances-environment-variables.md) att hitta de tre översta orden som är minst fem bokstäver långa:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

När containerns tillstånd visas som *Avslutad* (använd [az container show][az-container-show] för att kontrollera tillståndet) visar du loggen med az container logs [för][az-container-logs] att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Utdata:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Konfigurera nu en andra exempelcontainer för att analysera annan text genom att ange en annan kommandorad. Python-skriptet som körs av *containern, wordcount.py*, accepterar en URL som ett argument och bearbetar sidans innehåll i stället för standardvärdet.

Till exempel för att fastställa de tre översta orden som är minst fem bokstäver långa i *Julia och Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

När containern har *avslutats visar* du utdata genom att visa containerns loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel batchbearbetning av en stor datamängd med flera containrar, kan dra nytta av anpassade kommandorader vid körning. Mer information om hur du kör uppgiftsbaserade containrar finns i [Köra containerbaserade uppgifter med omstartsprinciper.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
