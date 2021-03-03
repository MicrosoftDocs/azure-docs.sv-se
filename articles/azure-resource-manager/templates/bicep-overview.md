---
title: Bicep-språk för Azure Resource Manager mallar
description: Beskriver bicep-språket för att distribuera infrastruktur till Azure via Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747192"
---
# <a name="what-is-bicep-preview"></a>Vad är bicep (för hands version)?

Bicep är ett språk för deklarativ distribution av Azure-resurser. Det fören klar redigerings upplevelsen genom att tillhandahålla koncis syntax och bättre stöd för modulär användning och kod åter användning. Bicep är ett domänbaserat språk (DSL), vilket innebär att det är utformat för ett visst scenario eller en viss domän. Bicep är inte avsett som ett allmänt programmeringsspråk för att skriva program.

Bicep är en transparent abstraktion över Azure Resource Manager mallar (ARM-mallar). Varje bicep-fil kompileras till en standard ARM-mall. Resurs typer, API-versioner och egenskaper som är giltiga i en ARM-mall är giltiga i en bicep-fil.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Kom igång

Börja med bicep genom att [Installera verktygen](https://github.com/Azure/bicep/blob/main/docs/installing.md).

När du har installerat verktygen kan du prova [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md). Själv studie serien vägleder dig genom strukturen och funktionerna i bicep. Du distribuerar bicep-filer och konverterar en ARM-mall till motsvarande bicep-fil.

Om du vill visa motsvarande JSON-och bicep-filer sida vid sida, se [bicep-Playground](https://aka.ms/bicepdemo).

Om du har en befintlig ARM-mall som du vill konvertera till bicep, se [dekompilera JSON till bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Bicep-förbättringar

Bicep erbjuder en enklare och mer koncis syntax jämfört med motsvarande JSON. Du använder inte `[...]` uttryck. I stället anropar du functions, hämtar värden från parametrar och variabler och refererar till resurser. En fullständig jämförelse av syntaxen finns i [jämföra JSON och bicep för mallar](compare-template-syntax.md).

Bicep hanterar automatiskt beroenden mellan resurser. Du kan undvika att ange `dependsOn` när det symboliska namnet för en resurs används i en annan resurs deklaration.

Med bicep kan du dela upp projektet i flera moduler.

Strukturen för bicep-filen är mer flexibel än JSON-mallen. Du kan deklarera parametrar, variabler och utdata var som helst i filen. I JSON måste du deklarera alla parametrar, variabler och utdata i motsvarande avsnitt i mallen.

VS Code-tillägget för bicep erbjuder omfattande verifiering och IntelliSense. Tillägget har till exempel IntelliSense för att hämta egenskaper för en resurs.

## <a name="faq"></a>Vanliga frågor

**Varför ska du skapa ett nytt språk i stället för att använda en befintlig?**

Du kan tänka på bicep som en revidering av det befintliga ARM-mallens språk i stället för ett nytt språk. Syntaxen har ändrats, men huvud funktionerna och körnings miljön är desamma.

Vi ansåg att använda ett befintligt programmeringsspråk innan du utvecklar bicep. Vi beslutade att vår mål grupp skulle hitta det enklare att lära sig bicep i stället för att komma igång med ett annat språk.

**Varför inte fokusera på din energi på terraform eller annan tredjeparts infrastruktur som kod erbjudanden?**

Olika användare föredrar olika konfigurations språk och-verktyg. Vi vill se till att alla dessa verktyg ger en bra upplevelse på Azure. Bicep är en del av denna ansträngning.

Om du är nöjd med terraform finns det ingen anledning att byta. Microsoft strävar efter att se till att terraform på Azure är det bästa det kan vara.

För kunder som har valt ARM-mallar tror vi att bicep förbättrar redigerings upplevelsen. Bicep hjälper också till med över gången till kunder som inte har antagit infrastruktur som kod.

**Är bicep bara för Azure?**

Bicep är ett DSL fokuserat på distribution av kompletta lösningar till Azure. Möte som mål kräver att du arbetar med vissa API: er utanför Azure. Vi förväntar oss att tillhandahålla utöknings punkter för dessa scenarier.

**Vad händer med mina befintliga ARM-mallar?**

De fortsätter att fungera exakt som de alltid har. Du behöver inte göra några ändringar. Vi kommer att fortsätta att stödja den underliggande ARM-mallens JSON-språk. Bicep filer kompileras till JSON och den JSON skickas till Azure för distribution.

När du är klar kan du [konvertera JSON-filerna till bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Nästa steg

Kom igång med [självstudierna bicep](./bicep-tutorial-create-first-bicep.md).
