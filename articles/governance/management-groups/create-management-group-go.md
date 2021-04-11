---
title: 'Snabb start: skapa en hanterings grupp med go'
description: I den här snabb starten använder du Go för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: bf2d2c556cfd6ada6d31fc6ee797888ed0899573
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091454"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Snabb start: skapa en hanterings grupp med go

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Ett huvud namn för Azure-tjänsten, inklusive _clientId_ och _clientSecret_. Om du inte har ett huvud namn för tjänsten som ska användas med Azure Policy eller vill skapa ett nytt, se [Azure-hanterings bibliotek för .net-autentisering](/dotnet/azure/sdk/authentication#mgmt-auth).
  Hoppa över steget för att installera .NET Core-paketen som vi gör i nästa steg.

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Lägg till hanterings grupp paketet

Om du vill aktivera gå till hantera hanterings grupper måste paketet läggas till. Det här paketet fungerar där Go kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerat.

1. Kontrol lera att den senaste go-installationen är installerad (minst **1,15**). Om den inte har installerats än kan du ladda ned den på [Golang.org](https://golang.org/dl/).

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om den inte har installerats än kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI krävs för att aktivera gå att använda- `auth.NewAuthorizerFromCLI()` metoden i följande exempel. Information om andra alternativ finns i [Azure SDK för go-mer autentiseringsinformation](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. I valfri miljö väljer du de paket som krävs för hanterings grupper:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Programkonfiguration

Med go-paket som har lagts till i din miljö kan du konfigurera Go-programmet som kan skapa en hanterings grupp.

1. Skapa Go-programmet och spara följande källa som `mgCreate.go` :

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Bygg Go-programmet:

   ```bash
   go build mgCreate.go
   ```

1. Skapa en hanterings grupp med hjälp av det kompilerade Go-programmet. Ersätt `<Name>` med namnet på den nya hanterings gruppen:

   ```bash
   mgCreate "<Name>"
   ```

Resultatet är en ny hanterings grupp i rot hanterings gruppen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade paketen från din go-miljö kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)