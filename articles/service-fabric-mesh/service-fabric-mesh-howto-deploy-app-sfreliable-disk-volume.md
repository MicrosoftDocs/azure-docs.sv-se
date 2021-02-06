---
title: Service Fabric tillförlitlig disk volym med Service Fabric nät
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric nätprogram genom att montera Service Fabric tillförlitlig diskbaserad volym i behållaren med hjälp av Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625794"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montering med hög tillgänglighet Service Fabric tillförlitlig disk baserad volym i ett Service Fabric nätprogram 

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den gemensamma metoden för att bevara tillstånd med behållar appar är att använda Fjärrlagring som Azure File Storage eller databas som Azure Cosmos DB. Detta medför betydande Läs-och skriv nätverks fördröjning i fjärrarkivet.

Den här artikeln visar hur du lagrar tillstånd med hög tillgänglighet Service Fabric tillförlitlig disk genom att montera en volym inuti behållaren för ett Service Fabric nätprogram.
Service Fabric Reliable disk tillhandahåller volymer för lokala läsningar med skrivningar som repliker ATS i Service Fabric klustret för hög tillgänglighet. Detta tar bort nätverks anrop för läsningar och minskar nätverks fördröjningen för skrivningar. Om behållaren startas om eller flyttas till en annan nod, kommer den nya behållar instansen att se samma volym som en äldre instans. Därför är den både effektiv och hög tillgänglig.

I det här exemplet har Counter-programmet en ASP.NET Core-tjänst med en webb sida som visar räknar värde i en webbläsare.

Med `counterService` jämna mellanrum läser du ett räknar värde från en fil, ökar den och skriver tillbaka den till filen. Filen lagras i en mapp som är monterad på volymen som backas upp av Service Fabric tillförlitlig disk.

## <a name="prerequisites"></a>Förutsättningar

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra uppgiften. Om du vill använda Azure CLI med den här artikeln kontrollerar du att `az --version` returnerar minst `azure-cli (2.0.43)` .  Installera (eller uppdatera) Azure Service Fabric mask CLI-modulen för tillägg genom att följa dessa [anvisningar](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som programmet ska distribueras till. Följande kommando skapar en resurs grupp med namnet `myResourceGroup` på en plats i östra USA. Om du ändrar resurs grupps namnet i nedanstående kommando, måste du komma ihåg att ändra det i alla kommandon som följer.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Distribuera mallen

>[!NOTE]
> Från och med den 2 november 2020 [gäller nedladdnings hastighets gränserna](https://docs.docker.com/docker-hub/download-rate-limit/) för anonyma och autentiserade begär anden till Docker Hub från Docker-fri Plans konton och tillämpas med IP-adress. 
> 
> Den här mallen använder offentliga avbildningar från Docker Hub. Observera att du kan begränsas till en begränsad hastighet. Mer information finns i [autentisera med Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Följande kommando distribuerar ett Linux-program med hjälp av [counter.sfreliablevolume.linux.jspå mall](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Använd [counter.sfreliablevolume.windows.jspå-mallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)för att distribuera ett Windows-program. Tänk på att det kan ta längre tid att distribuera större behållar avbildningar.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Du kan också se status för distributionen med kommandot

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Lägg märke till namnet på den gateway-resurs som har resurs typen som `Microsoft.ServiceFabricMesh/gateways` . Detta används för att hämta appens offentliga IP-adress.

## <a name="open-the-application"></a>Öppna programmet

Hämta IP-adressen för gateway-resursen för appen när programmet har distribuerats. Använd det Gateway-namn som du noterade i avsnittet ovan.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Utdata ska ha en egenskap `ipAddress` som är den offentliga IP-adressen för tjänstens slut punkt. Öppna den från en webbläsare. En webb sida med det räknar värde som uppdateras varje sekund visas.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrol lera att programmet kan använda volymen

Programmet skapar en fil med namnet `counter.txt` i mappen volym i `counter/counterService` . Innehållet i den här filen är det räknar värde som visas på webb sidan.

## <a name="delete-the-resources"></a>Ta bort resurserna

Ta ofta bort de resurser som du inte längre använder i Azure. Ta bort de resurser som är relaterade till det här exemplet genom att ta bort resurs gruppen där de distribuerades (vilket tar bort allt som är associerat med resurs gruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Visa Service Fabric Reliable Volume disk-exempelprogrammet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).