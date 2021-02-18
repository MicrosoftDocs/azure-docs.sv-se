---
title: Distribuera Prometheus-instans i Azure Red Hat OpenShift-kluster
description: Skapa en Prometheus-instans i ett Azure Red Hat OpenShift-kluster för att övervaka ditt programs mått.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, Aro, OpenShift, Metrics, Red Hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633452"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Distribuera en fristående Prometheus-instans i ett Azure Red Hat OpenShift-kluster

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka 30 juni 2022. Stöd för att skapa nya Azure Red Hat OpenShift 3,11-kluster fortsätter till och med 30 november 2020. Efter pensionering kommer de återstående Azure Red Hat OpenShift 3,11-klustren att stängas av för att förhindra säkerhets problem.
> 
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:arofeedback@microsoft.com).

Den här artikeln beskriver hur du konfigurerar en fristående Prometheus-instans som använder tjänst identifiering i ett kluster med ett i Azure Red Hat.

> [!NOTE]
> Kund administratörs åtkomst till Azure Red Hat OpenShift-kluster krävs inte.

Mål konfiguration:

- Ett projekt (Prometheus-projekt) som innehåller Prometheus och Alertmanager.
- Två projekt (app-project1 och app-Project2) som innehåller de program som ska övervakas.

Du kommer att förbereda vissa Prometheus-konfigurationsfiler lokalt. Skapa en ny mapp för att lagra dem. Konfigurationsfiler lagras i klustret som hemligheter, om hemliga token läggs till senare i klustret.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Logga in på klustret med hjälp av verktyget OC

1. Öppna en webbläsare och gå sedan till webb konsolen för klustret ( https://openshift .*slumpmässigt ID*. *region*. azmosa.IO).
2. Logga in med dina Azure autentiseringsuppgifter.
3. Välj ditt användar namn i det övre högra hörnet och välj sedan **Kopiera inloggnings kommando**.
4. Klistra in ditt användar namn i den terminal som du ska använda.

> [!NOTE]
> Kör kommandot för att se om du är inloggad på rätt kluster `oc whoami -c` .

## <a name="prepare-the-projects"></a>Förbereda projekten

Kör följande kommandon för att skapa projekten:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Du kan antingen använda `-n` parametern eller `--namespace` eller välja ett aktivt projekt genom att köra `oc project` kommandot.

## <a name="prepare-the-prometheus-configuration-file"></a>Förbereda konfigurations filen för Prometheus
Skapa en Prometheus. YML-fil genom att ange följande innehåll:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Skapa en hemlighet som heter Prom genom att ange följande konfiguration:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Filen Prometheus. yml är en grundläggande Prometheus-konfigurationsfil. Den anger intervall och konfigurerar automatisk identifiering i tre projekt (Prometheus-Project, app-project1, app-Project2). I den tidigare konfigurations filen kasseras de automatiskt identifierade slut punkterna via HTTP utan autentisering.

Mer information om hur du klipper slut punkter finns i [Prometheus Scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Förbereda konfigurations filen för Alertmanager
Skapa en alertmanager. YML-fil genom att ange följande innehåll:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Skapa en hemlighet som heter Prom-Alerts genom att ange följande konfiguration:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml är aviserings hanterarens konfigurations fil.

> [!NOTE]
> Kör kommandot för att kontrol lera de två föregående stegen `oc get secret -n prometheus-project` .

## <a name="start-prometheus-and-alertmanager"></a>Starta Prometheus och Alertmanager
Gå till [OpenShift/Origin-lagringsplatsen](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) och ladda ned mallen [Prometheus-standalone. yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) . Använd mallen för Prometheus-Project genom att ange följande konfiguration:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus-standalone. yaml-filen är en OpenShift-mall. Den skapar en Prometheus-instans med OAuth-proxy framför den och en Alertmanager-instans, som också skyddas med OAuth-proxy. I den här mallen konfigureras OAuth-proxy för att tillåta alla användare som kan "Hämta" Prometheus-projektets namnrymd (se `-openshift-sar` flaggan).

> [!NOTE]
> Du kan kontrol lera om Prom-StatefulSet har samma önskade och aktuella nummer repliker genom att köra `oc get statefulset -n prometheus-project` kommandot. Om du vill kontrol lera alla resurser i projektet kör du `oc get all -n prometheus-project` kommandot.

## <a name="add-permissions-to-allow-service-discovery"></a>Lägg till behörigheter för att tillåta tjänst identifiering

Skapa en Prometheus-sdrole. YML-fil genom att ange följande innehåll:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Om du vill använda mallen för alla projekt som du vill tillåta tjänst identifiering från, kör du följande kommandon:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Du kan kontrol lera att roll-och RoleBinding har skapats korrekt genom att köra `oc get role` `oc get rolebinding` kommandona och.

## <a name="optional-deploy-example-application"></a>Valfritt: Distribuera exempel program

Allt fungerar, men det finns inga mått källor. Gå till Prometheus-URL: en ( https://prom-prometheus-project.apps .*slumpmässigt ID*. *region*. azmosa.IO/). Du hittar det genom att använda följande kommando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Kom ihåg att lägga till https://-prefixet i början av värd namnet.

På sidan **Status > tjänst identifiering** visas 0/0 aktiva mål.

Om du vill distribuera ett exempel program som visar grundläggande python-mått under/Metrics-slutpunkten kör du följande kommandon:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nya programmen bör visas som giltiga mål på sidan för identifiering av tjänsten inom 30 sekunder efter distributionen.

Om du vill ha mer information väljer du **status**  >  **mål**.

> [!NOTE]
> För varje korrekt inkasserat mål lägger Prometheus till en data punkt i upp-måttet. Välj **Prometheus** i det övre vänstra hörnet, ange **upp** som-uttrycket och välj sedan **Kör**.

## <a name="next-steps"></a>Nästa steg

Du kan lägga till anpassad Prometheus-instrumentering i dina program. Prometheus-klient biblioteket, som fören klar användningen av Prometheus-mått, är redo för olika programmeringsspråk.

Mer information finns i följande GitHub-bibliotek:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Kör](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
