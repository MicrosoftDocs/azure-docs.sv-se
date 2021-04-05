---
title: Felsöka distribution av fjärrmodell
titleSuffix: Azure Machine Learning
description: Lär dig hur du arbetar runt, löser och felsöker några vanliga Docker-distributions fel med Azure Kubernetes-tjänsten och Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 8bec083e62bec6a0311487c1e64e780ad14f451b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518271"
---
# <a name="troubleshooting-remote-model-deployment"></a>Felsöka distribution av fjärrmodell 

Lär dig att felsöka och lösa eller kringgå vanliga fel som kan uppstå när du distribuerar en modell till Azure Container Instances (ACI) och Azure Kubernetes service (AKS) med Azure Machine Learning.

> [!NOTE]
> Om du distribuerar en modell till Azure Kubernetes service (AKS) rekommenderar vi att du aktiverar [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) för klustret. Detta hjälper dig att förstå övergripande kluster hälsa och Resursanvändning. Du kan också hitta följande resurser:
>
> * [Sök efter Resource Health händelser som påverkar ditt AKS-kluster](../aks/aks-resource-health.md)
> * [Diagnostik för Azure Kubernetes service](../aks/concepts-diagnostics.md)
>
> Om du försöker distribuera en modell till ett ohälsosamt eller överbelastat kluster förväntas det uppstå problem. Kontakta AKS-supporten om du behöver hjälp med att felsöka problem med AKS-kluster.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
* [Azure CLI](/cli/azure/install-azure-cli).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Steg för Docker-distribution av Machine Learning-modeller

När du distribuerar en modell till icke-lokal beräkning i Azure Machine Learning händer följande:

1. Dockerfile som du angav i ditt miljö objekt i din InferenceConfig skickas till molnet, tillsammans med innehållet i din käll katalog
1. Om en tidigare skapad avbildning inte är tillgänglig i behållar registret, skapas en ny Docker-avbildning i molnet och lagras i arbets ytans standard behållar register.
1. Docker-avbildningen från behållar registret laddas ned till beräknings målet.
1. Din arbets ytas standard-BLOB-butik är monterad på ditt beräknings mål, vilket ger dig åtkomst till registrerade modeller
1. Webb servern initieras genom att köra ditt Entry-skripts `init()` funktion
1. När din distribuerade modell tar emot en begäran, `run()` hanterar din funktion den begäran

Den största skillnaden när du använder en lokal distribution är att behållar avbildningen bygger på din lokala dator, vilket är anledningen till att du måste ha Docker installerat för en lokal distribution.

Att förstå dessa avancerade steg bör hjälpa dig att förstå var felen inträffar.

## <a name="get-deployment-logs"></a>Hämta distributions loggar

