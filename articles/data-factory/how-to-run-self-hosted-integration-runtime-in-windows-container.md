---
title: Så här kör du Self-Hosted Integration Runtime Windows-container
description: Läs mer om hur du kör Self-Hosted Integration Runtime i Windows-containern.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478139"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Så här kör du Self-Hosted Integration Runtime Windows-container

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln förklarar hur du kör Self-Hosted Integration Runtime i Windows-containern.
Azure Data Factory levererar det officiella stödet för Windows-containern Self-Hosted Integration Runtime. Du kan ladda ned källkoden för Docker-versionen och kombinera bygg- och körningsprocessen i din egen pipeline för kontinuerlig leverans. 

## <a name="prerequisites"></a>Förutsättningar 
- [Krav för Windows-container](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker version 2.3 och senare 
- Self-Hosted Integration Runtime version 5.2.7713.1 och senare 
## <a name="get-started"></a>Kom igång 
1.  Installera Docker och aktivera Windows-container 
2.  Ladda ned källkoden från https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Ladda ned den senaste versionen av SHIR i SHIR-mappen 
4.  Öppna mappen i gränssnittet: 
```console
cd "yourFolderPath"
```

5.  Skapa Docker-avbildningen i Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Kör dockercontainern: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY är obligatoriskt för det här kommandot. NODE_NAME, ENABLE_HA och HA_PORT är valfria. Om du inte anger värdet använder kommandot standardvärden. Standardvärdet för värdet ENABLE_HA är false och HA_PORT är 8060.

## <a name="container-health-check"></a>Hälsokontroll för container 
Efter 120 sekunders startperiod körs hälsokontrollen regelbundet var 30:e sekund. Den ger containermotorn IR-hälsostatus. 

## <a name="limitations"></a>Begränsningar
För närvarande stöder vi inte nedanstående funktioner när du kör Self-Hosted Integration Runtime i Windows-containern:
- HTTP-proxy 
- Krypterad nodnodskommunikation med TLS/SSL-certifikat 
- Generera och importera säkerhetskopiering 
- Daemon-tjänst 
- Automatisk uppdatering 

### <a name="next-steps"></a>Nästa steg
- Granska [integration runtime-begrepp i Azure Data Factory](./concepts-integration-runtime.md).
- Lär dig hur [du skapar en integrationskörning med egen värd i Azure Portal](./create-self-hosted-integration-runtime.md).