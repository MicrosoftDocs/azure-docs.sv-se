---
title: Felsöka PostgreSQL Hyperscale-servergrupper
description: Felsöka PostgreSQL Hyperscale-servergrupper med en Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "92320219"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Felsöka PostgreSQL Hyperscale-servergrupper
I den här artikeln beskrivs några tekniker som du kan använda för att felsöka din servergrupp. Förutom den här artikeln kanske du vill läsa om hur du använder [Kibana](monitor-grafana-kibana.md) för att söka i loggarna eller använda [Grafana](monitor-grafana-kibana.md) för att visualisera mått om din servergrupp. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Få mer information om körningen av ett azdata-kommando
Du kan lägga till **parametern --debug till** alla azdata-kommandon som du kör. Om du gör det visas ytterligare information om körningen av kommandot i konsolen. Du bör ha nytta av att få information som hjälper dig att förstå kommandots beteende.
Du kan till exempel köra
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

eller
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Dessutom kan du använda parametern --help för alla azdata-kommandon för att visa hjälp, lista över parametrar för ett visst kommando. Exempel:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Samla in loggar för datakontrollanten och dina servergrupper
Läs artikeln om att [hämta loggar för Azure Arc-aktiverade datatjänster](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktiv felsökning med Jupyter Notebooks i Azure Data Studio
Notebooks kan dokumentera procedurer genom att inkludera markdown-innehåll som beskriver vad du ska göra/hur du ska göra. De kan även innehålla körbar kod för att automatisera en procedur.  Det här mönstret är användbart för allt från standarddriftprocedurer till felsökningsguider.

Vi kan till exempel felsöka en PostgreSQL Hyperscale-servergrupp som kan ha problem med att använda Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Installera verktygen

Installera Azure Data Studio och `kubectl` på [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] den klientdator som du använder för att köra anteckningsboken i Azure Data Studio. Det gör du genom att följa anvisningarna i [Installera klientverktyg](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Uppdatera miljövariabeln PATH

Se till att dessa verktyg kan anropas var som helst på den här klientdatorn. På en Windows-klientdator kan du till exempel uppdatera systemmiljövariabeln PATH och lägga till mappen där du installerade kubectl.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Logga in med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Logga in din Arc-datakontrollant från den här klientdatorn och innan du startar Azure Data Studio. Det gör du genom att köra ett kommando som:

```console
azdata login --endpoint https://<IP address>:<port>
```

Ersätt `<IP address>` med IP-adressen för ditt Kubernetes-kluster och `<port>` porten som Kubernetes lyssnar på. Du uppmanas att ange användarnamn och lösenord. Om du vill se mer information kör du:_

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Logga in på kubernetes-klustret med kubectl

Om du vill göra det kanske du vill använda exempelkommandona i [det här](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blogginlägget.
Du skulle köra kommandon som:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Felsöknings-notebook-datorn

Starta Azure Data Studio och öppna anteckningsboken för felsökning. 

Implementera stegen som beskrivs i  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) för att:

1. Ansluta till Arc-datakontrollanten
2. Högerklicka på din Postgres-instans och välj **[Hantera]**
3. Välj **instrumentpanelen [Diagnostisera och lösa problem]**
4. Välj länken **[Felsök]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – Open PostgreSQL troubleshooting Notebook":::

**Felsökningsanteckningsboken TSG100** – Azure Arc-aktiverad PostgreSQL i hyperskala öppnas: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – Använda postgreSQL-felsökningsanteckningsbok":::

#### <a name="run-the-scripts"></a>Kör skripten
Välj knappen Kör alla längst upp för att köra anteckningsboken samtidigt, eller så kan du stega igenom och köra varje kodcell en i steg.

Visa utdata från körningen av kodcellerna för eventuella problem.

Vi lägger till mer information i anteckningsboken över tid om hur du identifierar vanliga problem och hur du löser dem.

## <a name="next-step"></a>Nästa steg
- Läs mer [om att hämta loggar för Azure Arc-aktiverade datatjänster](troubleshooting-get-logs.md)
- Läs mer om [att söka i loggar med Kibana](monitor-grafana-kibana.md)
- Läs mer [om övervakning med Grafana](monitor-grafana-kibana.md)
- Skapa egna notebook-datorer