Det första steget vid fel sökning är att hämta distributions loggarna. Börja med att följa [anvisningarna här](how-to-deploy-and-where.md#connect-to-your-workspace) för att ansluta till din arbets yta.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Om du vill hämta loggarna från en distribuerad WebService gör du följande:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Förutsatt att du har ett objekt av typen som `azureml.core.Workspace` kallas kan `ws` du göra följande:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Felsök lokalt

Om du har problem när du distribuerar en modell till ACI eller AKS kan du distribuera den som en lokal webb tjänst. Med en lokal webbtjänst blir det enklare att felsöka problem. Om du vill felsöka en distribution lokalt kan du läsa [artikeln om lokal fel sökning](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Behållaren kan inte schemaläggas

När du distribuerar en tjänst till ett Azure Kubernetes Service-beräkningsmål försöker Azure Machine Learning schemalägga tjänsten med det begärda antalet resurser. Om det inte finns några tillgängliga noder i klustret med en lämplig mängd resurser efter 5 minuter, kommer distributionen att Miss par. Fel meddelandet är `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Du kan åtgärda det här felet genom att antingen lägga till fler noder, ändra SKU: er för dina noder eller ändra resurs kraven för din tjänst. 

Fel meddelandet indikerar vanligt vis vilken resurs du behöver mer av – om du ser ett fel meddelande som anger att `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` tjänsten kräver GPU: er och det finns tre noder i klustret som inte har tillgängliga GPU: er. Detta kan åtgärdas genom att lägga till fler noder om du använder en GPU-SKU och växlar till en GPU-aktiverad SKU om du inte eller ändrar din miljö till att inte kräva GPU: er.  

## <a name="service-launch-fails"></a>Det går inte att starta tjänsten

När avbildningen har skapats försöker systemet starta en behållare med hjälp av distributions konfigurationen. Som en del av processen för container Start `init()` anropas funktionen i bedömnings skriptet av systemet. Om det finns undantag som inte har fångats i `init()` funktionen kan du se **CrashLoopBackOff** -fel i fel meddelandet.

Använd informationen i artikeln [Granska Docker-loggen](how-to-troubleshoot-deployment-local.md#dockerlog) .

## <a name="function-fails-get_model_path"></a>Funktionen misslyckades: get_model_path ()

I `init()` funktionen i bedömnings skriptet anropas ofta funktionen [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) för att hitta en modell fil eller en mapp med modell filer i behållaren. Om inte modellfilen eller mappen hittas misslyckas funktionen. Det enklaste sättet att felsöka det här felet är att köra följande python-kod i container Shell:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Det här exemplet skriver ut den lokala sökvägen (i förhållande till `/var/azureml-app` ) i behållaren där bedömnings skriptet förväntar sig att hitta modell filen eller mappen. Sedan kan du kontrol lera om filen eller mappen verkligen är den förväntas vara.

Om du ställer in loggnings nivån på fel sökning kan det leda till att ytterligare information loggas, vilket kan vara användbart vid identifiering av felet.

## <a name="function-fails-runinput_data"></a>Funktionen misslyckades: kör (input_data)

Om tjänsten har distribuerats, men den kraschar när du skickar data till bedömnings slut punkten, kan du lägga till fel som fångar upp instruktionen i `run(input_data)` funktionen så att den returnerar ett detaljerat fel meddelande i stället. Exempel:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Obs!** fel meddelanden som returneras från `run(input_data)` anropet ska endast göras för fel söknings syfte. Av säkerhets skäl bör du inte returnera fel meddelanden på det här sättet i en produktions miljö.

## <a name="http-status-code-502"></a>HTTP-statuskod 502

En status kod för 502 visar att tjänsten har utlöst ett undantag eller kraschat i `run()` metoden för score.py-filen. Använd informationen i den här artikeln för att felsöka filen.

## <a name="http-status-code-503"></a>HTTP-statuskod 503

Distributioner av Azure Kubernetes-tjänster stöder automatisk skalning, vilket gör att repliker kan läggas till för att stödja ytterligare belastning. Autoskalning är utformat för att hantera **gradvisa** ändringar i belastningen. Om du får stora toppar i begär Anden per sekund, kan klienterna få HTTP-statuskod 503. Även om autoskalning reagerar snabbt, tar det AKS en stor del av tiden för att skapa ytterligare behållare.

Beslut om att skala upp/ned baseras på användning av aktuella behållares repliker. Det totala antalet repliker som är upptagna (bearbetning av en begäran) dividerat med det totala antalet aktuella repliker är den aktuella användningen. Om det här värdet överskrider så `autoscale_target_utilization` skapas fler repliker. Om den är lägre minskas replikerna. Beslut om att lägga till repliker är Eager och snabbt (cirka 1 sekund). Beslut att ta bort repliker är försiktigt (cirka 1 minut). Som standard är den automatiska skalnings mål användningen inställd på **70%**, vilket innebär att tjänsten kan hantera toppar i begär Anden per sekund (RPS) på **upp till 30%**.

Det finns två saker som kan hjälpa till att förhindra 503 status koder:

> [!TIP]
> Dessa två metoder kan användas individuellt eller i kombination.

* Ändra användnings nivån där autoskalning skapar nya repliker. Du kan justera användnings målet genom `autoscale_target_utilization` att ange till ett lägre värde.

    > [!IMPORTANT]
    > Den här ändringen innebär inte att repliker skapas *snabbare*. I stället skapas de med ett lägre användnings tröskelvärde. I stället för att vänta tills tjänsten är 70% Använd, och om du ändrar värdet till 30%, så skapas repliker när 30% belastning sker.
    
    Om webb tjänsten redan använder de aktuella Max replikerna och du fortfarande ser 503 status koder ökar du `autoscale_max_replicas` värdet för att öka det maximala antalet repliker.

* Ändra det minsta antalet repliker. Att öka de minsta replikerna ger en större pool som hanterar inkommande toppar.

    Om du vill öka det lägsta antalet repliker anger `autoscale_min_replicas` du ett högre värde. Du kan beräkna de repliker som krävs genom att använda följande kod och ersätta värden med värden som är speciella för ditt projekt:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Om du får begär ande toppar som är större än de nya minsta replikerna kan hantera kan du få 503s igen. När trafik till tjänsten ökar kan du till exempel behöva öka de lägsta replikerna.

Mer information om hur du ställer in `autoscale_target_utilization` , `autoscale_max_replicas` och `autoscale_min_replicas` för finns i [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) -modulens referens.

## <a name="http-status-code-504"></a>HTTP-statuskod 504

En status kod för 504 visar att tids gränsen för begäran har uppnåtts. Standardvärdet för timeout är 1 minut.

Du kan öka tids gränsen eller försöka påskynda tjänsten genom att ändra score.py för att ta bort onödiga anrop. Om de här åtgärderna inte löser problemet kan du använda informationen i den här artikeln för att felsöka score.py-filen. Koden kan vara i ett tillstånd som inte svarar eller en oändlig loop.

## <a name="other-error-messages"></a>Andra fel meddelanden

Utför följande åtgärder för följande fel:

|Fel  | Lösning  |
|---------|---------|
|Bild skapande problem vid distribution av webb tjänst     |  Lägg till "pynacl = = 1.2.1" som ett pip-beroende till Conda-filen för avbildnings konfiguration       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Ändra SKU: n för virtuella datorer som används i distributionen till en som har mer minne. |
|FPGA-problem     |  Du kommer inte att kunna distribuera modeller på FPGAs förrän du har begärt och godkänts för FPGA-kvoten. Om du vill begära åtkomst fyller du i formuläret kvot förfrågan: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Avancerad fel sökning

Du kan behöva felsöka Python-koden i modelldistributionen interaktivt. Om Entry-skriptet till exempel inte fungerar och orsaken inte kan fastställas av ytterligare loggning. Genom att använda Visual Studio Code och debugpy kan du koppla till koden som körs i Docker-behållaren.

Mer information finns [i interaktiv fel sökning i vs Code guide](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Användar forum för modell distribution](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Distribuera och var](how-to-deploy-and-where.md)
* [Självstudie: träna & distribuera modeller](tutorial-train-models-with-aml.md)
* [Köra och felsöka experiment lokalt](./how-to-debug-visual-studio-code.md)