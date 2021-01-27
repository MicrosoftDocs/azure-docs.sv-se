---
title: 'Snabb start: din första ruby-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram symbolen för ruby och kör din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920048"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Snabb start: kör din första resurs diagram fråga med ruby

Det första steget för att använda Azure Resource Graph är att kontrol lera att nödvändiga Gems för ruby är installerade. Den här snabb starten vägleder dig genom processen att lägga till Gems i ruby-installationen.

I slutet av den här processen har du lagt till Gems i ruby-installationen och kör din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Ett huvud namn för Azure-tjänsten, inklusive _clientId_ och _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Skapa resurs diagrams projektet

Om du vill aktivera ruby för att fråga Azure Resource Graph måste du lägga till symbolen i `Gemfile` . Den här symbolen fungerar där ruby kan användas, inklusive med [Azure Cloud Shell](https://shell.azure.com), [bash på Windows 10](/windows/wsl/install-win10)eller lokalt installerat.

1. Kontrol lera att den senaste ruby-filen är installerad (minst **2.7.1**). Om den inte har installerats än kan du ladda ned den på [ruby-lang.org](https://www.ruby-lang.org/en/downloads/).

1. I den ruby-miljö du väljer initierar du ett paket i en ny projektmapp:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Uppdatera din `Gemfile` med den Gems som krävs för Azure Resource Graph. Den uppdaterade filen bör se ut ungefär som i det här exemplet:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Kör i mappen Project `bundle install` . Bekräfta att Gems installerades med `bundle list` .

1. Skapa med följande kod i samma projektmapp `argQuery.rb` och spara den uppdaterade filen:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

När ruby-skriptet är sparat och klart att använda är det dags att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

I varje anrop till finns `argQuery` det variabler som du måste ersätta med dina egna värden:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{clientId}` -Ersätt med klient-ID: t för tjänstens huvud namn
- `{clientSecret}` -Ersätt med klient hemligheten för tjänstens huvud namn
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

1. Ändra kataloger till den projektmapp där du skapade `Gemfile` `argClient.rb` filerna och.

1. Kör din första Azure Resource Graph-fråga med hjälp av Gems och `resources` metoden:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Ändra den sista parametern till `argQuery.rb` och ändra frågan till `order by` egenskapen **namn** :

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Ändra den sista parametern till `argQuery.rb` och ändra frågan till först `order by` egenskapen **namn** och sedan `limit` till de fem främsta resultaten:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade Gems från ruby-miljön kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> Symbolen `azure_mgmt_resourcegraph` har beroenden som `ms_rest` och `ms_rest_azure` som även kan ha installerats beroende på din miljö. Du kan avinstallera dessa Gems även om de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs diagram Gems i ruby-miljön och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
