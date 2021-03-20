---
title: Bädda in Internet Analyzer-klienten | Microsoft Docs
description: I den här artikeln får du lära dig hur du bäddar in Internet Analyzer JavaScript-klienten i ditt program.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84744059"
---
# <a name="embed-the-internet-analyzer-client"></a>Bädda in Internet Analyzer-klienten

Den här artikeln visar hur du bäddar in JavaScript-klienten i ditt program. Installationen av den här klienten är nödvändig för att köra tester och ta emot styrkorts analyser. **Den profilbaserade JavaScript-klienten tillhandahålls efter att det första testet har kon figurer ATS.** Därifrån kan du fortsätta att lägga till eller ta bort tester i profilen utan att behöva bädda in ett nytt skript. Mer information om Internet Analyzer finns i [översikten](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Internet Analyzer kräver åtkomst till Azure och andra Microsoft-tjänster för att fungera korrekt. Tillåt nätverks åtkomst till `fpc.msedge.net` och alla förkonfigurerade slut punkts-URL: er (synliga genom [CLI](internet-analyzer-cli.md)) innan du bäddar in klienten.

## <a name="find-the-client-script-url"></a>Hitta klient skriptets URL

Skript-URL: en kan hittas antingen via Azure Portal eller Azure CLI när ett test har kon figurer ATS. Mer information finns i [skapa en Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).

Alternativ 1. I Azure Portal använder du [den här länken](https://aka.ms/InternetAnalyzerPreviewPortal) för att öppna sidan förhandsgranska Portal för Azure Internet Analyzer. Gå till din Internet Analyzer-profil för att se skript-URL: en genom att gå till **inställningar > konfiguration**.

Alternativ 2. Använd Azure CLI för att kontrol lera `scriptFileUri` egenskapen.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Klient information

Skriptet genereras specifikt för din profil och dina tester. När det har lästs in körs skriptet på en fördröjning på 2 sekunder. Först kontaktar tjänsten Internet Analyzer för att hämta listan över slut punkter som kon figurer ATS i dina tester. Sedan körs mätningarna och överför tillbaka de tidsbaserade resultaten till Internet Analyzer-tjänsten.

## <a name="client-examples"></a>Klient exempel

De här exemplen visar några grundläggande metoder för att bädda in klientens JavaScript-skript på din webb sida eller ditt program. Vi använder `0bfcb32638b44927935b9df86dcfe397` som exempel på profil-ID för skript-URL: en.

### <a name="run-on-page-load"></a>Kör vid sid inläsning
Den enklaste metoden är att använda skript tag gen inuti meta tag-blocket. Den här taggen kör skriptet en gång per sid inläsning.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)
