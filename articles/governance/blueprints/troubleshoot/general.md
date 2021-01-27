---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem med att skapa, tilldela och ta bort ritningar, till exempel princip överträdelser och skiss parameter funktioner.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 65cf8ef9a5dcba0165aad8522f91ff1eb2c963a8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918852"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka fel med Azure-ritningar

Du kan stöta på fel när du skapar, tilldelar eller tar bort ritningar. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta fel information

Många fel är resultatet av att tilldela en skiss till ett omfång. När en tilldelning Miss lyckas innehåller skissen information om den misslyckade distributionen. Den här informationen anger problemet så att det kan åtgärdas och nästa distribution lyckas.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **tilldelade skisser** från sidan till vänster och Använd sökrutan för att filtrera skiss tilldelningarna för att hitta den misslyckade tilldelningen. Du kan också sortera tilldelnings tabellen i kolumnen **etablerings tillstånd** för att se alla misslyckade tilldelningar grupperade tillsammans.

1. Välj skissen med statusen _misslyckad_ eller högerklicka och välj **Visa tilldelnings information**.

1. En röd banderoll-varning om att tilldelningen misslyckades visas överst på sidan skiss tilldelning. Välj var som helst på banderollen för att få mer information.

Det är vanligt att felet orsakas av en artefakt och inte skissen som helhet. Om en artefakt skapar ett Key Vault och Azure Policy förhindrar att Key Vault skapas, kommer hela tilldelningen att Miss förfalla.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scenario: princip överträdelse

#### <a name="issue"></a>Problem

Mallen kunde inte distribueras på grund av en princip överträdelse.

#### <a name="cause"></a>Orsak

En princip kan vara i konflikt med distributionen av ett antal orsaker:

- Den resurs som skapas begränsas av en princip (ofta SKU eller plats begränsningar)
- Distributionen anger fält som har kon figurer ATS av en princip (common med taggar)

#### <a name="resolution"></a>Lösning

Ändra skissen så att den inte hamnar i konflikt med principerna i fel informationen. Om den här ändringen inte är möjlig, är ett alternativt alternativ att omfånget för princip tilldelningen har ändrats, så att skissen inte längre strider mot principen.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scenario: skiss parametern är en funktion

#### <a name="issue"></a>Problem

Skiss parametrar som är funktioner bearbetas innan de skickas till artefakter.

#### <a name="cause"></a>Orsak

Att skicka en skiss parameter som använder en funktion, till exempel `[resourceGroup().tags.myTag]` , till en artefakt resulterar i att det behandlade resultatet av funktionen som anges i artefakten i stället för den dynamiska funktionen.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion via som en parameter, kan du undanta hela strängen med `[` så att skiss parametern ser ut som `[[resourceGroup().tags.myTag]` . Escape-tecken gör att ritningar hanterar värdet som en sträng när skissen bearbetas. Skiss tjänsten placerar sedan funktionen på artefakten så att den kan vara dynamisk som förväntat. Mer information finns [i syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Ta bort fel

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scenario: tids gräns för borttagning av tilldelning

#### <a name="issue"></a>Problem

Borttagning av skiss tilldelning slutförs inte.

#### <a name="cause"></a>Orsak

En skiss tilldelning kan bli fastnat i ett icke-terminal-tillstånd när den tas bort. Det här tillståndet orsakas när resurser som skapats av skiss tilldelningen fortfarande väntar på borttagning eller inte returnerar en status kod till Azure-ritningar.

#### <a name="resolution"></a>Lösning

Skiss tilldelningar i ett icke-terminal-tillstånd markeras automatiskt som **misslyckade** efter en tids gräns på _sex timmar_ . När tids gränsen har justerats för skiss tilldelningens tillstånd kan borttagningen göras om.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.