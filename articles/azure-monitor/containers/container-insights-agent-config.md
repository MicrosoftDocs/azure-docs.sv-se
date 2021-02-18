---
title: Konfigurera Azure Monitor för behållare agent data insamling | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare agent för att styra logg insamling för STDOUT/stderr och miljövariabler.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625373"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurera agentdatainsamling för Azure Monitor för containrar

Azure Monitor för behållare samlar in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster från behållarens agent. Du kan konfigurera inställningar för data insamling för agenter genom att skapa en anpassad Kubernetes-ConfigMaps för att styra den här upplevelsen. 

Den här artikeln visar hur du skapar ConfigMap och konfigurerar data insamling utifrån dina krav.

>[!NOTE]
>För Azure Red Hat OpenShift skapas en mall ConfigMap-fil i *OpenShift-Azure-Logging-* namnområdet. 
>

## <a name="configmap-file-settings-overview"></a>Översikt över ConfigMap-fil inställningar

En mall ConfigMap-fil tillhandahålls som gör att du enkelt kan redigera den med dina anpassningar utan att behöva skapa den från grunden. Innan du börjar bör du gå igenom Kubernetes-dokumentationen om [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) och bekanta dig med hur du skapar, konfigurerar och distribuerar ConfigMaps. Detta gör att du kan filtrera stderr och STDOUT per namnrymd eller över hela klustret, och miljövariabler för alla behållare som körs i alla poddar/noder i klustret.

>[!IMPORTANT]
>Den lägsta agent version som stöds för att samla in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar är ciprod06142019 eller senare. Om du vill verifiera agent versionen går du till fliken **Node** och väljer en nod. i rutan Egenskaper noterar du värdet för egenskapen för **agentens avbildnings tag** . För ytterligare information om agent versionerna och vad som ingår i varje utgåva, se [agent viktig](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)information.

### <a name="data-collection-settings"></a>Inställningar för data insamling

I följande tabell beskrivs de inställningar som du kan konfigurera för att styra data insamling:

