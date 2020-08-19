---
title: Miljövariabler för avvikelse detektor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 4a44df3f2108421c73c161d8f2df37330b9c2d14
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980231"
---
### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

>[!NOTE]
> Slut punkterna för icke-testresurser som skapats efter den 1 juli 2019 använder det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med din nyckel och slut punkt från den resurs som du har skapat skapar du två miljövariabler för autentisering:

* `ANOMALY_DETECTOR_KEY` – Resurs nyckeln för autentisering av dina begär Anden.
* `ANOMALY_DETECTOR_ENDPOINT` – Resurs slut punkten för att skicka API-begäranden. Resultatet ser ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ anvisningarna för ditt operativ system.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Starta om konsol fönstret när du har lagt till miljövariabeln.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din `.bash_profile` och Lägg till miljövariabeln:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

***