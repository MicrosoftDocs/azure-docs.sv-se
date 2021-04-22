---
title: Så här distribuerar du OPC Vault-certifikathanteringstjänsten – Azure | Microsoft Docs
description: Så här distribuerar du OPC Vault-certifikathanteringstjänsten från grunden.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870291"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Skapa och distribuera OPC Vault-certifikathanteringstjänsten

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln finns [det senaste innehållet i Azure Industrial IoT.](https://azure.github.io/Industrial-IoT/)

Den här artikeln beskriver hur du distribuerar OPC Vault-certifikathanteringstjänsten i Azure.

> [!NOTE]
> Mer information finns i GitHub [OPC Vault-lagringsplatsen](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Förutsättningar

### <a name="install-required-software"></a>Installera nödvändig programvara

För närvarande är bygg- och distributionsåtgärden begränsad till Windows.
Exemplen är alla skrivna för C# .NET Standard, som du behöver för att skapa tjänsten och exemplen för distribution.
Alla verktyg som du behöver för .NET Standard kommer med .NET Core-verktygen. Se [Kom igång med .NET Core.](/dotnet/articles/core/getting-started)

1. [Installera .NET Core 2.1+][dotnet-install].
2. [Installera Docker][docker-url] (valfritt, endast om den lokala Docker-versionen krävs).
4. Installera [Azure-kommandoradsverktygen för PowerShell][powershell-install].
5. Registrera dig för en [Azure-prenumeration.][azure-free]

### <a name="clone-the-repository"></a>Klona lagringsplatsen

Om du inte har gjort det ännu klonar du den här GitHub-lagringsplatsen. Öppna en kommandotolk eller terminal och kör följande:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Du kan också klona lagringsplatsen direkt i Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Skapa och distribuera Azure-tjänsten i Windows

Ett PowerShell-skript är ett enkelt sätt att distribuera OPC Vault-mikrotjänsten och programmet.

1. Öppna ett PowerShell-fönster i lagringsplatsens rot. 
3. Gå till mappen deploy `cd deploy` .
3. Välj ett namn för `myResourceGroup` som sannolikt inte orsakar en konflikt med andra distribuerade webbsidor. Se avsnittet "Webbplatsnamn används redan" senare i den här artikeln.
5. Starta distributionen med `.\deploy.ps1` för interaktiv installation eller ange en fullständig kommandorad:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Om du planerar att utveckla med den här distributionen lägger du till för att aktivera `-development 1` Swagger UI och distribuera felsökningsbyggen.
6. Följ anvisningarna i skriptet för att logga in på din prenumeration och för att ange ytterligare information.
9. Efter en lyckad bygg- och distributionsåtgärd bör du se följande meddelande:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Om det uppstår problem kan du läsa avsnittet "Felsöka distributionsfel" senare i artikeln.

8. Öppna din favoritwebbläsare och öppna programsidan: `https://myResourceGroup.azurewebsites.net`
8. Ge webbappen och OPC Vault-mikrotjänsten några minuter att värma upp efter distributionen. Webbplatsens startsida kan sluta svara vid första användningen i upp till en minut tills du får de första svaren.
11. Om du vill ta en titt på Swagger-API:et öppnar du: `https://myResourceGroup-service.azurewebsites.net`
13. Starta för att starta en lokal DDRS-server med `.\myResourceGroup-gds.cmd` dotnet. Starta med `.\myResourceGroup-dockergds.cmd` Docker.

Det går att distribuera om en version med exakt samma inställningar. Tänk på att en sådan åtgärd förnyar alla programhemligheter och kan återställa vissa inställningar i Azure Active Directory-programregistreringar (Azure AD).

Det är också möjligt att distribuera om bara binärfilerna för webbappen. Med parametern `-onlyBuild 1` distribueras nya zip-paket för tjänsten och appen till webbprogrammen.

Efter en lyckad distribution kan du börja använda tjänsterna. Se [Hantera OPC Vault-certifikathanteringstjänsten](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Ta bort tjänsterna från prenumerationen

Så här gör du:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till resursgruppen där tjänsten har distribuerats.
3. Välj **Ta bort resursgrupp** och bekräfta.
4. Efter en kort stund tas alla distribuerade tjänstkomponenter bort.
5. Gå till **Azure Active Directory**  >  **Appregistreringar**.
6. Det bör finnas tre registreringar för varje distribuerad resursgrupp. Registreringarna har följande namn: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Ta bort varje registrering separat.

Nu tas alla distribuerade komponenter bort.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributionsfel

### <a name="resource-group-name"></a>Namn på resursgrupp

Använd ett kort och enkelt resursgruppsnamn. Namnet används också för att namnge resurser och tjänstens URL-prefix. Därför måste den uppfylla kraven på resursnamngivning.  

### <a name="website-name-already-in-use"></a>Webbplatsnamn som redan används

Det är möjligt att namnet på webbplatsen redan används. Du måste använda ett annat resursgruppsnamn. Värdnamnen som används av distributionsskriptet är: https: \/ /resourcegroupname.azurewebsites.net och https: \/ /resourgroupname-service.azurewebsites.net.
Andra namn på tjänster byggs genom en kombination av kortnamnshashar och står troligen inte i konflikt med andra tjänster.

### <a name="azure-ad-registration"></a>Azure Active Directory-registrering 

Distributionsskriptet försöker registrera tre Azure AD-program i Azure AD. Beroende på dina behörigheter i den valda Azure AD-klientorganisationen kan den här åtgärden misslyckas. Det finns två alternativ:

- Om du väljer en Azure AD-klientorganisation från en lista över klienter startar du om skriptet och väljer en annan från listan.
- Du kan också distribuera en privat Azure AD-klientorganisation i en annan prenumeration. Starta om skriptet och välj att använda det.

## <a name="deployment-script-options"></a>Alternativ för distributionsskript

Skriptet använder följande parametrar:


```
-resourceGroupName
```

Detta kan vara namnet på en befintlig eller en ny resursgrupp.

```
-subscriptionId
```


Det här är prenumerations-ID:t där resurser ska distribueras. Det är valfritt.

```
-subscriptionName
```


Du kan också använda prenumerationsnamnet.

```
-resourceGroupLocation
```


Det här är en resursgruppsplats. Om den här parametern anges försöker den skapa en ny resursgrupp på den här platsen. Den här parametern är också valfri.


```
-tenantId
```


Det här är Den Azure AD-klientorganisation som ska användas. 

```
-development 0|1
```

Detta är för att distribuera för utveckling. Använd felsökningsbygge och ange ASP.NET miljö till Utveckling. Skapa `.publishsettings` för import i Visual Studio 2017 så att den kan distribuera appen och tjänsten direkt. Den här parametern är också valfri.

```
-onlyBuild 0|1
```

Det här är för att återskapa och omdistribuera endast webbapparna och återskapa Docker-containrarna. Den här parametern är också valfri.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC Vault från grunden kan du:

> [!div class="nextstepaction"]
> [Hantera OPC-valv](howto-opc-vault-manage.md)