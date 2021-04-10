---
title: ARM-mall vanliga frågor och svar
description: Vanliga frågor och svar om Azure Resource Manager mallar (ARM-mallar).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419413"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Vanliga frågor om ARM-mallar

I den här artikeln får du svar på vanliga frågor om Azure Resource Manager mallar (ARM-mallar).

## <a name="getting-started"></a>Komma igång

* **Vad är ARM-mallar och varför ska jag använda dem?**

  ARM-mallar är JSON-filer där du definierar vad du vill distribuera till Azure. Med hjälp av mallar kan du implementera en lösning för infrastruktur som kod för Azure. Din organisation kan regelbundet och tillförlitligt distribuera den infrastruktur som krävs till olika miljöer.

  Mer information om hur ARM-mallar hjälper dig att hantera din Azure-infrastruktur finns i [Vad är arm-mallar?](overview.md)

* **Hur gör jag för att kom igång med mallar?**

  Du behöver rätt verktyg för att förenkla redigering av ARM-mallar. Vi rekommenderar att du installerar [Visual Studio Code](https://code.visualstudio.com/) och [Azure Resource Manager Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). En snabb introduktion till dessa verktyg finns i [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  När du är redo att lära dig hur du skapar ARM-mallar startar du [övnings serien för nybörjare på arm-mallarna](template-tutorial-create-first-template.md). De här självstudierna tar dig steg för steg genom processen att skapa en ARM-mall. Du lär dig mer om de olika avsnitten i mallen och hur de fungerar tillsammans. Det här innehållet är också tillgängligt som en [Microsoft Learn modul](/learn/modules/authoring-arm-templates/).

* **Bör jag använda ARM-mallar eller terraform för att distribuera till Azure?**

  Använd det alternativ som du gillar bäst. Båda tjänsterna hjälper dig att automatisera distributioner till Azure.

  Vi tror att det finns några fördelar med att använda ARM-mallar över andra infrastruktur-som-kod-tjänster. Läs mer om de här fördelarna i [Varför väljer du arm-mallar?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **Jag missade din presentation på Microsoft build 2020. Är presentationen tillgänglig för visning?**

  Ja, [se det när som helst](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Var kan jag få mer information om de nya funktionerna som du presenterade vid bygget?**

  För allmän information om funktioner som vi arbetar med kan du gå med i vår [Azure Advisor-distribution Yammer-grupp](https://aka.ms/ARMMeet).

  Om du vill veta mer om det nya mall språket registrerar du dig [för meddelanden](https://aka.ms/armLangUpdates).

  Mer information om specifikationer för mallar finns i [Azure Resource Manager mall-specifikationer (för hands version)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Skapa och testa mallar

* **Var kan jag lära mig om bästa praxis för ARM-mallar?**

  Rekommendationer om hur du implementerar dina mallar finns i [metod tips för ARM-mallar](template-best-practices.md). När du har skapat en mall kör du [arm-testverktyget](https://github.com/azure/arm-ttk). Det kontrollerar om din mall matchar rekommenderade metoder.

* **Jag har konfigurerat min miljö via portalen. Finns det något sätt att hämta mallen från en befintlig resurs grupp?**

  Ja, du kan [Exportera mallen](export-template-portal.md) från en resurs grupp. Den exporterade mallen är en bra utgångs punkt för att lära dig om mallar, men du vill förmodligen ändra den innan du använder den i en produktions miljö.

  När du exporterar mallen kan du välja vilka resurser som du vill inkludera i mallen.

* **Kan jag skapa en resurs grupp i en ARM-mall och distribuera resurser till den?**

  Ja, du kan skapa en resurs grupp i en mall när du distribuerar mallen på samma nivå som din Azure-prenumeration. Ett exempel på hur du skapar en resurs grupp och distribuerar resurser finns i [resurs grupp och resurser](deploy-to-subscription.md#resource-groups).

* **Kan jag skapa en prenumeration i en ARM-mall?**

  Ja, mer information finns i [program mässigt skapa Azure-prenumerationer med de senaste API: erna](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Hur kan jag testa min mall innan jag distribuerar den?**

  Vi rekommenderar att du kör [arm-testverktyget](https://github.com/azure/arm-ttk) och [Vad-händer-åtgärden](template-deploy-what-if.md) på mallarna innan du distribuerar dem. Test Toolkit kontrollerar om din mall använder bästa praxis. Den innehåller varningar när den identifierar ändringar som kan förbättra hur du implementerar din mall.

  Åtgärden vad händer om visar ändringarna som din mall kommer att göra i din miljö. Du kan se oönskade ändringar innan de distribueras. Vad – om returnerar eventuella fel som kan identifieras under preflight-valideringen. Om din mall till exempel innehåller ett syntaktiskt fel returneras felet. Det returnerar också eventuella fel som kan fastställa om det slutliga läget för de distribuerade resurserna. Om din mall exempelvis distribuerar ett lagrings konto med ett namn som redan används, vad-om returnerar det felet.

* **Var hittar jag information om de egenskaper som är tillgängliga för varje resurs typ?**

  VS Code ger IntelliSense för att arbeta med resurs egenskaperna. Du kan också visa [mallens referens](/azure/templates/) för egenskaper och beskrivningar.

* **Jag behöver skapa flera instanser av en resurs typ. Hur gör jag för att skapa en iterator i min mall?**

  Använd kopiera-elementet för att ange fler än en instans. Du kan använda kopiera på [resurser](copy-resources.md), [Egenskaper](copy-properties.md), [variabler](copy-variables.md)och [utdata](copy-outputs.md).

## <a name="template-language"></a>Mallens språk

* **Jag har hört att du arbetar med ett nytt språk för mallar. Var hittar jag mer information?**

  Läs mer om det nya språket i [Vad är bicep (för hands version)?](bicep-overview.md).

* **Finns det någon plan för att stödja att skapa mallar i YAML?**

  För närvarande finns det ingen plan för att stödja YAML. Vi tror att det nya mall-språket kommer att erbjuda en lösning som är enklare att använda än YAML eller JSON.

* **Kan jag fortfarande skriva mallar i JSON när det nya mall-språket har frigjorts?**

  Ja, du kan fortsätta att använda JSON-mallar.

* **Kommer du att erbjuda ett verktyg för att konvertera mina JSON-mallar till det nya mall språket?**

  Ja. Se [konvertera arm-mallar mellan JSON och bicep](bicep-decompile.md).

## <a name="template-specs"></a>Mallspecifikationer

* **Hur kan jag komma igång med för hands versionen av mall-specifikationer?**

  Installera den senaste versionen av PowerShell eller Azure CLI. För Azure PowerShell använder du [version 5.0.0 eller senare](/powershell/azure/install-az-ps). För Azure CLI använder du [version 2.14.2 eller senare](/cli/azure/install-azure-cli).

* **Hur är mall-specifikationer och Azure-ritningar relaterade?**

  Azure-ritningar använder mall-specifikationer i dess implementering genom att ersätta `blueprint definition` resursen med en `template spec` resurs. Vi kommer att tillhandahålla en migrerings Sök väg för att konvertera skiss definitionen till en mall-specifikation, men API: er för skiss definition stöds fortfarande. Det finns inga ändringar i `blueprint assignment` resursen. Ritningar är fortfarande en användar upplevelse för att skapa en styrd miljö i Azure.

* **Ersätt de länkade mallarna med specifikationer för mallar?**

  Nej, men mall-specifikationerna är utformade för att fungera bra med länkade mallar. Du behöver inte flytta den länkade mallen till en offentligt tillgänglig slut punkt innan du distribuerar den överordnade mallen. I stället paketerar du den överordnade mallen och dess artefakter tillsammans när du skapar en mall-specifikation.

* **Kan mall-specifikationer delas mellan prenumerationer?**

  Ja, de kan användas över prenumerationer så länge användaren har Läs behörighet till specifikationen för mallen. Det går inte att använda mall-specifikationer mellan klienter.

## <a name="scripts-in-templates"></a>Skript i mallar

* **Kan jag inkludera ett skript i min mall för att utföra uppgifter som inte är möjliga i en mall?**

  Ja, Använd [distributions skript](deployment-script-template.md). Du kan inkludera Azure PowerShell-eller Azure CLI-skript i dina mallar. Funktionen är en för hands version.

* **Kan jag fortfarande använda anpassade skript tillägg och önskad tillstånds konfiguration (DSC)?**

  Dessa alternativ är fortfarande tillgängliga och har inte ändrats. Distributions skript är utformade för att utföra åtgärder som inte är relaterade till den virtuella datorns gäst. Om du behöver köra ett skript på ett värd operativ system på en virtuell dator är det anpassade skript tillägget och/eller DSC ett bättre alternativ. Distributions skript har dock fördelar, till exempel timeout-varaktighet.

* **Stöds distributions skript i Azure Government?**

  Ja, du kan använda distributions skript i US Gov, Arizona och US Gov, Virginia.

## <a name="preview-changes-before-deployment"></a>Förhandsgranska ändringar före distribution

* **Kan jag förhandsgranska de ändringar som ska utföras innan du distribuerar en mall?**

  Ja, Använd [funktionen what-if](template-deploy-what-if.md). Den utvärderar miljöns aktuella tillstånd och jämför den med det tillstånd som kommer att finnas efter distributionen. Du kan granska de sammanfattade ändringarna för att se till att mallen inte har några oväntade resultat.

* **Kan jag använda what-if med både stegvisa och fullständiga lägen?**

  Ja, båda [distributions lägena](deployment-modes.md) stöds. Ett exempel på hur du använder stegvist läge finns i [köra konsekvens åtgärder](template-deploy-what-if.md#run-what-if-operation). Ett exempel på hur du använder fullständig läge finns i [Bekräfta borttagning](template-deploy-what-if.md#confirm-deletion).

* **Vad händer om fungerar med länkade mallar?**

  Ja, vad-om utvärderar den överordnade mallens tillstånd och dess länkade mallar.

* **Kan jag använda konsekvens i en Azure-pipeline?**

  Ja, du kan använda konsekvens för att kontrol lera att pipelinen ska fortsätta.

* **När jag använder vad som händer, ser jag ändringar i egenskaper som inte finns i min mall. Förväntas "brus"?**

  Vi arbetar på att minska bruset. Du hjälper oss att förbättra genom att skicka in problem i vår GitHub-lagrings platsen här: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Mall-visualiserare

* **Finns det något sätt för mig att visualisera min ARM-mall och dess resurser?**

  Vi har ett [Community-tillägg vs Code-tillägg](https://aka.ms/ARMVisualizer) som gör ett bra jobb att visualisera arm-mallen. Den visar de resurser som du distribuerar och relationerna mellan dem.

* **Kan jag använda mallen visualisera utanför VS-kod?**

  Mallens visualiserare har för hands visats i portalen. Om du vill ha mer information kan du titta på den här [korta sessionen från build](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Distributions gränser

* **Hur många resurs grupper kan jag distribuera till i en enda distributions åtgärd?**

  Tidigare var gränsen fem resurs grupper. Den har nyligen ökat till 800 resurs grupper. Mer information finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

* **Jag har fått ett fel meddelande om att det är begränsat till 800-distributioner i distributions historiken. Vad ska jag göra?**

  Vi ändrar hur distributions historiken för en resurs grupp upprätthålls. Tidigare var du tvungen att manuellt ta bort distributioner från den här historiken för att undvika det här felet. Från och med juni 2020 tar vi automatiskt bort distributioner från historiken när du får nära gränsen. Mer information finns i [automatiska borttagningar från distributions historiken](deployment-history-deletions.md).

  Att ta bort en distribution från historiken påverkar inte de distribuerade resurserna.

## <a name="templates-and-devops"></a>Mallar och DevOps

* **Kan jag integrera ARM-mallar i Azure-pipeliner?**

  Ja. En förklaring av hur du använder mallar och pipeliner finns i [Självstudier: kontinuerlig integrering av arm-mallar med Azure-pipelines](deployment-tutorial-pipeline.md) och [integrera arm-mallar med Azure-pipelines](add-template-to-azure-pipelines.md).

* **Kan jag använda GitHub-åtgärder för att distribuera en mall?**

  Ja, se [distribuera arm-mallar med GitHub-åtgärder](deploy-github-actions.md).

## <a name="next-steps"></a>Nästa steg

En introduktion till ARM-mallar finns i [Vad är arm-mallar?](overview.md).
