---
title: Distribuera en befintlig app snabbt till ett kluster
description: Använda en Azure Service Fabric-kluster som värd för ett befintligt Node.js-program med Visual Studio.
ms.topic: conceptual
ms.date: 12/06/2017
ms.custom: devx-track-js
ms.openlocfilehash: dfd1fe9db54925bc17a53a7686fa34d5ea53cd5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013606"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Skapa ett Node.js-program i Azure med Node.js

Den här snabbstarten hjälper dig att distribuera ett befintligt program (Node.js i det här exemplet) till ett Service Fabric-kluster som körs på Azure.

## <a name="prerequisites"></a>Förutsättningar

Du måste [konfigurera utvecklingsmiljön](service-fabric-get-started.md) innan du börjar. Inklusive installation av Service Fabric SDK och Visual Studio 2019 eller 2015.

Du måste också ha ett befintligt Node.js-program för distribution. Denna snabbstart använder en enkel Node.js-webbplats som du kan hämta [här][download-sample]. Extrahera filen till din `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\`-mapp när du har skapat projektet i nästa steg.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][create-account].

## <a name="create-the-service"></a>Skapa tjänsten

Starta Visual Studio som **administratör**.

Skapa ett projekt med `CTRL`+`SHIFT`+`N`

Klicka på **Moln > Service Fabric-program** i dialogrutan **Nytt projekt**.

Ge programmet namnet **MyGuestApp** och tryck på **skapa**.

>[!IMPORTANT]
>Node.js kan enkelt dela 260 tecken för sökvägar i windows. Använd en kort sökväg för projektet som **c:\code\svc1**. Alternativt kan du följa **[de här instruktionerna](https://stackoverflow.com/a/41687101/1664231)** för att aktivera långa filsökvägar i Windows 10.
   
![Dialogrutan Nytt projekt i Visual Studio][new-project]

Du kan skapa alla typer av Service Fabric-tjänster från nästa dialogruta. För den här snabbstarten, välj **kan köras av gäst**.

Namnge tjänsten **MyGuestService** och ange önskade alternativ till höger som följande värden:

| Inställning                   | Värde |
| ------------------------- | ------ |
| Kodpaketmapp       | _&lt;mappen med Node.js-appen&gt;_ |
| Kodpaketbeteende     | Kopiera innehållet i mappen till projektet |
| Program                   | node.exe |
| Argument                 | server.js |
| Arbetsmapp            | Kodpaket |

Tryck på **OK**.

![Dialogrutan Ny tjänst i Visual Studio][new-service]

Visual Studio skapar programprojektet och aktörtjänstprojektet och visar dem i Solution Explorer.

Programprojektet (**MyGuestApp**) innehåller ingen kod direkt. Projektet refererar till en uppsättning tjänst projekt. Dessutom innehåller den tre andra typer av innehåll:

* **Publicera profiler**  
Verktygsinställningar för olika miljöer.

* **Skript**  
PowerShell-skript för distribution/uppgradering av program.

* **Programdefinition**  
Innehåller programmanifestet under *ApplicationPackageRoot*. Associerade parameterfiler för programmet finns under *ApplicationParameters*, som definierar programmet och gör att du kan konfigurera det specifikt för en given miljö.
    
En översikt över innehållet i tjänstprojektet finns i [Komma igång med Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Konfigurera nätverk

För exemplet Node.js-appen som vi distribuerar används port **80** och vi behöver tala om för Service Fabric att porten ska exponeras.

Öppna filen **ServiceManifest.xml** i projektet. Längst ned i manifestet finns en `<Resources> \ <Endpoints>` med en-post som redan har definierats. Ändra posten för att lägga till `Port`, `Protocol` och `Type`. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Distribuera till Azure

Om du trycker på **F5** och kör projektet distribueras det till det lokala klustret. Men vi vill distribuera till Azure i stället.

Högerklicka på projektet och välj **Publicera...**, vilket öppnar en dialogruta för att publicera till Azure.

![Dialogrutan Publicera till azure för en service fabric-tjänst][publish]

Välj målprofilen **PublishProfiles\Cloud.xml**.

Om du inte gjort detta tidigare väljer du ett Azure-konto för distribution. Om du inte har en ännu, kan du [registrera dig för en][create-account].

Under **Anslutningens slutpunkt** väljer du Service Fabric-klustret att distribuera till. Om du inte har en sådan väljer du **&lt; Skapa nytt kluster.. &gt; .** som öppnar webbläsarfönstret till Azure Portal. Mer information finns i [Skapa ett kluster i portalen](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

När du skapar Service Fabric-kluster, se till att ange **Anpassade slutpunkter** till **80**.

![Service Fabric-nod-typkonfiguration med anpassad slutpunkt][custom-endpoint]

Det tar en stund att skapa ett nytt Service Fabric-kluster. När den har skapats går du tillbaka till dialog rutan publicera och väljer **&lt; uppdatera &gt;**. Det nya klustret visas i listrutan. Markera det.

Tryck på **Publicera** och vänta tills distributionen är klar.

Det kan ta några minuter. När den är klar kan det ta några minuter för att programmet ska vara helt tillgängligt.

## <a name="test-the-website"></a>Testa webbplatsen

När tjänsten har publicerats kan du testa den i en webbläsare. 

Först öppna Azure-portalen och sök efter Service Fabric-tjänsten.

Kontrollera översiktsbladet för tjänstadressen. Använda domännamnet från egenskapen _Klientanslutningsslutpunkt_. Till exempel `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Bladet Service fabric-översikt på Azure portal][overview]

Navigera till den här adressen där du ser `HELLO WORLD` svaret.

## <a name="delete-the-cluster"></a>Ta bort klustret

Glöm inte att ta bort alla resurser som du har skapat för den här snabb starten, eftersom du debiteras för dessa resurser.

## <a name="next-steps"></a>Nästa steg
Läs mer om [körbara filer för gäst](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
