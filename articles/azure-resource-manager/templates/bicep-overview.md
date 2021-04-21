---
title: Bicep-språk för Azure Resource Manager mallar
description: Beskriver språket Bicep för distribution av infrastruktur till Azure via Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773755"
---
# <a name="what-is-bicep-preview"></a>Vad är Bicep (förhandsversion)?

Bicep är ett språk för deklarativ distribution av Azure-resurser. Du kan använda Bicep i stället för JSON för att utveckla Azure Resource Manager mallar (ARM-mallar). Bicep förenklar redigeringen genom att tillhandahålla koncis syntax, bättre stöd för återanvändning av kod och förbättrad typsäkerhet. Bicep är ett domänspecifikt språk (DSL), vilket innebär att det är utformat för ett visst scenario eller en viss domän. Det är inte avsett som ett allmänt programmeringsspråk för att skriva program.

JSON-syntaxen för att skapa mallar kan vara utförlig och kräva komplicerade uttryck. Bicep förbättrar den upplevelsen utan att förlora någon av funktionerna i en JSON-mall. Det är en transparent abstraktion över JSON för ARM-mallar. Varje Bicep-fil kompileras till en ARM-standardmall. Resurstyper, API-versioner och egenskaper som är giltiga i en ARM-mall är giltiga i en Bicep-fil. Det finns några [kända begränsningar i](#known-limitations) den aktuella versionen.

Bicep är för närvarande i förhandsversion. Om du vill spåra status för arbetet kan du gå till [lagringsplatsen för Bicep-projektet](https://github.com/Azure/bicep).

Mer information om Bicep finns i följande video.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Kom igång

Börja med Bicep genom [att installera verktygen](bicep-install.md).

När du har installerat verktygen kan du prova [Bicep-självstudien](./bicep-tutorial-create-first-bicep.md). Självstudieserien vägled dig genom strukturen och funktionerna i Bicep. Du distribuerar Bicep-filer och konverterar en ARM-mall till motsvarande Bicep-fil.

Om du vill visa motsvarande JSON- och Bicep-filer sida vid sida kan du gå till [Bicep Playground.](https://aka.ms/bicepdemo)

Om du har en befintlig ARM-mall som du vill konvertera till Bicep, se [Konvertera ARM-mallar mellan JSON och Bicep.](bicep-decompile.md)

## <a name="benefits-of-bicep-versus-other-tools"></a>Fördelar med Bicep jämfört med andra verktyg

Bicep ger följande fördelar jämfört med andra alternativ:

* **Stöd för alla resurstyper och API-versioner:** Bicep stöder omedelbart alla förhandsversioner och GA-versioner för Azure-tjänster. Så snart en resursprovider introducerar nya resurstyper och API-versioner kan du använda dem i din Bicep-fil. Du behöver inte vänta på att verktygen ska uppdateras innan du använder de nya tjänsterna.
* **Redigeringsupplevelse:** När du använder VS Code för att skapa bicep-filer får du en förstklassig redigeringsupplevelse. Redigeraren innehåller omfattande typsäkerhet, IntelliSense och syntaxvalidering.
* **Modularitet:** Du kan dela upp bicep-koden i hanterbara delar med hjälp av [moduler](bicep-modules.md). Modulen distribuerar en uppsättning relaterade resurser. Med moduler kan du återanvända kod och förenkla utvecklingen. Lägg till modulen i en Bicep-fil varje gång du behöver distribuera resurserna.
* **Integrering med Azure-tjänster:** Bicep är integrerat med Azure-tjänster som Azure Policy, mallspecifikter och skisser.
* **Inga tillståndsfiler att hantera:** Alla tillstånd lagras i Azure. Användare kan samarbeta och vara förtroende för att uppdateringarna hanteras som förväntat. Använd vad [om-åtgärden för att](template-deploy-what-if.md) förhandsgranska ändringar innan du distribuerar mallen.
* **Ingen kostnad och öppen källkod:** Bicep är helt kostnadsfritt. Du behöver inte betala för premiumfunktioner. Det stöds också av Microsofts support.

## <a name="bicep-improvements"></a>Bicep-förbättringar

Bicep erbjuder en enklare och mer koncis syntax jämfört med motsvarande JSON. Du använder inte `[...]` uttryck. I stället anropar du funktioner direkt och hämtar värden från parametrar och variabler. Du ger varje distribuerad resurs ett symboliskt namn, vilket gör det enkelt att referera till resursen i mallen.

Följande JSON returnerar till exempel ett utdatavärde från en resursegenskap.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Motsvarande utdatauttryck i Bicep är lättare att skriva. I följande exempel returneras samma egenskap med hjälp av det symboliska **namnet publicIP** för en resurs som definieras i mallen:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

En fullständig jämförelse av syntaxen finns i [Jämföra JSON och Bicep för mallar.](compare-template-syntax.md)

Bicep hanterar automatiskt beroenden mellan resurser. Du kan undvika att `dependsOn` ange när det symboliska namnet på en resurs används i en annan resursdeklaration.

Strukturen för Bicep-filen är mer flexibel än JSON-mallen. Du kan deklarera parametrar, variabler och utdata var som helst i filen. I JSON måste du deklarera alla parametrar, variabler och utdata i motsvarande avsnitt i mallen.

## <a name="known-limitations"></a>Kända begränsningar

Följande begränsningar finns för närvarande:

* Det går inte att ange läge eller batchstorlek för kopieringsloopar.
* Det går inte att kombinera loopar och villkor.
* Enkelradsobjekt och `['a', 'b', 'c']` matriser, till exempel , stöds inte.

## <a name="faq"></a>Vanliga frågor

**Varför ska jag skapa ett nytt språk i stället för att använda ett befintligt?**

Du kan tänka på Bicep som en revision av det befintliga ARM-mallspråket i stället för ett nytt språk. Syntaxen har ändrats, men huvudfunktionerna och körningen förblir desamma.

Innan vi utvecklade Bicep övervägde vi att använda ett befintligt programmeringsspråk. Vi har bestämt att målgruppen skulle få det enklare att lära sig Bicep i stället för att komma igång med ett annat språk.

**Varför inte fokusera din energi på Terraform eller andra tredjepartserbjudanden av infrastruktur som kod?**

Olika användare föredrar olika konfigurationsspråk och verktyg. Vi vill se till att alla dessa verktyg ger en bra upplevelse i Azure. Bicep är en del av det arbetet.

Om du är nöjd med Terraform finns det ingen anledning att byta. Microsoft strävar efter att se till att Terraform på Azure är så bra som det kan vara.

För kunder som har valt ARM-mallar tror vi att Bicep förbättrar redigeringsupplevelsen. Bicep hjälper också till med övergången för kunder som inte har infört infrastruktur som kod.

**Är Bicep endast för Azure?**

Bicep är ett DSL som fokuserar på att distribuera kompletta lösningar till Azure. För att uppfylla det målet krävs att du arbetar med vissa API:er som ligger utanför Azure. Vi förväntar oss att tillhandahålla utökningspunkter för dessa scenarier.

**Vad händer med mina befintliga ARM-mallar?**

De fortsätter att fungera precis som de alltid har gjort. Du behöver inte göra några ändringar. Vi fortsätter att stödja det underliggande JSON-språket för ARM-mallar. Bicep-filer kompileras till JSON och JSON skickas till Azure för distribution.

När du är klar kan du konvertera [JSON-filerna till Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Nästa steg

Kom igång med [Bicep-självstudien](./bicep-tutorial-create-first-bicep.md).
