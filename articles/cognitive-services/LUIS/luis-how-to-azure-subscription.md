---
title: Använda redigerings-och körnings nycklar – LUIS
description: När du först använder LUIS behöver du inte skapa någon redigerings nyckel. När du vill publicera appen och sedan använda runtime-slutpunkten måste du skapa och tilldela körnings nyckeln till appen.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988157"
---
# <a name="create-luis-resources"></a>Skapa LUIS-resurser

Att skapa och köra frågor för förutsägelse av körning ger autentisering till din Language Understanding-app (LUIS) och förutsägelse slut punkt.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-resurser

LUIS tillåter tre typer av Azure-resurser och en icke-Azure-resurs:

|Resurs|Syfte|Kognitiv tjänst `kind`|Kognitiv tjänst `type`|
|--|--|--|--|
|Skapar resurs|Gör att du kan skapa, hantera, träna, testa och publicera dina program. [Skapa en Luis Authoring-resurs](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) om du tänker redigera Luis Apps program mässigt eller från Luis-portalen. Du måste [migrera ditt Luis-konto](luis-migration-authoring.md#what-is-migration) innan du länkar dina Azures redigerings resurser till ditt program. Du kan kontrol lera behörigheter till redigerings resursen genom att tilldela personer [rollen deltagare](#contributions-from-other-authors). <br><br> En nivå är tillgänglig för LUIS Authoring-resursen:<br> <ul> <li>**Kostnads fri F0 Authoring-resurs**, som ger dig 1 000 000 kostnads fria redigerings transaktioner och 1 000 kostnads fria testnings slut punkt förfrågningar varje månad. |`LUIS.Authoring`|`Cognitive Services`|
|Förutsägelse resurs| När du har publicerat ditt LUIS-program använder du förutsägelse resurs/nyckel för att fråga förutsägelse slut punkts begär Anden. Skapa en LUIS förutsägelse resurs innan klient programmet begär förutsägelser utöver de 1 000-begäranden som anges av redigerings-eller start resurs. <br><br> Det finns två nivåer för förutsägelse resursen:<br><ul> <li> **Kostnads fri F0 förutsägelse resurs**, som ger dig 10 000 kostnads fri förutsägelse slut punkt begär Anden varje månad.<br> <li> **Standard S0 förutsägelse resurs**, som är den betalda nivån. [Läs mer om priser.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Start/utvärderings resurs|Gör att du kan skapa, hantera, träna, testa och publicera dina program. Den här resursen skapas som standard om du väljer alternativet Start resurs första gången du loggar in på LUIS. Start nyckeln är slutligen inaktuell. Alla LUIS-användare kommer att behöva [migrera sina konton](luis-migration-authoring.md#what-is-migration) och länka sina Luis-program till en redigerings resurs. Till skillnad från redigerings resursen ger den här resursen inte behörighet för Azure-rollbaserad åtkomst kontroll. <br><br> Precis som med redigerings resursen ger start resursen dig 1 000 000 kostnads fri redigering av transaktioner och 1 000 kostnads fria test slut punkter.|-|Inte en Azure-resurs.|
|[Cognitive Services resurs nyckel för multitjänst](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Slut punkts begär Anden för förfrågan som delas med LUIS och andra kognitiva tjänster som stöds.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS tillhandahåller två typer av F0-resurser (kostnads fri nivå): en för att skapa transaktioner och en för förutsägelse transaktioner. Om du har slut på ledig kvot för förutsägelse transaktioner, se till att du använder F0 förutsägelse resurs, som ger dig en 10 000 kostnads fri transaktion, och inte en redigerings resurs, som ger dig 1 000 förutsägelse transaktioner per månad.

När processen för att skapa Azure-resurser är färdig [tilldelar du resursen](#assign-a-resource-to-an-app) till appen i Luis-portalen.

> [!important]
> Du bör redigera LUIS-appar i de [regioner](luis-reference-regions.md#publishing-regions) där du vill publicera och fråga.

## <a name="resource-ownership"></a>Resurs ägarskap

En Azure-resurs, till exempel en LUIS-resurs, ägs av den prenumeration som innehåller resursen.

Om du vill ändra ägarskap för en resurs kan du utföra någon av följande åtgärder:
* Överför prenumerationens [ägare](../../cost-management-billing/manage/billing-subscription-transfer.md) .
* Exportera LUIS-appen som en fil och importera sedan appen på en annan prenumeration. Export är tillgängligt på sidan **Mina appar** i Luis-portalen.

## <a name="resource-limits"></a>Resursgränser

### <a name="authoring-key-creation-limits"></a>Redigerings gränser för att skapa nycklar

Du kan skapa så många som 10 redigerings nycklar per region, per prenumeration. Publicerings regioner skiljer sig från redigerings områden. Se till att du skapar en app i den redigerings region som motsvarar den publicerings region där du vill att ditt klient program ska finnas. Information om hur du skapar redigerings områden för att publicera regioner finns i [redigerings-och publicerings regioner](luis-reference-regions.md). 

Mer information om nyckel gränser finns i [nyckel gränser](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Fel för nyckel användnings gränser

Användnings gränser baseras på pris nivån.

Om du överskrider kvoten för transaktioner per sekund (TPS) får du ett HTTP 429-fel. Om du överskrider kvoten för din transaktion per månad (TPM) får du ett HTTP 403-fel.


### <a name="reset-an-authoring-key"></a>Återställa en redigerings nyckel

För [migrerade Authoring Resource](luis-migration-authoring.md) -appar: om redigerings nyckeln komprometteras, återställer du nyckeln i Azure Portal på sidan **nycklar** för redigerings resursen.

För appar som inte har migrerats: återställs nyckeln på alla dina appar på LUIS-portalen. Om du redigerar dina appar via redigerings-API: erna måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln.

### <a name="regenerate-an-azure-key"></a>Återskapa en Azure-nyckel

Du kan återskapa en Azure-nyckel från sidan **nycklar** i Azure Portal.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Ägarskap för appar, åtkomst och säkerhet

En app definieras av sina Azure-resurser, vilket bestäms av ägarens prenumeration.

Du kan flytta LUIS-appen. Använd följande resurser för att hjälpa dig med hjälp av Azure Portal eller Azure CLI:

* [Flytta en app mellan LUIS Authoring-resurser](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Flytta en resurs till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Flytta en resurs inom samma prenumeration eller mellan prenumerationer](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Bidrag från andra författare

För [migrerade redigerings resurs](luis-migration-authoring.md) program: du kan hantera _deltagare_ för en redigerings resurs i Azure Portal med hjälp av **åtkomst kontroll sidan (IAM)** . Lär dig [hur du lägger till en användare](luis-how-to-collaborate.md) med hjälp av medarbetares e-postadress och deltagar rollen.

För appar som ännu inte har migrerats: du kan hantera alla _medarbetare_ på sidan för att **Hantera > samarbets partners** i Luis-portalen.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Fråga förutsägelse åtkomst för privata och offentliga appar

För privata appar är åtkomsten för fråga förutsägelse körning tillgänglig för ägare och deltagare. För offentliga appar är körnings åtkomst tillgänglig för användare som har sin egen Azure [-tjänst](../cognitive-services-apis-create-account.md) eller [Luis](#create-resources-in-the-azure-portal) runtime-resurs och den offentliga appens ID.

Det finns för närvarande ingen katalog över offentliga appar.

### <a name="authoring-permissions-and-access"></a>Redigera behörigheter och åtkomst
Åtkomst till en app från [Luis](luis-reference-regions.md#luis-website) -portalen eller [redigerings-API: erna](https://go.microsoft.com/fwlink/?linkid=2092087) styrs av Azure Authoring-resursen.

Ägaren och alla deltagare har åtkomst för att redigera appen.

|Redigerings åtkomst innehåller:|Kommentarer|
|--|--|
|Lägga till eller ta bort slut punkts nycklar||
|Exportera version||
|Exportera slut punkts loggar||
|Importera version||
|Gör appen offentlig|När en app är offentlig kan alla som har en redigerings-eller slut punkts nyckel fråga appen.|
|Ändra modell|
|Publicera|
|Granska slut punkts yttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Åtkomst till förutsägelse slut punkts körning

Åtkomst för att skicka frågor till förutsägelse slut punkten styrs av en inställning på sidan **program information** i avsnittet **Hantera** .

|[Privat slutpunkt](#runtime-security-for-private-apps)|[Offentlig slutpunkt](#runtime-security-for-public-apps)|
|:--|:--|
|Tillgängligt för ägare och deltagare|Tillgängligt för ägare, deltagare och någon annan som känner till app-ID|

Du kan styra vem som ska se din LUIS körnings nyckel genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan robot-och LUIS redan säkrare. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (t. ex. en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (via något som [Azure AD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentiseras och auktoriseringen verifieras, skicka anropet till LUIS. Den här strategin förhindrar inte man-in-the-middle-attacker. Men det obfuscate din nyckel-och slut punkts-URL från dina användare, så att du kan spåra åtkomst och låta dig lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Körnings säkerhet för privata appar

En privat Apps körnings miljö är bara tillgänglig för följande nycklar:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigerings nyckel| Upp till 1 000 slut punkts träffar|
|Redigerings nycklar för medarbetare/deltagare| Upp till 1 000 slut punkts träffar|
|Alla nycklar som tilldelats LUIS av en författare eller medarbetare/deltagare|Baserat på nyckel användnings nivå|

### <a name="runtime-security-for-public-apps"></a>Körnings säkerhet för offentliga appar

När din app har kon figurer ATS som offentlig, kan _alla_ giltiga Luis-redigerings nycklar eller Luis-slutpunkt-nyckeln fråga den, så länge nyckeln inte har använt hela slut punkts kvoten.

En användare som inte är ägare eller deltagare kan bara komma åt en offentlig Apps körnings miljö om app-ID: t. LUIS har inte någon offentlig marknad eller något annat sätt för användarna att söka efter en offentlig app.

En offentlig app publiceras i alla regioner. En användare med en region baserad LUIS resurs nyckel kan komma åt appen i den region där den är kopplad till resurs nyckeln.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Kontrol lera åtkomst till din frågas förutsägelse slut punkt

Du kan styra vem som kan se slut punkts nyckeln för LUIS förutsägelse körning genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan robot-och LUIS redan säkrare. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (t. ex. en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (via något som [Azure AD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentisering och auktorisering verifieras, skicka anropet till LUIS. Den här strategin förhindrar inte man-in-the-middle-attacker. Men det gör obfuscate till din slut punkt från dina användare, så att du kan spåra åtkomsten och göra det möjligt att lägga till slut punkts svars loggning (som [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Logga in på LUIS-portalen och börja redigera

1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.
1. Börja redigera LUIS-appen genom att välja din Azure LUIS Authoring-nyckel:

   ![Skärm bild som visar välkomst skärmen.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. [Skapa en ny app](luis-how-to-start-new-app.md#create-new-app-in-luis)när du är klar med resurs urvals processen.


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Skapa resurser i Azure CLI

Använd [Azure CLI](/cli/azure/install-azure-cli) för att skapa varje resurs individuellt.

Resurs `kind` :

* Redigerings `LUIS.Authoring`
* Förutsägelse `LUIS`

1. Logga in på Azure CLI:

    ```azurecli
    az login
    ```

    Det här kommandot öppnar en webbläsare så att du kan välja rätt konto och tillhandahålla autentisering.

1. Skapa en LUIS som skapar en resurs av typen `LUIS.Authoring` , med namnet `my-luis-authoring-resource` . Skapa den i den _befintliga_ resurs gruppen med namnet `my-resource-group` för `westus` regionen.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Skapa en LUIS förutsägelse slut punkt resurs av typen `LUIS` , med namnet `my-luis-prediction-resource` . Skapa den i den _befintliga_ resurs gruppen med namnet `my-resource-group` för `westus` regionen. Om du vill ha högre data flöde än den kostnads fria nivån kan du ändra `F0` till `S0` . [Läs mer om pris nivåer och data flöde.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Dessa nycklar används inte av Luis-portalen förrän de har tilldelats på sidan **Hantera**  >  **Azure-resurser** på Luis-portalen.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Tilldela resurser i LUIS-portalen

Du kan tilldela en redigerings resurs för en enskild app eller för alla appar i LUIS. Följande procedur tilldelar alla appar till en enda redigerings resurs.

1. Logga in på [Luis-portalen](https://www.luis.ai).
1. Välj ditt användar konto i det övre högra hörnet och välj sedan **Inställningar**.
1. På sidan **användar inställningar** väljer du **Lägg till redigering resurs** och väljer sedan en befintlig redigerings resurs. Välj **Spara**.

## <a name="assign-a-resource-to-an-app"></a>Tilldela en resurs till en app

>[!NOTE]
>Om du inte har någon Azure-prenumeration kan du inte tilldela eller skapa en ny resurs. Du måste skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/en-us/free/) och sedan gå tillbaka till Luis för att skapa en ny resurs från portalen.

Du kan använda den här proceduren för att skapa en redigerare eller en förutsägelse resurs eller tilldela en till ett program: 

1. Logga in på [Luis-portalen](https://www.luis.ai). Välj en app i listan **Mina appar** .
1. Gå till **Hantera**  >  **Azure-resurser**:

    ![Skärm bild som visar sidan Azure-resurser.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. På fliken **förutsägelse resurs** eller **redigering av resurs** väljer du resursen **Lägg till förutsägelse resurs** eller **Lägg till redigerare** .
1. Använd fälten i formuläret för att hitta rätt resurs och välj sedan **Spara**.
1. Om du inte har en befintlig resurs kan du skapa en genom att välja **skapa en ny Luis-resurs** längst ned i fönstret.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Tilldela en körnings resurs för en fråga förutsägelse utan att använda LUIS-portalen

För automatiserade processer som CI/CD-pipelines kanske du vill automatisera tilldelningen av en LUIS runtime-resurs till en LUIS-app. Det gör du genom att följa dessa steg:

1. Hämta en Azure Resource Manager token från [den här webbplatsen](https://resources.azure.com/api/token?plaintext=true). Denna token upphör att gälla, så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Skärm bild som visar webbplatsen för att begära en Azure Resource Manager-token.](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS runtime-resurser mellan prenumerationer. Använd API för att [Hämta Luis Azure-konton](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), som ditt användar konto har åtkomst till.

    Detta POST-API kräver följande värden:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}` . Värdet för token måste föregås av ordet `Bearer` och ett blank steg.|
    |`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|

    API: et returnerar en matris med JSON-objekt som representerar dina LUIS-prenumerationer. Returnerade värden är prenumerations-ID, resurs grupp och resurs namn, returnerat som `AccountName` . Hitta objektet i matrisen som är den LUIS-resurs som du vill tilldela till LUIS-appen.

1. Tilldela token till LUIS-resursen med hjälp av [tilldela ett Luis Azure-konto till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API.

    Detta POST-API kräver följande värden:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}` . Värdet för token måste föregås av ordet `Bearer` och ett blank steg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|
    |Huvud|`Content-type`|`application/json`|
    |Mängden|`appid`|LUIS-app-ID.
    |Brödtext||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-USWest-S0-2"}|

    När detta API har slutförts returneras `201 - created status` .

## <a name="unassign-a-resource"></a>Ta bort tilldelning av en resurs

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj sedan en app från listan **Mina appar** .
1. Gå till **Hantera**  >  **Azure-resurser**.
1. På fliken **förutsägelse resurs** eller fliken **redigering** av resurs väljer du knappen **ta bort** resurs för resursen.

När du tar bort tilldelningen av en resurs tas den inte bort från Azure. Den länkas bara från LUIS.


## <a name="delete-an-account"></a>Ta bort ett konto

Se [data lagring och borttagning](luis-concept-data-storage.md#accounts) för information om vilka data som tas bort när du tar bort ditt konto.

## <a name="change-the-pricing-tier"></a>Ändra pris nivån

1.  Leta upp och välj din LUIS-prenumeration i [Azure Portal](https://portal.azure.com):

    ![Skärm bild som visar en LUIS-prenumeration i Azure Portal.](./media/luis-usage-tiers/find.png)
1.  Välj **pris nivå** för att se tillgängliga pris nivåer:

    ![Skärm bild som visar meny alternativet pris nivå.](./media/luis-usage-tiers/subscription.png)
1.  Välj pris nivå och klicka sedan på **Välj** för att spara din ändring:

    ![Skärm bild som visar hur du väljer och sparar en pris nivå.](./media/luis-usage-tiers/plans.png)

    När pris ändringen är klar verifierar ett popup-fönster uppdatering av pris nivå:

    ![Skärm bild av popup-fönstret som verifierar pris uppdateringen.](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slut punkts nyckeln](#assign-a-resource-to-an-app) på **publicerings** sidan och Använd den i alla slut punkts frågor.

## <a name="view-azure-resource-metrics"></a>Visa Azures resurs mått

### <a name="view-a-summary-of-azure-resource-usage"></a>Visa en sammanfattning av användningen av Azure Resource
Du kan visa användnings information om LUIS i Azure Portal. På sidan **Översikt** visas en sammanfattning, inklusive senaste anrop och fel. Om du gör en LUIS kan du tillåta upp till fem minuter innan ändringen visas.

![Skärm bild som visar översikts sidan.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Anpassa användnings diagram för Azure-resursanvändning
Sidan **mått** ger en mer detaljerad vy över data:

![Skärm bild som visar sidan mått.](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera dina mått diagram för en viss tids period och en mått typ:

![Skärm bild som visar ett anpassat diagram.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Avisering om totalt antal transaktioner
Om du vill veta när du når ett visst transaktions tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering:

![Skärm bild som visar sidan aviserings regler.](./media/luis-usage-tiers/alert-default.png)

Lägg till en mått avisering för det **totala anrops** måttet under en viss tids period. Lägg till e-postadresser till alla personer som ska ta emot aviseringen. Lägg till webhookar för alla system som ska ta emot aviseringen. Du kan också köra en Logic app när aviseringen utlöses.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder versioner](luis-how-to-manage-versions.md) för att kontrol lera appens livs cykel.
* Migrera till den nya [redigerings resursen](luis-migration-authoring.md).