| Nyckel | Datatyp | Värde | Beskrivning |
|--|--|--|--|
| `schema-version` | Sträng (Skift läges känslig) | v1 | Det här är den schema version som används av agenten<br> vid parsning av den här ConfigMap.<br> Schema version som stöds för närvarande är v1.<br> Det finns inte stöd för att ändra det här värdet och det kommer att vara<br> avvisades när ConfigMap utvärderas. |
| `config-version` | Sträng |  | Stöder möjlighet att hålla koll på den här konfigurations filens version i käll kontroll systemet/lagrings platsen.<br> Maximalt antal tillåtna tecken är 10 och alla andra tecken trunkeras. |
| `[log_collection_settings.stdout] enabled =` | Boolesk | sant eller falskt | Kontrollerar om STDOUT container logg samling är aktive rad. När det är inställt på `true` och inga namn områden utesluts för STDOUT logg insamling<br> ( `log_collection_settings.stdout.exclude_namespaces` inställningen nedan) kommer STDOUT-loggar att samlas in från alla behållare över alla poddar/noder i klustret. Om detta inte anges i ConfigMaps<br> Standardvärdet är `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Sträng | Kommaavgränsad matris | Matris med Kubernetes-namnområden som StdOut-loggar inte ska samlas in för. Den här inställningen gäller endast om<br> `log_collection_settings.stdout.enabled`<br> är inställt på `true` .<br> Om inget värde anges i ConfigMap är standardvärdet<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boolesk | sant eller falskt | Anger om stderr container logg samling är aktive rad.<br> När det är inställt på `true` och inga namn områden utesluts för STDOUT logg insamling<br> ( `log_collection_settings.stderr.exclude_namespaces` inställning) kommer stderr-loggar att samlas in från alla behållare över alla poddar/noder i klustret.<br> Om inget värde anges i ConfigMaps är standardvärdet<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Sträng | Kommaavgränsad matris | Matris med Kubernetes-namnområden som stderr-loggar inte ska samlas in för.<br> Den här inställningen gäller endast om<br> `log_collection_settings.stdout.enabled` är inställt på `true` .<br> Om inget värde anges i ConfigMap är standardvärdet<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boolesk | sant eller falskt | Den här inställningen styr miljö variabel samlingen<br> över alla poddar/noder i klustret<br> och standardvärdet `enabled = true` när inget anges<br> i ConfigMaps.<br> Om samlingen av miljövariabler är globalt aktive rad kan du inaktivera den för en speciell behållare<br> genom att ställa in miljövariabeln<br> `AZMON_COLLECT_ENV` Om du vill ange **falskt** antingen med en Dockerfile-inställning eller i [konfigurations filen för Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) under avsnittet **kuvert:** .<br> Om insamlingen av miljövariabler är globalt inaktive rad kan du inte aktivera samling för en speciell behållare (det vill säga den enda åsidosättning som kan tillämpas på behållar nivån är att inaktivera samlingen när den redan är aktive rad globalt.). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boolesk | sant eller falskt | Den här inställningen styr anrikningen av behållar loggen till att fylla i värdena för namn och bild egenskaper<br> för varje logg post skrivs till tabellen ContainerLog för alla behållar loggar i klustret.<br> Det används som standard `enabled = false` när det inte anges i ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Boolesk | sant eller falskt | Den här inställningen tillåter insamling av Kube-händelser av alla typer.<br> Som standard samlas inte Kube-händelser av typen *Normal* in. När den här inställningen är inställd på `true` filtreras inte de *normala* händelserna längre och alla händelser samlas in.<br> Som standard är detta inställt på `false` . |

### <a name="metric-collection-settings"></a>Inställningar för mått samling

I följande tabell beskrivs de inställningar som du kan konfigurera för att styra mått samlingen:

| Nyckel | Datatyp | Värde | Beskrivning |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolesk | sant eller falskt | Med den här inställningen kan du samla in användnings mått för beständig volym (PV) i Kube-systemets namnrymd. Som standard samlas användnings statistik för beständiga volymer med beständiga volym anspråk i Kube-systemets namnrymd inte in. När den här inställningen är inställd på `true` samlas användnings statistik för nuvärdet för alla namn områden. Som standard är detta inställt på `false` . |

ConfigMaps är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Det går inte att ha en annan ConfigMaps över samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera och distribuera din ConfigMap-konfigurationsfil till klustret.

1. Hämta [mallen CONFIGMAP yaml File](https://aka.ms/container-azm-ms-agentconfig) och spara den som container-AZM-MS-agentconfig. yaml. 

   > [!NOTE]
   > Det här steget krävs inte när du arbetar med Azure Red Hat OpenShift eftersom ConfigMap-mallen redan finns i klustret.

2. Redigera ConfigMap yaml-filen med dina anpassningar för att samla in STDOUT-, stderr-och/eller miljövariabler. Om du redigerar ConfigMap yaml-filen för Azure Red Hat OpenShift ska du först köra kommandot `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` för att öppna filen i en text redigerare.

    - Om du vill undanta vissa namn rymder för STDOUT-logg samling konfigurerar du nyckeln/värdet med hjälp av följande exempel: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Om du vill inaktivera en miljö variabel samling för en speciell behållare anger du nyckel/värde `[log_collection_settings.env_var] enabled = true` för att aktivera variabel insamling globalt och följer sedan stegen [här](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) för att slutföra konfigurationen av den angivna behållaren.
    
    - Om du vill inaktivera stderr-logg samling i hela klustret konfigurerar du nyckeln/värdet med hjälp av följande exempel: `[log_collection_settings.stderr] enabled = false` .

3. För andra kluster än Azure Red Hat OpenShift skapar du ConfigMap genom att köra följande kubectl-kommando: `kubectl apply -f <configmap_yaml_file.yaml>` på andra kluster än Azure Red Hat OpenShift. 
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Spara ändringarna i redigeraren för Azure Red Hat OpenShift.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Verifiera konfigurationen

För att verifiera att konfigurationen har tillämpats på ett annat kluster än Azure Red Hat OpenShift, använder du följande kommando för att granska loggarna från en agent pod: `kubectl logs omsagent-fdf58 -n kube-system` . Om det finns konfigurations fel från omsagent-poddar visas fel som liknar följande i utdata:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel som rör tillämpning av konfigurations ändringar är också tillgängliga för granskning. Följande alternativ är tillgängliga för att utföra ytterligare fel sökning av konfigurations ändringar:

- Från en agents Pod loggar med samma `kubectl logs` kommando. 

    >[!NOTE]
    >Det här kommandot kan inte användas för Azure Red Hat OpenShift-kluster.
    > 

- Från Live-loggar. Live-loggar visar fel som liknar följande:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Från tabellen **KubeMonAgentEvents** i din Log Analytics-arbetsyta. Data skickas varje timme med *fel* allvarlighets grad för konfigurations fel. Om det inte finns några fel innehåller posten i tabellen data med allvarlighets grad *information* som inte rapporterar några fel. Egenskapen **Tags** innehåller mer information om Pod och behållar-ID: t där felet inträffade och även den första förekomsten, senaste förekomst och antal under den senaste timmen.

- Med Azure Red Hat OpenShift kontrollerar du omsagent-loggarna genom att söka i **ContainerLog** -tabellen för att kontrol lera om logg insamling av OpenShift-Azure-loggning är aktiverat.

När du har korrigerat felen i ConfigMap på andra kluster än Azure Red Hat OpenShift, sparar du yaml-filen och tillämpar den uppdaterade ConfigMaps genom att köra kommandot: `kubectl apply -f <configmap_yaml_file.yaml` . Redigera och spara den uppdaterade ConfigMaps för Azure Red Hat OpenShift genom att köra kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Använder uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap på andra kluster än Azure Red Hat OpenShift och du vill uppdatera det med en nyare konfiguration, kan du redigera ConfigMap-filen som du tidigare har använt och sedan använda samma kommando som tidigare `kubectl apply -f <configmap_yaml_file.yaml` . Redigera och spara den uppdaterade ConfigMaps för Azure Red Hat OpenShift genom att köra kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Verifierar schema version

Konfigurations schema versioner som stöds är tillgängliga som Pod-anteckning (schema versioner) på omsagent-pod. Du kan se dem med följande kubectl-kommando: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Utdata ser ut ungefär så här med antecknings schema versioner:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Nästa steg

- Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Mer information om hur du skapar rekommenderade aviseringar för hög processor-och minnes användning finns i avsnittet [skapa prestanda aviseringar med Azure Monitor för behållare](./container-insights-log-alerts.md) som stöder DevOps eller operativa processer och procedurer

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för AKS eller hybrid kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.