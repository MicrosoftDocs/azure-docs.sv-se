---
title: Distribuera egen värd-Gateway till Docker | Microsoft Docs
description: Lär dig hur du distribuerar en egen värdbaserad Gateway-komponent i Azure API Management till Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82205097"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuera en Azure API Management egen värd-Gateway till Docker

Den här artikeln innehåller steg för att distribuera den lokala gateway-komponenten i Azure API Management till en Docker-miljö.

> [!NOTE]
> Värd tjänster för egen värd i Docker passar bäst för utvärderings-och utvecklings användnings fall. Kubernetes rekommenderas för produktions användning. Se [det här](how-to-deploy-self-hosted-gateway-kubernetes.md) dokumentet för att lära dig hur du distribuerar en lokal gateway till Kubernetes.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa en Docker-miljö. [Docker för skriv bord](https://www.docker.com/products/docker-desktop) är ett utmärkt alternativ för utveckling och utvärdering. Se [Docker-dokumentationen](https://docs.docker.com) för information om alla Docker-versioner, deras funktioner och omfattande dokumentation på Docker.
- [Etablera en gateway-resurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Egen värd-Gateway är paketerad som en x86-64 Linux-baserad Docker-behållare.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuera den egna värdbaserade gatewayen till Docker

1. Välj **gateways** från under **distribution och infrastruktur**.
2. Välj den gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. Observera att en åtkomsttoken i text rutan **token** genererades automatiskt när du använder standard värdena för **förfallo datum** och **hemlig nyckel** . Om det behövs väljer du önskade värden i någon av eller båda kontrollerna för att generera en ny token.
4. Kontrol lera att **Docker** är markerat under **distributions skript**.
5. Klicka på **kuvert. conf** -fillänk bredvid **miljön** för att ladda ned filen.
6. Välj **Kopiera** -ikonen längst till höger i text rutan **Kör** för att kopiera Docker-kommandot till Urklipp.
7. Klistra in kommandot i terminalfönstret (eller kommando fönstret). Justera port mappningarna och behållar namnet efter behov. Observera att kommandot förutsätter att den nedladdade miljö filen finns i den aktuella katalogen.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Kör kommandot. Kommandot instruerar din Docker-miljö att köra behållaren med [behållar avbildningen](https://aka.ms/apim/sputnik/dhub) som hämtats från Microsoft container Registry och för att mappa behållarens http-(8080)-och HTTPS-portar (8081) till portarna 80 och 443 på värden.
9. Kör kommandot nedan för att kontrol lera om Gateway-behållaren körs:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Gå tillbaka till Azure Portal, klicka på **Översikt** och kontrol lera att den lokala gateway-behållaren som du precis har distribuerat rapporterar en felfri status.

![Gateway-status](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Använd <code>console docker container logs <gateway-name></code> kommandot för att visa en ögonblicks bild av Gateway-loggen med egen värd.
>
> Använd <code>docker container logs --help</code> kommandot för att visa alla logg visnings alternativ.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md).
* [Konfigurera eget domän namn för den egna värdbaserade gatewayen](api-management-howto-configure-custom-domain-gateway.md).
