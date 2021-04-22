---
title: Skapa ett Internet Analyzer-test med CLI-| Microsoft Docs
description: I den här artikeln får du lära dig hur du skapar ditt första Internet Analyzer test med hjälp av Azure CLI.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 4e200440bd37d3593d5fc096b3dd79eff25dc1d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877236"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Skapa ett Internet Analyzer med CLI (förhandsversion)

Det finns två sätt att skapa en Internet Analyzer resurs – med hjälp [av Azure Portal](internet-analyzer-create-test-portal.md) eller cli. Det här avsnittet hjälper dig att skapa en Azure Internet Analyzer resurs med hjälp av vår CLI-upplevelse. 


> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga förhandsversionen är tillgänglig för användning globalt. Datalagringen är dock begränsad till *USA, västra 2* under förhandsversionen.

## <a name="object-model"></a>Objektmodell
I Internet Analyzer CLI finns följande typer av resurser:
* **Tester** – Ett test jämför slutanvändares prestanda för två Internetslutpunkter (A och B) över tid.
* **Profiler** – Tester skapas under en Internet Analyzer profil. Profiler gör det möjligt att gruppera relaterade tester. en enskild profil kan innehålla ett eller flera tester.
* **Förkonfigurerade slutpunkter** – Vi har konfigurerat slutpunkter med en mängd olika konfigurationer (regioner, accelerationstekniker osv.). Du kan använda någon av dessa förkonfigurerade slutpunkter i dina tester.
* **Styrkort –** Ett styrkort ger snabba och meningsfulla sammanfattningar av måttresultat. Se Tolka [ditt styrkort](internet-analyzer-scorecard.md).
* **Tidsserie** – En tidsserie visar hur ett mått ändras över tid.

## <a name="profile-and-test-creation"></a>Skapa profil och testa
1. Få Internet Analyzer åtkomst till förhandsversionen genom **att följa Hur gör jag för att delta i förhandsversionen?** instruktioner från vanliga frågor Azure Internet Analyzer vanliga frågor och [svar](internet-analyzer-faq.md).
2. [Installera Azure CLI.](/cli/azure/install-azure-cli)
3. Kör kommandot `login` för att starta en CLI-session:
    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.
    Annars öppnar du en webbläsarsida på och https://aka.ms/devicelogin anger auktoriseringskoden som visas i terminalen.

4. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

5. Välj ditt prenumerations-ID som har beviljats åtkomst till Internet Analyzer offentliga förhandsversionen.

    När du har loggat in visas en lista över prenumerationer som är kopplade till ditt Azure-konto. Prenumerationsinformationen med `isDefault: true` är den prenumeration som för närvarande är aktiverad efter inloggning. Om du vill välja en annan prenumeration använder du [kommandot az account set](/cli/azure/account#az_account_set) med prenumerations-ID:t som du vill växla till. Mer information om prenumerationsval finns i [Använda flera Azure-prenumerationer.](/cli/azure/manage-azure-subscriptions-azure-cli)

    Det finns några olika sätt att logga in som inte är interaktiva. Läs mer i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

6. **[Valfritt]** Skapa en ny Azure-resursgrupp:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installera Azure CLI Internet Analyzer tillägget:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Skapa en ny Internet Analyzer profil:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Visa en lista över alla förkonfigurerade slutpunkter som är tillgängliga för den nyligen skapade profilen:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Skapa ett nytt test under den nyligen skapade InternetAnalyzer-profilen:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Kommandot ovan förutsätter att både och är värdar för bilden med en `www.contoso.com` `www.microsoft.com` bildpunkt ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) under anpassade sökvägar. Om en objektsökväg inte anges explicit använder Internet Analyzer som objektsökväg som standard, vilket är den plats där de förkonfigurerade slutpunkterna är värdar för bilden med en `/apc/trans.gif` bildpunkt. Observera också att schemat (https/http) inte behöver anges. Internet Analyzer stöder endast HTTPS-slutpunkter, så HTTPS förutsätts.

11. Det nya testet bör visas under Internet Analyzer profilen:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Exempel på utdata:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Om du vill börja generera mått måste JavaScript-filen som testets **scriptFileUri** pekar på bäddas in i webbappen. Specifika anvisningar finns på sidan [Bädda in Internet Analyzer klient.](internet-analyzer-embed-client.md)

13. Du kan övervaka testets förlopp genom att hålla reda på dess statusvärde:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Du kan granska testets insamlade resultat genom att generera tidsserier eller poängkort för det:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Nästa steg

* Bläddra i [Internet Analyzer CLI-referensen](/cli/azure/internet-analyzer) för en fullständig lista över kommandon och användningsexempel som stöds.
* Läs vanliga [Internet Analyzer vanliga frågor och svar.](internet-analyzer-faq.md)
* Läs mer om att bädda in [Internet Analyzer client](internet-analyzer-embed-client.md) och skapa en [anpassad slutpunkt.](internet-analyzer-custom-endpoint.md)