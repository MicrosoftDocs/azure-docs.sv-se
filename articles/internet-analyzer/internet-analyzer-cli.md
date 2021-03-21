---
title: Skapa ett Internet Analyzer-test med CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du skapar ditt första Internet Analyzer-test med hjälp av Azure CLI.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a9a9fe93ebe302a76d69249dc56933e1bcc924d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200095"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Skapa ett Internet Analyzer-test med CLI (för hands version)

Det finns två sätt att skapa en Internet Analyzer-resurs – med hjälp av [Azure Portal](internet-analyzer-create-test-portal.md) eller med cli. Det här avsnittet hjälper dig att skapa en ny Azure Internet Analyzer-resurs med hjälp av vår CLI-upplevelse. 


> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga för hands versionen är tillgänglig för användning globalt; data lagring är dock begränsad till *västra USA 2* under för hands versionen.

## <a name="object-model"></a>Objekt modell
Internet Analyzer CLI exponerar följande typer av resurser:
* **Tester** – ett test jämför slut användar prestandan för två Internet slut punkter (A och B) över tid.
* **Profiler** – tester skapas under en Internet Analyzer-profil. Profiler gör att relaterade tester kan grupperas. en enskild profil kan innehålla ett eller flera tester.
* **Förkonfigurerade slut punkter** – vi har ställt in slut punkter med olika konfigurationer (regioner, accelerations tekniker osv.). Du kan använda någon av dessa förkonfigurerade slut punkter i dina tester.
* **Styrkort** – ett styrkort ger snabba och meningsfulla sammanfattningar av mått resultatet. Se [tolka ditt styrkort](internet-analyzer-scorecard.md).
* **Tids serier** – en tids serie visar hur ett mått förändras över tid.

## <a name="profile-and-test-creation"></a>Profil-och test skapande
1. Få till gång till Internet Analyzer Preview genom att följa **Hur gör jag för att delta i för hands versionen?** instruktioner från [vanliga frågor och svar om Azure Internet Analyzer](internet-analyzer-faq.md).
2. [Installera Azure CLI](/cli/azure/install-azure-cli).
3. Kör `login` kommandot för att starta en CLI-session:
    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.
    Annars öppnar du en webb sida på https://aka.ms/devicelogin och anger den auktoriseringskod som visas i din terminal.

4. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

5. Välj ditt prenumerations-ID som har beviljats åtkomst till den offentliga Internet Analyzer-förhands granskningen.

    När du har loggat in visas en lista över prenumerationer som är associerade med ditt Azure-konto. Prenumerations informationen med `isDefault: true` är den aktuella aktiverade prenumerationen när du har loggat in. Om du vill välja en annan prenumeration använder du kommandot [AZ Account set](/cli/azure/account#az-account-set) med det PRENUMERATIONS-ID som du vill växla till. Mer information om val av prenumeration finns i [använda flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).

    Det finns några olika sätt att logga in som inte är interaktiva. Läs mer i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

6. **[Valfritt]** Skapa en ny Azure-resurs grupp:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installera Azure CLI Internet Analyzer-tillägget:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Skapa en ny Internet Analyzer-profil:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Lista alla förkonfigurerade slut punkter som är tillgängliga för den nyligen skapade profilen:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Skapa ett nytt test under den nyligen skapade InternetAnalyzer-profilen:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Kommandot ovan förutsätter att både `www.contoso.com` och `www.microsoft.com` är värdar för bilden med en bild punkt ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) under anpassade sökvägar. Om en objekt Sök väg inte anges explicit, används `/apc/trans.gif` som standard objekt Sök vägen i Internet Analyzer, vilket är den förkonfigurerade slut punkten som är värd för en bild av en bild punkt. Observera också att schemat (https/http) inte behöver anges. Internet Analyzer har endast stöd för HTTPS-slutpunkter, så HTTPS antas.

11. Det nya testet bör visas under Internet Analyzer-profilen:
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

12. För att börja skapa mätningar måste JavaScript-filen som testens **scriptFileUri** pekar på vara inbäddad i webb programmet. Du hittar mer information på sidan [bädda in Internet Analyzer-klienten](internet-analyzer-embed-client.md) .

13. Du kan övervaka testens förlopp genom att hålla reda på dess "status"-värde:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Du kan kontrol lera testens insamlade resultat genom att generera timeseries eller styrkort:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Nästa steg

* Bläddra i [Internet Analyzer CLI-referensen](/cli/azure/ext/internet-analyzer/internet-analyzer) för en fullständig lista över kommandon och användnings exempel som stöds.
* Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md).
* Lär dig mer om att bädda in [Internet Analyzer-klienten](internet-analyzer-embed-client.md) och skapa en [anpassad slut punkt](internet-analyzer-custom-endpoint.md).