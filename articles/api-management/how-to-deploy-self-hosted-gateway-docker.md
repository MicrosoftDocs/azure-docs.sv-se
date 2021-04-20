---
title: Distribuera en gateway med egen värd till Docker | Microsoft Docs
description: Lär dig hur du distribuerar en gatewaykomponent med egen värd i Azure API Management Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725619"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuera en Azure API Management gateway med egen värd till Docker

Den här artikeln innehåller stegen för att distribuera en gatewaykomponent med egen värd i Azure API Management till en Docker-miljö.

> [!NOTE]
> Att vara värd för en gateway med egen värd i Docker passar bäst för användningsfall för utvärdering och utveckling. Kubernetes rekommenderas för produktionsanvändning. I [det här](how-to-deploy-self-hosted-gateway-kubernetes.md) dokumentet lär du dig hur du distribuerar en gateway med egen värd till Kubernetes.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa en Docker-miljö. [Docker för Desktop](https://www.docker.com/products/docker-desktop) är ett bra alternativ för utveckling och utvärdering. Se [Docker-dokumentationen](https://docs.docker.com) för information om alla Docker-versioner, deras funktioner och omfattande dokumentation om Själva Docker.
- [Etablera en gatewayresurs i din API Management instans](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Gateway med egen värd paketeras som en x86-64 Linux-baserad Docker-container.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuera gatewayen med egen värd till Docker

1. Välj **Gatewayer** under **Distribution och infrastruktur.**
2. Välj den gatewayresurs som du vill distribuera.
3. Välj **Distribution.**
4. Observera att en åtkomsttoken i **textrutan Token** genererades automatiskt med standardvärdena **Förfallo-** och **Hemlig** nyckel. Om det behövs väljer du önskade värden i en eller båda kontrollerna för att generera en ny token.
5. Kontrollera att **Docker** är valt under **Distributionsskript**.
6. Välj **fillänken env.conf** bredvid **Miljön för** att ladda ned filen.
7. Välj  kopieringsikonen till höger i **textrutan Kör** för att kopiera Docker-kommandot till Urklipp.
8. Klistra in kommandot i terminalfönstret (eller kommandofönstret). Justera portmappningarna och containernamnet efter behov. Observera att kommandot förutsätter att den nedladdade miljöfilen finns i den aktuella katalogen.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Kör kommandot. Kommandot instruerar Docker-miljön att köra [](https://aka.ms/apim/sputnik/dhub) containern med containeravbildningen som hämtats från Microsoft Container Registry och att mappa containerns HTTP-portar (8080) och HTTPS-portar (8081) till portarna 80 och 443 på värden.
10. Kör kommandot nedan för att kontrollera om gatewaycontainern körs:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Gå tillbaka vill Azure Portal klickar du på  Översikt och bekräftar att en gatewaycontainer med egen värd som du precis har distribuerat rapporterar en felfri status.

    ![gateway-status](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Använd <code>console docker container logs <gateway-name></code> kommandot för att visa en ögonblicksbild av gatewayloggen med egen värd.
>
> Använd <code>docker container logs --help</code> kommandot för att se alla loggvisningsalternativ.

## <a name="next-steps"></a>Nästa steg

* Mer information om gatewayen med egen värd finns i Översikt API Management Azure [API Management gateway med egen värd.](self-hosted-gateway-overview.md)
* [Konfigurera ett anpassat domännamn för gatewayen med egen värd.](api-management-howto-configure-custom-domain-gateway.md)
