---
title: Översikt över mallar
description: Beskriver fördelarna med Azure Resource Manager mallar (ARM-mallar) för att distribuera resurser.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 14bcbbd7a7ae7315dbb8e9d3e7e44ce0ffe0a4b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419991"
---
# <a name="what-are-arm-templates"></a>Vad är ARM-mallar?

Med flytten till molnet har många team antagit smidiga utvecklings metoder. Dessa team itererar snabbt. De måste distribuera sina lösningar flera gånger till molnet och känna till att deras infrastruktur är i ett tillförlitligt tillstånd. Eftersom infrastrukturen har blivit en del av den iterativa processen har indelningen mellan drift och utveckling försvunnit. Team behöver hantera infrastruktur-och program kod genom en enhetlig process.

För att möta dessa utmaningar kan du automatisera distributioner och använda infrastrukturen som kod. I kod definierar du den infrastruktur som måste distribueras. Infrastruktur koden blir en del av projektet. Precis som program kod, lagrar du infrastruktur koden i en käll lagrings plats och version. Vilken som helst av dina team kan köra koden och distribuera liknande miljöer.

Använd Azure Resource Manager mallar (ARM-mallar) om du vill implementera infrastruktur som kod för dina Azure-lösningar. Mallen är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. I mallen anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

Vi har introducerat ett nytt språk för att utveckla ARM-mallar. Språket heter bicep och är för närvarande en för hands version. Bicep-och JSON-mallar ger samma funktioner. Du kan konvertera en mall mellan de två språken. Bicep innehåller en syntax som är enklare att använda för att skapa mallar. Mer information finns i [Vad är bicep (för hands version)?](bicep-overview.md).

Mer information om hur du kan komma igång med ARM-mallar finns i följande video.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>Varför ska du välja ARM-mallar?

Om du försöker bestämma mellan att använda ARM-mallar och en annan infrastruktur som kod tjänster, bör du tänka på följande fördelar med att använda mallar:

* **Deklarativ syntax**: arm-mallar gör att du kan skapa och distribuera en hel Azure-infrastruktur i en deklarativ miljö. Du kan till exempel distribuera inte bara virtuella datorer, utan även nätverks infrastrukturen, lagrings system och andra resurser som du kan behöva.

* **Upprepnings bara resultat**: Distribuera infrastrukturen flera gånger under utvecklings livs cykeln och få förtroende för att resurserna distribueras på ett konsekvent sätt. Mallarna är idempotenta, vilket innebär att du kan distribuera samma mall flera gånger och få samma resurs typer i samma tillstånd. Du kan utveckla en mall som representerar det önskade läget, i stället för att utveckla många separata mallar som representerar uppdateringar.

* **Orchestration**: du behöver inte bekymra dig om de komplexa funktionerna för beställning. Resource Manager dirigerar distributionen av beroende resurser så att de skapas i rätt ordning. När så är möjligt distribuerar Resource Manager resurserna parallellt så att distributionerna slutar snabbare än seriella distributioner. Du distribuerar mallen via ett kommando i stället för att använda flera tvingande kommandon.

   ![Malldistribution jämförelse](./media/overview/template-processing.png)

* **Modulära filer**: du kan dela upp dina mallar i mindre, återanvändbara komponenter och länka dem tillsammans vid distributions tiden. Du kan också kapsla en mall inuti en annan mall.

* **Skapa en Azure-resurs**: du kan använda nya Azure-tjänster och-funktioner direkt i mallar. Så snart en resurs leverantör inför nya resurser kan du distribuera dessa resurser via mallar. Du behöver inte vänta på att verktyg eller moduler uppdateras innan du använder de nya tjänsterna.

* **Utöknings barhet**: med [distributions skript](deployment-script-template.md)kan du lägga till PowerShell-eller bash-skript i dina mallar. Distributions skripten utökar din möjlighet att konfigurera resurser under distributionen. Ett skript kan inkluderas i mallen eller lagras i en extern källa och refereras till i mallen. Distributions skript ger dig möjlighet att slutföra din miljö konfiguration från slut punkt till slut punkt i en enda ARM-mall.

