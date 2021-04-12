---
title: Offline-distribution
description: Offline-distribution gör att du kan hämta behållar avbildningar från ett privat behållar register i stället för att hämta från Microsoft Container Registry.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955373"
---
# <a name="offline-deployment-overview"></a>Översikt över offline-distribution

Vanligt vis hämtas behållar avbildningarna som används för att skapa Azure Arc-datakontrollanten, SQL-hanterade instanser och PostgreSQL storskaliga Server grupper direkt från Microsoft Container Registry (MCR). I vissa fall kommer den miljö som du distribuerar till inte att ha någon anslutning till Microsoft Container Registry.  I situationer som detta kan du hämta behållar avbildningar med hjälp av en dator _, som har_ åtkomst till Microsoft container Registry och sedan tagga och skicka dem till ett privat behållar register som _kan_ anslutas från miljön där du vill distribuera Azure Arc-aktiverade data tjänster.

Eftersom månads uppdateringar tillhandahålls för Azure Arc-aktiverade data tjänster och det finns ett stort antal behållar avbildningar, är det bäst att utföra den här processen för att hämta, tagga och skicka behållar avbildningar till ett privat behållar register med hjälp av ett skript.  Skriptet kan antingen automatiseras eller köras manuellt.

Du hittar ett [exempel skript](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) i Azure Arc GitHub-lagringsplatsen.

> [!NOTE]
> Det här skriptet kräver installation av python och [Docker CLI](https://docs.docker.com/install/).

Skriptet uppmanas interaktivt att ange följande information.  Alternativt, om du vill att skriptet ska köras utan interaktiva prompter, kan du ange motsvarande miljövariabler innan du kör skriptet.

|Prompt|Miljö variabel|Kommentarer|
|---|---|---|
|Ange käll behållar register – tryck på RETUR för att använda `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Normalt skulle du hämta avbildningarna från Microsoft Container Registry, men om du deltar i en privat för hands version med ett annat register kan du använda den information som du fick som en del av för hands versions programmet.|
|Ange käll container register lagring – tryck på RETUR för att använda `arcdata` :|SOURCE_DOCKER_REPOSITORY|Om du hämtar från Microsoft Container Registry är lagrings platsen `arcdata` .|
|Ange användar namn för käll behållar registret – tryck på RETUR för att använda ingen:|SOURCE_DOCKER_USERNAME|Ange bara ett värde om du hämtar behållar avbildningar från en källa som kräver inloggning.  Microsoft-Container Registry kräver ingen inloggning.|
|Ange lösen ord för käll behållar registret – tryck på RETUR för att använda ingen:|SOURCE_DOCKER_PASSWORD|Ange bara ett värde om du hämtar behållar avbildningar från en källa som kräver inloggning.  Microsoft-Container Registry kräver ingen inloggning. Detta är ett maskerat lösen ord visas.  Du ser inte lösen ordet om du skriver eller klistrar in det i.|
|Ange behållar avbildnings tag för avbildningarna vid källan – tryck på RETUR för att använda `<current monthly release tag>` :|SOURCE_DOCKER_TAG|Standard tag gen namnet uppdateras varje månad för att avspegla månad och år för den aktuella versionen på Microsoft Container Registry.|
|Ange DNS-namn eller IP-adress för mål container registret:|TARGET_DOCKER_REGISTRY|DNS-namn eller IP-adress för mål registret.  Det här är det register som avbildningarna ska skickas _till_.|
|Ange mål behållare för register lagrings plats:|TARGET_DOCKER_REPOSITORY|Lagrings platsen i mål registret för att skicka avbildningarna till.|
|Ange användar namn för mål behållar registret – tryck på RETUR om du vill använda ingen:|TARGET_DOCKER_USERNAME|Det användar namn som används för att logga in på mål behållar registret.|
|Ange lösen ord för mål behållar registret – tryck på RETUR för att använda ingen:|TARGET_DOCKER_PASSWORD|Det lösen ord som används för att logga in på mål behållar registret. Detta är ett maskerat lösen ord visas.  Du ser inte lösen ordet om du skriver eller klistrar in det i.|
|Ange behållar avbildnings tag gen för avbildningarna på målet:|TARGET_DOCKER_TAG|Normalt använder du samma tagg som källan för att undvika förvirring.|