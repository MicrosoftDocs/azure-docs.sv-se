---
title: Konfigurera en utvecklings miljö i Azure våren Cloud | Microsoft Docs
description: Lär dig hur du använder blå-grön-distribution med Azure våren Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226124"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurera en utvecklings miljö i Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln beskriver hur du konfigurerar en mellanlagrings distribution med hjälp av ett blått-grönt distributions mönster i Azure våren Cloud. Blå/grön distribution är ett mönster för kontinuerlig leverans för Azure DevOps förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras. Den här artikeln visar hur du kan använda mellanlagrings distributionen i produktion utan att ändra produktions distributionen direkt.

## <a name="prerequisites"></a>Förutsättningar

* Azure våren Cloud-instansen med *standard* **pris nivån**.
* Ett program som körs.  Se [snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md).
* Tillägg för Azure CLI [ASC](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Om du vill använda ett annat program för det här exemplet måste du göra en enkel ändring i en offentlig del av programmet.  Den här ändringen särskiljer mellanlagrings distributionen från produktionen.

>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra instruktionerna i den här artikeln.  Den har vanliga, förinstallerade Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com).  Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

Följ anvisningarna i nästa avsnitt om du vill konfigurera en utvecklings miljö i Azure våren Cloud.

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Visa appar och distributioner

Visa distribuerade appar med följande procedurer.

1. Gå till Azure våren Cloud-instansen i Azure Portal.

1. Öppna **distributioner** i det vänstra navigerings fönstret.

    [![Distribution-föråldrad](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Öppna bladet "appar" för att visa appar för din tjänst instans.

    [![Appar – instrument panel](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Du kan klicka på en app och Visa information.

    [![Appar – Översikt](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Öppna bladet **distributioner** om du vill se alla distributioner av appen. Distributions rutnätet visar om distributionen är produktion eller mellanlagring.

    [![Distributions instrument panel](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Du kan klicka på namnet på distributionen för att Visa distributions översikten. I det här fallet heter den enda distributionen som *standard*.

    [![Översikt över distributioner](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Skapa en mellanlagrings distribution

1. Gör en liten ändring i ditt program i din lokala utvecklings miljö. På så sätt kan du enkelt särskilja de två distributionerna. Skapa jar-paketet genom att köra följande kommando: 

    ```console
    mvn clean package -DskipTests
    ```

1. Skapa en ny distribution i Azure CLI och ge den namnet "grön" för mellanlagrings distributionen.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. När CLI-distributionen har slutförts öppnar du appens sida från **instrument panelen för program** och visar alla dina instanser på fliken **distributioner** till vänster.

   [![Distributions instrument panel efter grön distribution](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> Identifierings statusen är *OUT_OF_SERVICE* så att trafiken inte dirigeras till den här distributionen innan verifieringen är klar.

## <a name="verify-the-staging-deployment"></a>Verifiera mellanlagrings distributionen

För att kontrol lera att den gröna utvecklings utvecklingen fungerar:
1. Gå till **distributioner** och klicka på `green` **mellanlagrings distributionen**.
1. På sidan **Översikt** klickar du på **test slut punkten**.
1. Då öppnas mellanlagrings versionen som visar dina ändringar.

>[!TIP]
> * Bekräfta att test slut punkten slutar med ett snedstreck (/) för att säkerställa att CSS-filen läses in på rätt sätt.  
> * Om din webbläsare kräver att du anger inloggnings uppgifter för att visa sidan använder du [URL-avkodning](https://www.urldecoder.org/) för att avkoda test slut punkten. URL-avkodning returnerar en URL i formatet "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green".  Använd det här formuläret för att få åtkomst till din slut punkt.

>[!NOTE]    
> Konfigurations Server inställningarna gäller både för din mellanlagrings miljö och produktion. Om du till exempel anger kontext Sök vägen ( `server.servlet.context-path` ) för din app gateway i config server som *somepath* ändras sökvägen till den gröna distributionen till "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green/somepath/...".
 
 Om du besöker den offentliga app-gatewayen bör du se den gamla sidan utan din nya ändring.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ange den gröna distributionen som produktions miljö

1. När du har verifierat din ändring i din mellanlagrings miljö kan du skicka den till produktion. Gå tillbaka till **distributions hantering** och välj det program som finns för närvarande i `Production` .

1. Klicka på ellipserna efter **registrerings status** och Ställ in produktions versionen på `staging` .

   [![Distributioner ange mellanlagrings distribution](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Gå tillbaka till sidan **distributions hantering** . Ange `green` distributionen till `production` . När inställningen är klar `green` bör distributionens status visas.  Detta är nu den produktions version som körs.

   [![Distributioner ange distributions resultat för mellanlagring](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Appens URL ska visa dina ändringar.

>[!NOTE]
> När du har angett den gröna distributionen som produktions miljö blir den tidigare distributionen den mellanlagrings distributionen.

## <a name="modify-the-staging-deployment"></a>Ändra mellanlagrings distributionen

Om du inte är nöjd med ändringen kan du ändra program koden, bygga ett nytt jar-paket och ladda upp det till din gröna distribution med hjälp av Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Ta bort mellanlagrings distributionen

Om du vill ta bort din mellanlagrings distribution från Azure-porten går du till sidan för mellanlagrings distribution och väljer sedan knappen **ta bort** .

Du kan också ta bort mellanlagrings distributionen från Azure CLI genom att köra följande kommando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Nästa steg

* [CI/CD för Azure våren Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
