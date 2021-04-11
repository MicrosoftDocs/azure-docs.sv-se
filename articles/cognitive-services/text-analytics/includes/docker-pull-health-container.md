---
title: Docker-hämtning för hälso containern
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Textanalys för hälso container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089721"
---
Fyll i och skicka [formuläret för Cognitive Services begäran](https://aka.ms/csgate) för att begära åtkomst till textanalys för offentlig för hands version av hälso tillstånd.  Det här programmet gäller för både behållaren och den offentliga för hands versionen av webb-API: et.
Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållar registret.

> [!IMPORTANT]
> * I formuläret måste du använda en e-postadress som är kopplad till ett Azure-prenumerations-ID.
> * Den Textanalys resursen (fakturerings slut punkt och apiKey) som du använder för att köra behållaren måste ha skapats med det godkända Azure-prenumerations-ID: t. 
> * Kontrol lera din e-postadress (både Inkorgen och skräppostmappen) för uppdateringar av status för ditt program från Microsoft.
> * Den här behållar-URL: en har ändrats, se exemplen nedan. Behållaren är inte tillgänglig för hämtning från och med den `containerpreview.azurecr.io` 26 April 2021.


När det har godkänts använder du [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta behållar avbildningen från Microsoft Public container Registry.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
