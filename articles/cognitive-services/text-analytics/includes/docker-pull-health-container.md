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
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965177"
---
Fyll i och skicka [formuläret för Cognitive Services begäran](https://aka.ms/csgate) för att begära åtkomst till textanalys för offentlig för hands version av hälso tillstånd.  Det här programmet gäller för både behållaren och den offentliga för hands versionen av webb-API: et.
Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållar registret.

> [!IMPORTANT]
> * I formuläret måste du använda en e-postadress som är kopplad till ett Azure-prenumerations-ID.
> * Den Azure-resurs som du använder för att köra behållaren måste ha skapats med det godkända Azure-prenumerations-ID: t. 
> * Kontrol lera din e-postadress (både Inkorgen och skräppostmappen) för uppdateringar av status för ditt program från Microsoft.

När det har godkänts skickas ett e-postmeddelande med autentiseringsuppgifterna för att komma åt det privata behållar registret.  Använd kommandot Docker Login med autentiseringsuppgifter som anges i ditt onboarding-e-postmeddelande för att ansluta till vårt privata behållar register för Cognitive Services behållare.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned den här behållar avbildningen från vårt privata behållar register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
