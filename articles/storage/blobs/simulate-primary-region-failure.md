---
title: Självstudie – simulera ett problem med att läsa data från den primära regionen
titleSuffix: Azure Storage
description: Simulera ett fel vid läsning av data från den primära regionen när lagrings kontot har kon figurer ATS för Read-Access geo-Zone-redundant lagring (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91280359"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Självstudie: simulera ett problem med att läsa data från den primära regionen

Den här självstudien är del två i en serie. I den här artikeln får du lära dig om fördelarna med [Read-Access geo-Zone-redundant lagring](../common/storage-redundancy.md) (ra-GZRS) genom att simulera ett haveri.

Du kan använda antingen [statiska routnings](#simulate-a-failure-with-an-invalid-static-route) -eller [Fiddler](#simulate-a-failure-with-fiddler)för att simulera ett haveri. Med båda metoderna kan du simulera misslyckade begär anden till den primära slut punkten för det [geo-redundanta](../common/storage-redundancy.md) lagrings kontot med Läs åtkomst (ra-GZRS), vilket leder till att programmet läser från den sekundära slut punkten i stället.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulera ett fel med [en ogiltig statisk väg](#simulate-a-failure-with-an-invalid-static-route) eller [Fiddler](#simulate-a-failure-with-fiddler)
> * Simulerar en återställning av den primära slutpunkten

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien slutför du den föregående själv studie kursen: [gör dina program data hög tillgängliga med Azure Storage][previous-tutorial].

Om du vill simulera ett haveri med statisk routning använder du en upphöjd kommando tolk.

Om du vill simulera ett haveri med Fiddler kan du hämta och [Installera Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulera ett fel med en felaktig statisk väg

Du kan skapa en ogiltig statisk väg för alla begär anden till den primära slut punkten för det [geo-redundanta](../common/storage-redundancy.md) lagrings kontot med Läs åtkomst (ra-GZRS). I den här kursen används den lokala värden som gateway för dirigering av begäranden till lagringskontot. När den lokala värden används som gateway kommer alla begäranden till den primära slutpunkt för lagringskontot att utföra en loopback i värden, vilket resulterar i ett fel. Följ stegen nedan för att simulera ett fel och en återställning av den primära slutpunkten med en felaktig statisk väg.

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Följ anvisningarna i [föregående självstudie][previous-tutorial] för att starta exemplet och hämta test filen, vilket bekräftar att den kommer från primär lagring. Beroende på din mål plattform kan du sedan manuellt pausa exemplet eller vänta i en prompt.

### <a name="simulate-failure"></a>Simulera ett fel

När programmet har pausats öppnar du en kommando tolk i Windows som administratör eller kör Terminal som rot på Linux.

Hämta information om den primära slut punkts domänen för lagrings kontot genom att ange följande kommando i en kommando tolk eller Terminal, och Ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagrings konto.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopiera IP-adressen för ditt lagringskonto till en textredigerare för senare användning.

För att hämta IP-adressen för din lokala värd skriver du `ipconfig` i kommandotolken i Windows eller `ifconfig` i terminalen i Linux.

Om du vill lägga till en statisk väg för en målvärd skriver du följande kommando i kommando tolken i Windows eller Linux-terminalen, ersätter `<destination_ip>` med ditt lagrings kontos IP-adress och `<gateway_ip>` med din lokala värd-IP-adress.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

I fönstret med det aktiva exemplet, återuppta programmet eller tryck på lämplig nyckel för att ladda ned exempel filen och bekräfta att den kommer från sekundär lagring. Du kan sedan Pausa exemplet igen eller vänta i prompten.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Om du vill simulera den primära slut punkten när den fungerar igen tar du bort den ogiltiga statiska vägen från routningstabellen. Detta innebär att alla begäranden till den primära slutpunkten dirigeras via standardgatewayen. Skriv följande kommando i en Windows-kommandotolk eller Linux-terminal.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Du kan sedan återuppta programmet eller trycka på lämplig nyckel för att ladda ned exempel filen igen, den här gången bekräftar att den återigen kommer från primär lagring.

## <a name="simulate-a-failure-with-fiddler"></a>Simulera ett fel med Fiddler

För att simulera fel med Fiddler, injicera du ett misslyckat svar för begär anden till den primära slut punkten för ditt RA-GZRS-lagrings konto.

Följande avsnitt visar hur du simulerar ett fel samt återställning av den primära slutpunkten med Fiddler.

### <a name="launch-fiddler"></a>Starta Fiddler

Öppna Fiddler, välj **Rules** (Regler) och **Customize Rules** (Anpassa regler).

![Anpassa Fiddler-regler](media/simulate-primary-region-failure/figure1.png)

Fiddler ScriptEditor startas och visar filen **SampleRules.js**. Den här filen används för att anpassa Fiddler.

Klistra in följande kod exempel i `OnBeforeResponse` funktionen och Ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagrings konto. Beroende på exemplet kan du också behöva ersätta `HelloWorld` med namnet på test filen (eller ett prefix som `sampleFile` ) som hämtas. Den nya koden är kommenterad för att säkerställa att den inte körs direkt.

När det är klart väljer du **File** (Arkiv) och **Save** (Spara) för att spara ändringarna. Lämna fönstret ScriptEditor öppet för användning i följande steg.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Klistra in den anpassade regeln](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Följ anvisningarna i [föregående självstudie][previous-tutorial] för att starta exemplet och hämta test filen, vilket bekräftar att den kommer från primär lagring. Beroende på din mål plattform kan du sedan manuellt pausa exemplet eller vänta i en prompt.

### <a name="simulate-failure"></a>Simulera ett fel

När programmet är pausat växlar du tillbaka till Fiddler och tar bort kommentaren till den anpassade regeln som du sparade i `OnBeforeResponse` funktionen. Se till att välja **Arkiv** och **Spara** för att spara ändringarna så att regeln börjar gälla. Den här koden söker efter begär anden till det RA-GZRS lagrings kontot och, om sökvägen innehåller namnet på exempel filen, returnerar svars koden för `503 - Service Unavailable` .

I fönstret med det aktiva exemplet, återuppta programmet eller tryck på lämplig nyckel för att ladda ned exempel filen och bekräfta att den kommer från sekundär lagring. Du kan sedan Pausa exemplet igen eller vänta i prompten.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Ta bort eller kommentera den anpassade regeln igen i Fiddler. Välj **fil** och **Spara** för att se till att regeln inte längre tillämpas.

I fönstret med det aktiva exemplet, återuppta programmet eller tryck på lämplig nyckel för att ladda ned exempel filen och bekräfta att den kommer från den primära lagrings platsen en gång till. Du kan sedan avsluta exemplet.

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien lärde du dig hur du simulerar ett fel för att testa läsåtkomst för geo-redundant lagring.

Läs mer om hur RA-GZRS-lagring fungerar, samt dess associerade risker, i följande artikel:

> [!div class="nextstepaction"]
> [Designa HA-appar med RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
