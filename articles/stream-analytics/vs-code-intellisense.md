---
title: IntelliSense i Azure Stream Analytics verktyg för Visual Studio Code
description: Den här artikeln beskriver hur du använder IntelliSense-funktioner i Azure Stream Analytics verktyg för Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491578"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense i Azure Stream Analytics verktyg för Visual Studio Code

IntelliSense är tillgängligt för [Stream Analytics frågespråk](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) i [Azure Stream Analytics Tools för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense är ett stöd för kod komplettering som innehåller ett antal funktioner: list medlemmar, parameter information, snabb information och fullständig ord. IntelliSense-funktioner anropas ibland av andra namn som "kod komplettering", "innehålls stöd" och "kodtips".

![IntelliSense-demonstration](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-funktioner

IntelliSense-funktionerna i Stream Analytics verktyg för VS Code drivs av en språk tjänst. En språk tjänst analyserar din käll kod och ger intelligent kod komplettering baserad på språksemantik. Om en språk tjänst vet att det går att slutföra kan IntelliSense-förslag visas när du skriver. Om du fortsätter att skriva, filtreras en lista över medlemmar, till exempel variabler och metoder, för att bara inkludera medlemmar som innehåller de tecken som du har angett. När du trycker på `Tab` `Enter` tangenterna eller, infogar IntelliSense den medlem som du har valt.

Du kan utlösa IntelliSense i alla redigerings fönster genom att ange ett utlösnings steg, till exempel punkt symbolen `.` .

![autoifyllning av IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Widgeten förslag stöder CamelCase filtrering. Du kan ange bokstäver som är versaler i ett metod namn för att begränsa förslagen. Exempel: "CRA" kommer att snabbt ta upp "createApplication".

### <a name="types-of-completions"></a>Typer av slut för ande

I Stream Analytics Tools för VS Code-IntelliSense finns olika typer av slut för ande, inklusive förslag på språk Server, kodfragment och enkel Word-baserad text komplettering.

|Slutförande     |  Typ       |
| ----- | ------- |
| Nyckelord | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Data uppsättnings namn| `input`, `output`, `intermediate result set`|
| Kolumn namn för data uppsättning|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Namn komplettering

Förutom automatisk komplettering av nyckelord, Stream Analytics Tools för VS Code läser listan över jobb-och utdatafiler, samt namnen på kolumnerna i dina data källor när de har kon figurer ATS. Tillägget kommer ihåg den här informationen för att tillhandahålla funktioner för namn komplettering som är användbara för att lägga till instruktioner med några tangenttryckningar:

När du kodar behöver du inte lämna redigeraren för att utföra sökningar efter jobbnamn, utmatnings namn och kolumn namn. Du kan hålla ditt sammanhang, hitta den information du behöver, infoga element direkt i koden och låta IntelliSense-filen vara klar.

Observera att du måste konfigurera antingen lokal inmatning eller Live-inmatning och spara konfigurations filen för att kunna använda namn komplettering.

![namn komplettering](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Parameter information

Alternativet för IntelliSense- **parameter information** öppnar en parameter lista som innehåller information om antalet, namn och typer för de parametrar som krävs av en funktion. Parametern i fetstil anger nästa parameter som krävs när du skriver en funktion.

Parameter listan visas också för kapslade funktioner. Om du skriver en funktion som en parameter till en annan funktion visas parametrarna för den inre funktionen i parameter listan. När den inre funktions parameter listan har slutförts, återgår parameter listan till att visa de yttre funktions parametrarna.

![parameter information](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Snabb information

Som anges av språk tjänsten kan du se **snabb information** för varje identifierare i koden. Några exempel på identifierare är indata, utdata, en mellanliggande resultat uppsättning eller funktion. När du flyttar mus pekaren över en identifierare visas dess deklaration i ett popup-fönster. Egenskaperna och data scheman för indata, om det är konfigurerat, och mellanliggande data uppsättning visas.

![snabb information](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Felsöka IntelliSense

Det här problemet orsakas av att inmatnings konfigurationen som tillhandahåller data saknas. Du kan kontrol lera om en [lokal](visual-studio-code-local-run.md#define-a-local-input) indatamängd eller [Live-indatamängd](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) har kon figurer ATS korrekt.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)
* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)
* [Testa Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code](visual-studio-code-local-run-live-input.md)