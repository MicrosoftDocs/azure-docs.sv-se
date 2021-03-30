---
title: Felsöka Server grupper för PostgreSQL-storskaliga
description: Felsök PostgreSQL-Server grupper med en Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320219"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Felsöka PostgreSQL för storskaliga Server grupper
I den här artikeln beskrivs några tekniker som du kan använda för att felsöka Server gruppen. Förutom den här artikeln kanske du vill läsa om hur du använder [Kibana](monitor-grafana-kibana.md) för att söka i loggarna eller använda [Grafana](monitor-grafana-kibana.md) för att visualisera mått för Server gruppen. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Få mer information om körningen av ett azdata-kommando
Du kan lägga till parametern **--Debug** för alla azdata-kommandon som du kör. Om du gör det visas ytterligare information om körningen av det kommandot i konsolen. Du bör ha nytta av att få information som hjälper dig att förstå hur kommandot fungerar.
Du kan till exempel köra
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

eller
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Dessutom kan du använda parametern--Help på alla azdata-kommandon för att visa viss hjälp, lista över parametrar för ett visst kommando. Exempel:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Samla in loggar för datakontrollanten och dina Server grupper
Läs artikeln om hur du [hämtar loggar för Azure Arc-aktiverade data tjänster](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktiv fel sökning med Jupyter-anteckningsböcker i Azure Data Studio
Notebooks kan dokumentera procedurer genom att inkludera markdown-innehåll som beskriver vad du ska göra/hur du ska göra. De kan även innehålla körbar kod för att automatisera en procedur.  Det här mönstret är användbart för allt från standarddriftprocedurer till felsökningsguider.

Vi kan till exempel felsöka en PostgreSQL-Server grupp som kan ha problem med att använda Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Installera verktygen

Installera Azure Data Studio `kubectl` och [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] på klient datorn som du använder för att köra antecknings boken i Azure Data Studio. Det gör du genom att följa anvisningarna i [Installera klient verktyg](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Uppdatera PATH-miljövariabeln

Se till att dessa verktyg kan anropas från var som helst på klient datorn. Till exempel på en Windows-klientdator uppdaterar du sökvägen systemets miljö variabel och lägger till den mapp där du installerade kubectl.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Logga in med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Logga in på din ARC-datakontrollant från den här klient datorn och innan du startar Azure Data Studio. Det gör du genom att köra ett kommando som:

```console
azdata login --endpoint https://<IP address>:<port>
```

Ersätt `<IP address>` med IP-adressen för ditt Kubernetes-kluster och `<port>` porten som Kubernetes lyssnar på. Du uppmanas att ange användar namn och lösen ord. Om du vill se mer information kör du: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Logga in på ditt Kubernetes-kluster med kubectl

Det kan du göra genom att använda exempel kommandona som finns i [det här](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blogg inlägget.
Du kör kommandon som:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Fel söknings antecknings boken

Starta Azure Data Studio och öppna fel söknings antecknings boken. 

Implementera stegen som beskrivs i  [033-Manage-postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) för att:

1. Anslut till data styrenheten för bågen
2. Högerklicka på postgres-instansen och välj **[hantera]**
3. Välj **instrument panelen [diagnostisera och lösa problem]**
4. Välj **länken [Felsök]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – öppna PostgreSQL fel söknings antecknings bok":::

**TSG100 – Azure-bågen aktive rad postgresql-felsöknings antecknings bok** öppnas: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – använda postgresql fel söknings dator":::

#### <a name="run-the-scripts"></a>Kör skripten
Välj knappen Kör alla längst upp för att köra antecknings boken på en gång, eller så kan du gå igenom och köra varje kod cell en i taget.

Visa utdata från körningen av kod cellerna för eventuella möjliga problem.

Vi kommer att lägga till mer information i antecknings boken över tid om hur du identifierar vanliga problem och hur du kan lösa dem.

## <a name="next-step"></a>Nästa steg
- Läs om hur du [hämtar loggar för Azure Arc-aktiverade data tjänster](troubleshooting-get-logs.md)
- Läs om att [söka i loggar med Kibana](monitor-grafana-kibana.md)
- Läs om [övervakning med Grafana](monitor-grafana-kibana.md)
- Skapa egna antecknings böcker
