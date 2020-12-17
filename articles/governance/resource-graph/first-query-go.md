---
title: 'Snabb start: din första go-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram paketet för go och kör din första fråga.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: e09bbb63b56fd30eb3aa9e7e527acdd8691d3ee6
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655806"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Snabb start: kör din första resurs diagram fråga med go

Det första steget för att använda Azure Resource Graph är att kontrol lera att de nödvändiga paketen för Go är installerade. Den här snabb starten vägleder dig genom processen att lägga till paketen i din go-installation.

I slutet av den här processen har du lagt till paketen i go-installationen och kört din första resurs diagram fråga.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="add-the-resource-graph-package"></a>Lägg till resurs diagram paketet

Om du vill aktivera gå till fråga Azure Resource Graph måste paketet läggas till. Det här paketet fungerar där Go kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrol lera att den senaste go-installationen är installerad (minst **1,14**). Om den inte har installerats än kan du ladda ned den på [Golang.org](https://golang.org/dl/).

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om den inte har installerats än kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI krävs för att aktivera gå att använda- `auth.NewAuthorizerFromCLI()` metoden i följande exempel. Information om andra alternativ finns i [Azure SDK för go-mer autentiseringsinformation](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I valfri miljö väljer du de paket som krävs för Azure Resource Graph:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Med go-paket som har lagts till i den miljö du väljer är det dags att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

1. Skapa Go-programmet och spara följande källa som `argQuery.go` :

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Bygg Go-programmet:

   ```bash
   go build argQuery.go
   ```

1. Kör din första Azure Resource Graph-fråga med hjälp av det kompilerade Go-programmet. Ersätt `<SubID>` med ditt prenumerations-ID:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Ändra den första parametern till `argQuery` och ändra frågan till `order by` egenskapen **namn** . Ersätt `<SubID>` med ditt prenumerations-ID:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Ändra den första parametern till `argQuery` och ändra frågan till första `order by` egenskapen **namn** och sedan `limit` till de fem översta resultaten. Ersätt `<SubID>` med ditt prenumerations-ID:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade paketen från din go-miljö kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs diagram paketen i din go-miljö och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)