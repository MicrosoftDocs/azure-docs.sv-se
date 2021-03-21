---
title: Publicerings guide för container erbjudanden i Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera behållar erbjudanden i Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741669"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Publicerings guide för Azure Container-erbjudanden

Med Azure Container får du hjälp att publicera behållar avbildningen på Azure Marketplace. Använd den här guiden för att förstå kraven för den här erbjudande typen.

Azure Container-erbjudanden är transaktions erbjudanden som distribueras och debiteras via Azure Marketplace. Det List alternativ som en användare ser är "Hämta nu".

Använd Azure containers erbjudande typ när din lösning är en Docker-behållar avbildning som har kon figurer ATS som en Kubernetes-baserad Azure Container instance.

> [!NOTE]
> En Azure Container instance är en Docker-instans av körnings tid som ger det snabbaste och enklaste sättet att köra en behållare i Azure, utan att behöva hantera några virtuella datorer och utan att behöva använda en tjänst på högre nivå. Container instances kan distribueras direkt till Azure eller dirigeras av Azure Kubernetes Services eller Azure Kubernetes service Engine.  

Microsoft har för närvarande stöd för kostnads fria och BYOL-licensierings modeller.

## <a name="container-offer-requirements"></a>Krav för container-erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöder antingen den kostnads fria eller BYOL fakturerings modellen.<br><br> |  
| Avbildning som skapats från en Dockerfile | Behållar avbildningar måste baseras på Docker-avbildnings specifikationen och skapas från en Dockerfile.<br> <br>Mer information om hur du skapar Docker-avbildningar finns i avsnittet "användning" i [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Vara värd för en Azure Container Registry-lagringsplats | Behållar avbildningar måste finnas i en Azure Container Registry-lagringsplats.<br> <br>Mer information om hur du arbetar med Azure Container Registry finns i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Bildtaggning | Behållar avbildningar måste innehålla minst en tagg (maximalt antal Taggar: 16).<br><br>Mer information om att tagga en bild finns `docker tag` på sidan på [Docker-dokumentations](https://docs.docker.com/engine/reference/commandline/tag) webbplatsen.<br><br> |  

## <a name="next-steps"></a>Nästa steg

- Information om hur du förbereder tekniska till gångar för ett behållar erbjudande finns i [Skapa Azure Container Technical-tillgångar](create-azure-container-technical-assets.md).

- Information om hur du skapar ett erbjudande för Azure-behållare finns [i skapa ett erbjudande för Azure-behållare på Azure Marketplace](create-azure-container-offer.md) för mer information.
