---
title: Bicep-språk för Azure Resource Manager mallar
description: Beskriver bicep-språket för att distribuera infrastruktur till Azure via Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955915"
---
# <a name="what-is-bicep-preview"></a>Vad är bicep (för hands version)?

Bicep är ett språk för deklarativ distribution av Azure-resurser. Du kan använda bicep i stället för JSON för att utveckla dina Azure Resource Manager-mallar (ARM-mallar). Bicep fören klar redigerings upplevelsen genom att tillhandahålla koncis syntax, bättre stöd för kod åter användning och bättre typ säkerhet. Bicep är ett domänbaserat språk (DSL), vilket innebär att det är utformat för ett visst scenario eller en viss domän. Den är inte avsedd som ett allmänt programmeringsspråk för att skriva program.

JSON-syntaxen för att skapa mallen kan vara utförlig och kräva ett komplicerat uttryck. Bicep förbättrar upplevelsen utan att förlora någon av funktionerna i en JSON-mall. Det är en transparent abstraktion över JSON för ARM-mallar. Varje bicep-fil kompileras till en standard ARM-mall. Resurs typer, API-versioner och egenskaper som är giltiga i en ARM-mall är giltiga i en bicep-fil. Det finns några [kända begränsningar](#known-limitations) i den aktuella versionen.

Bicep är för närvarande en för hands version. Information om hur du spårar statusen för arbetet finns i [bicep-projektets lagrings plats](https://github.com/Azure/bicep).

Mer information om bicep finns i följande video.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Kom igång

Börja med bicep genom att [Installera verktygen](bicep-install.md).

När du har installerat verktygen kan du prova [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md). Själv studie serien vägleder dig genom strukturen och funktionerna i bicep. Du distribuerar bicep-filer och konverterar en ARM-mall till motsvarande bicep-fil.

Om du vill visa motsvarande JSON-och bicep-filer sida vid sida, se [bicep-Playground](https://aka.ms/bicepdemo).

Om du har en befintlig ARM-mall som du vill konvertera till bicep, se [konvertera arm-mallar mellan JSON och bicep](bicep-decompile.md).

## <a name="bicep-improvements"></a>Bicep-förbättringar

Bicep erbjuder en enklare och mer koncis syntax jämfört med motsvarande JSON. Du använder inte `[...]` uttryck. I stället anropar du funktioner direkt och hämtar värden från parametrar och variabler. Du ger varje distribuerad resurs ett symboliskt namn som gör det enkelt att referera till resursen i mallen.

Följande JSON returnerar till exempel ett utdata-värde från en resurs egenskap.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Motsvarande utmatnings uttryck i bicep är enklare att skriva. I följande exempel returneras samma egenskap med det symboliska namnet **publicIP** för en resurs som definieras i mallen:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

En fullständig jämförelse av syntaxen finns i [jämföra JSON och bicep för mallar](compare-template-syntax.md).

Bicep hanterar automatiskt beroenden mellan resurser. Du kan undvika att ange `dependsOn` när det symboliska namnet för en resurs används i en annan resurs deklaration.

Med bicep kan du dela upp projektet i flera moduler.

Strukturen för bicep-filen är mer flexibel än JSON-mallen. Du kan deklarera parametrar, variabler och utdata var som helst i filen. I JSON måste du deklarera alla parametrar, variabler och utdata i motsvarande avsnitt i mallen.

VS Code-tillägget för bicep erbjuder omfattande verifiering och IntelliSense. Du kan till exempel använda tilläggets IntelliSense för att hämta egenskaper för en resurs.

## <a name="known-limitations"></a>Kända begränsningar

Följande gränser finns för närvarande:

* Det går inte att ange läge eller batchstorlek för kopierings slingor.
* Det går inte att kombinera slingor och villkor.
* Enkel rads objekt och matriser, som `['a', 'b', 'c']` inte stöds.

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

När du är klar kan du [konvertera JSON-filerna till bicep](bicep-decompile.md).

## <a name="next-steps"></a>Nästa steg

Kom igång med [självstudierna bicep](./bicep-tutorial-create-first-bicep.md).
