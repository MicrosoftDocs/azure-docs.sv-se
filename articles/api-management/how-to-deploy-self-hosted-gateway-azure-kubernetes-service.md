---
title: Distribuera en gateway med egen värd till Azure Kubernetes-tjänsten | Microsoft Docs
description: Lär dig hur du distribuerar den egna värdbaserade Gateway-komponenten i Azure API Management till Azure Kubernetes-tjänsten
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015229"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Distribuera till Azure Kubernetes Service

Den här artikeln innehåller steg för att distribuera en lokal gateway-komponent i Azure API Management till [Azure Kubernetes-tjänsten](https://azure.microsoft.com/services/kubernetes-service/). Om du vill distribuera en lokal gateway till ett Kubernetes-kluster ser du det här[dokumentet](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Förutsättningar

- [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- [Skapa ett Azure Kubernetes-kluster](../aks/kubernetes-walkthrough-portal.md)
- [Etablera en gateway-resurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Distribuera den egna värdbaserade gatewayen till AKS

1. Välj **gateways** från under **distribution och infrastruktur**.
2. Välj den lokal gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. Observera att en ny token i text rutan **token** genererades automatiskt när du använder standard värdena för **förfallo datum** och **hemlig nyckel** . Justera antingen eller båda om du vill och välj **generera** för att skapa en ny token.
5. Kontrol lera att **Kubernetes** är markerat under **distributions skript**.
6. Hämta filen genom att välja **<Gateway-name>. yml-** fillänk bredvid **distribution** .
7. Justera port mappningarna och behållar namnet i YML-filen efter behov.
8. Beroende på ditt scenario kan du behöva ändra [tjänst typen](../aks/concepts-network.md#services). Standardvärdet är `NodePort`.
9. Välj **kopierings** ikonen längst till höger i text rutan **distribuera** för att spara `kubectl` kommandot till Urklipp.
10. Klistra in kommandot i terminalfönstret (eller kommando fönstret). Observera att kommandot förväntar sig att den nedladdade miljö filen finns i den aktuella katalogen.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Kör kommandot. Kommandot instruerar ditt AKS-kluster att köra behållaren, med hjälp av egen värdbaserade gatewayens avbildning som hämtats från Microsoft Container Registry och för att konfigurera behållaren för att exponera HTTP (8080) och HTTPS (443)-portar.
12. Kör kommandot nedan för att kontrol lera att Gateway-Pod körs. Observera att ditt Pod-namn är annorlunda.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Kör kommandot nedan för att kontrol lera att Gateway-tjänsten körs. Observera att tjänst namnet och IP-adresserna är olika.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Gå tillbaka till Azure Portal och kontrol lera att Gateway-noden som du precis har distribuerat rapporterar felfri status.

> [!TIP]
> Använd <code>kubectl logs <gateway-pod-name></code> kommandot för att visa en ögonblicks bild av Gateway-loggen med egen värd.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Läs mer om [Azure Kubernetes-tjänsten](../aks/intro-kubernetes.md)
* Lär dig [hur du konfigurerar och beständiga loggar i molnet](how-to-configure-cloud-metrics-logs.md)
* * Lär dig [hur du konfigurerar och beständiga loggar lokalt](how-to-configure-local-metrics-logs.md)