* **Testning**: du kan se till att din mall följer rekommenderade rikt linjer genom att testa den med verktyget arm Template Tool Kit (arm-TTK). Det här test paketet är ett PowerShell-skript som du kan ladda ned från [GitHub](https://github.com/Azure/arm-ttk). Med verktygs paketet är det enklare för dig att utveckla expertis med hjälp av mallens språk.

* **Förhandsgranska ändringar**: du kan använda [åtgärden vad händer om](template-deploy-what-if.md) du vill få en förhands granskning av ändringarna innan du distribuerar mallen. Med vad som händer, kan du se vilka resurser som kommer att skapas, uppdateras eller tas bort och vilka resurs egenskaper som ska ändras. Konsekvens åtgärden kontrollerar miljöns aktuella tillstånd och eliminerar behovet av att hantera tillstånd.

* **Inbyggd verifiering**: din mall distribueras bara när du har överfört verifieringen. Resource Manager kontrollerar mallen innan du påbörjar distributionen för att kontrol lera att distributionen kommer att lyckas. Distributionen är mindre troligt att stoppas i ett halv klart tillstånd.

* **Spårade distributioner**: i Azure Portal kan du granska distributions historiken och få information om mall distributionen. Du kan se den mall som har distribuerats, de parameter värden som angavs i och eventuella värden. Annan infrastruktur som kod tjänster spåras inte via portalen.

   ![Distributions historik](./media/overview/deployment-history.png)

* **Princip som kod**: [Azure policy](../../governance/policy/overview.md) är en princip som kod ramverk för att automatisera styrningen. Om du använder Azure-principer görs princip reparationer på icke-kompatibla resurser när de distribueras med hjälp av mallar.

* **Distributions ritningar**: du kan dra nytta av [skisser](../../governance/blueprints/overview.md) som tillhandahålls av Microsoft för att uppfylla regler och efterlevnad. Dessa ritningar innehåller färdiga mallar för olika arkitekturer.

* **CI/CD-integrering**: du kan integrera mallar i verktygen för kontinuerlig integrering och distribution (CI/CD), vilket kan automatisera dina versions pipeliner för snabba och tillförlitliga program-och infrastruktur uppdateringar. Genom att använda Azure-DevOps och Resource Manager-postmall kan du använda Azure-pipelines för att kontinuerligt bygga och distribuera ARM-mallar projekt. Mer information finns i [vs Project with pipelines](add-template-to-azure-pipelines.md) and [självstudie: kontinuerlig integrering av Azure Resource Manager mallar med Azure-pipelines](./deployment-tutorial-pipeline.md).

* **Exporterad kod**: du kan hämta en mall för en befintlig resurs grupp genom att antingen exportera det aktuella läget för resurs gruppen eller Visa mallen som används för en viss distribution. Att granska den [exporterade mallen](export-template-portal.md) är ett bra sätt att lära sig mer om mallsyntaxen.

* **Redigerings verktyg**: du kan skapa mallar med [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) och verktyget mall-tillägg. Du får IntelliSense, syntaxkontroll, direkt hjälp och många andra språk funktioner. Förutom Visual Studio Code kan du också använda [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Mallfil

I mallen kan du skriva [mallar](template-expressions.md) som utökar funktionerna i JSON. Dessa uttryck använder de [funktioner](template-functions.md) som tillhandahålls av Resource Manager.

Mallen innehåller följande avsnitt:

* [Parametrar](template-parameters.md) -ange värden under distributionen som tillåter att samma mall används med olika miljöer.

* [Variabler](template-variables.md) – definiera värden som återanvänds i dina mallar. De kan konstrueras från parameter värden.

* [Användardefinierade funktioner](template-user-defined-functions.md) – skapa anpassade funktioner som fören klar din mall.

* [Resurser](resource-declaration.md) – ange vilka resurser som ska distribueras.

* [Utdata](template-outputs.md) – returnera värden från de distribuerade resurserna.

## <a name="template-deployment-process"></a>Malldistribution process

När du distribuerar en mall konverterar Resource Manager mallen till REST API åtgärder. Till exempel när Resource Manager tar emot en mall med följande resursdefinition:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Den konverterar definitionen till följande REST API-åtgärd, som skickas till resursprovidern Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Observera att **API version** som du anger i mallen för resursen används som API-version för rest-åtgärden. Du kan distribuera mallen flera gånger och vara säker på att den fortsätter att fungera. Genom att använda samma API-version behöver du inte bekymra dig om att bryta ändringar som kan införas i senare versioner.

Använd något av följande alternativ om du vill distribuera en mall:

* [Azure-portalen](deploy-portal.md)
* [Azure CLI](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST-API](deploy-rest.md)
* [Knapp i GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>Mall design

Det är helt upp till dig hur du definierar mallar och resursgrupper och hur du vill hantera din lösning. Du kan till exempel distribuera programmet i tre nivåer via en enda mall till en enda resursgrupp.

![mall med tre nivåer](./media/overview/3-tier-template.png)

Du behöver dock inte definiera hela infrastrukturen i en enda mall. Ofta är det praktiskt att dela in dina distributionskrav i en uppsättning riktade mallar för specifika ändamål. Du kan enkelt återanvända dessa mallar för olika lösningar. Om du vill distribuera en viss lösning skapar du en huvud mall som länkar alla nödvändiga mallar. Följande bild visar hur du distribuerar en lösning i tre nivåer via en överordnad mall som innehåller tre kapslade mallar.

![mall med kapslad nivå](./media/overview/nested-tiers-template.png)

Om du tänker dig att dina nivåer har separata livscykler kan du distribuera dina tre nivåer till separata resursgrupper. Observera att resurserna fortfarande kan länkas till resurser i andra resursgrupper.

![nivåmall](./media/overview/tier-templates.png)

Mer information om kapslade mallar finns i [Använda länkade mallar med Azure Resource Manager](linked-templates.md).

## <a name="share-templates"></a>Dela mallar

När du har skapat mallen kanske du vill dela den med andra användare i din organisation. Med [specifikationer för mallar](template-specs.md) kan du lagra en mall som en resurs typ. Du använder rollbaserad åtkomst kontroll för att hantera åtkomst till mallen specifikation. Användare med Läs behörighet till mallen specifikation kan distribuera den, men inte ändra mallen.

Den här metoden innebär att du på ett säkert sätt kan dela mallar som uppfyller organisationens standarder.

## <a name="next-steps"></a>Nästa steg

* En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i [Självstudier: skapa och distribuera din första arm-mall](template-tutorial-create-first-template.md).
* Om du vill lära dig mer om ARM-mallar via en guidad uppsättning moduler på Microsoft Learn, se [distribuera och hantera resurser i Azure med hjälp av arm-mallar](/learn/paths/deploy-manage-resource-manager-templates/).
* Information om egenskaperna i mallfiler finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Information om hur du exporterar mallar finns i [snabb start: skapa och distribuera arm-mallar med hjälp av Azure Portal](quickstart-create-templates-use-the-portal.md).
* Svar på vanliga frågor finns [vanliga frågor om ARM-mallar](frequently-asked-questions.md).
