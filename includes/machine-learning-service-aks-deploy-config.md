---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e17f14d3acf8d74d1715d14fbd914ee536d29931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510984"
---
Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | NA | Beräkningsmålet. För AKS måste värdet vara `aks` . |
| `autoScaler` | NA | Innehåller konfigurations element för autoskalning. Se tabellen för autoskalning. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Om autoskalning ska aktive ras för webb tjänsten. Om `numReplicas`  =  `0` , `True` i annat fall `False` . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Det minsta antal behållare som ska användas när den här webb tjänsten autoskalas. Standard, `1` . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Det maximala antal behållare som ska användas när den här webb tjänsten autoskalas. Standard, `10` . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hur ofta den automatiska skalnings tjänsten försöker skala den här webb tjänsten. Standard, `1` . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Mål användningen (i procent av 100) som autoskalning ska försöka underhålla för den här webb tjänsten. Standard, `70` . |
| `dataCollection` | NA | Innehåller konfigurations element för data insamling. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Huruvida modell data insamling ska aktive ras för webb tjänsten. Standard, `False` . |
| `authEnabled` | `auth_enabled` | Huruvida nyckel autentisering ska aktive ras för webb tjänsten. Både `tokenAuthEnabled` och `authEnabled` kan inte vara `True` . Standard, `True` . |
| `tokenAuthEnabled` | `token_auth_enabled` | Huruvida token-autentisering ska aktive ras för webb tjänsten. Både `tokenAuthEnabled` och `authEnabled` kan inte vara `True` . Standard, `False` . |
| `containerResourceRequirements` | NA | Behållare för processor-och minnes enheterna. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Antalet processor kärnor som ska allokeras för den här webb tjänsten. Appar `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Mängden minne (i GB) som ska allokeras för den här webb tjänsten. Objekt `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Om Application Insights loggning ska aktive ras för webb tjänsten. Standard, `False` . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | En tids gräns för att genomdriva för Poäng anrop till webb tjänsten. Standard, `60000` . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maximalt antal samtidiga förfrågningar per nod för den här webb tjänsten. Standard, `1` . |
| `maxQueueWaitMs` | `max_request_wait_time` | Den längsta tid som en begäran kommer att finnas kvar i Thee-kön (i millisekunder) innan ett 503-fel returneras. Standard, `500` . |
| `numReplicas` | `num_replicas` | Antalet behållare som ska allokeras för den här webb tjänsten. Inget standardvärde. Om den här parametern inte anges är autoskalning aktive rad som standard. |
| `keys` | NA | Innehåller konfigurations element för nycklar. |
| &emsp;&emsp;`primaryKey` | `primary_key` | En primär autentiserings nyckel som ska användas för den här webbtjänsten |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | En sekundär autentiserings nyckel som ska användas för den här webbtjänsten |
| `gpuCores` | `gpu_cores` | Antalet GPU-kärnor (per behållar replik) som ska allokeras för den här webbtjänsten. Standardvärdet är 1. Stöder endast heltals värden. |
| `livenessProbeRequirements` | NA | Innehåller konfigurations element för Live-avsöknings krav. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Hur ofta (i sekunder) som utsökningen av Direktmigrering ska utföras. Standardvärdet är 10 sekunder. Minimalt värde är 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Antal sekunder efter att containern har startats innan direktmigreringen avsökningar initieras. Standardvärdet är 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Antal sekunder efter vilket tids gränsen för direktmigreringen avsökningen uppnåddes. Standardvärdet är 2 sekunder. Minimalt värde är 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimi kraven på efterföljande lyckade försök för att avsökningen av Direktmigrering ska anses vara lyckad efter att ha misslyckats. Standardvärdet är 1. Minimalt värde är 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | När en POD startar och Live-avsökningen Miss lyckas, kommer Kubernetes att försöka failureThreshold gånger innan den ger upp. Standardvärdet är 3. Minimalt värde är 1. |
| `namespace` | `namespace` | Kubernetes-namnområdet som webservicen distribueras till. Upp till 63 gemena alfanumeriska tecken ("a-z", "0"-' 9 ') och bindestreck (-). Det första och sista tecknet får inte vara bindestreck. |

Följande JSON är ett exempel på en distributions konfiguration för användning med CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```