---
title: Så här distribuerar du OPC Vault Certificate Management Service – Azure | Microsoft Docs
description: Så här distribuerar du OPC-valvet certifikat hanterings tjänst från grunden.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646383"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Bygga och distribuera certifikat hanterings tjänsten för OPC Vault

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln förklarar hur du distribuerar OPC-valvet för certifikat hantering i Azure.

> [!NOTE]
> Mer information finns i GitHub [OPC Vault-lagringsplatsen](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Förutsättningar

### <a name="install-required-software"></a>Installera nödvändig program vara

För närvarande är åtgärden för att bygga och distribuera begränsad till Windows.
Exemplen är skrivna för C# .NET standard, som du behöver för att bygga tjänsten och exempel för distribution.
Alla verktyg du behöver för .NET standard levereras med .NET Core-verktyg. Se [Kom igång med .net Core](/dotnet/articles/core/getting-started).

1. [Installera .net Core 2.1 +][dotnet-install].
2. [Installera Docker][docker-url] (valfritt, endast om den lokala Docker-versionen krävs).
4. Installera [Azures kommando rads verktyg för PowerShell][powershell-install].
5. Registrera dig för en [Azure-prenumeration][azure-free].

### <a name="clone-the-repository"></a>Klona lagringsplatsen

Klona den här GitHub-lagringsplatsen om du inte redan gjort det. Öppna en kommando tolk eller Terminal och kör följande:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Du kan också klona lagrings platsen direkt i Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Bygg och Distribuera Azure-tjänsten i Windows

Ett PowerShell-skript är ett enkelt sätt att distribuera OPC Vault mikrotjänster och programmet.

1. Öppna ett PowerShell-fönster i lagrings platsen-roten. 
3. Gå till mappen distribuera `cd deploy` .
3. Välj ett namn för `myResourceGroup` det är osannolikt att orsaka en konflikt med andra distribuerade webb sidor. Se avsnittet "webbplats namn som redan används" längre fram i den här artikeln.
5. Starta distributionen med `.\deploy.ps1` för interaktiv installation eller ange en fullständig kommando rad:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Om du planerar att utveckla med den här distributionen lägger du till `-development 1` för att aktivera Swagger-användargränssnittet och distribuera fel söknings versioner.
6. Följ instruktionerna i skriptet för att logga in på din prenumeration och ange ytterligare information.
9. När du har skapat och distribuerat en lyckad åtgärd bör du se följande meddelande:
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
   > I händelse av problem kan du läsa avsnittet "Felsöka distributions fel" senare i artikeln.

8. Öppna din favorit webbläsare och öppna program sidan: `https://myResourceGroup.azurewebsites.net`
8. Ge webbappen och OPC-valvet en mikrotjänst några minuter att värma upp efter distributionen. Webb start sidan kan sluta svara vid första användningen, i upp till en minut tills du får de första svaren.
11. Ta en titt på Swagger-API: et genom att öppna: `https://myResourceGroup-service.azurewebsites.net`
13. Starta en lokal GDS-server med dotNet genom att starta `.\myResourceGroup-gds.cmd` . Starta med Docker `.\myResourceGroup-dockergds.cmd` .

Det går att distribuera om en version med exakt samma inställningar. Tänk på att en sådan åtgärd förnyar alla program hemligheter och kan återställa vissa inställningar i program registreringarna i Azure Active Directory (Azure AD).

Det är också möjligt att distribuera om bara webbappens binärfiler. Med-parametern `-onlyBuild 1` distribueras nya zip-paket för tjänsten och appen till webb programmen.

När distributionen är klar kan du börja använda tjänsterna. Se [Hantera certifikat hanterings tjänsten för OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Ta bort tjänsterna från prenumerationen

Så här gör du:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till resurs gruppen där tjänsten distribuerades.
3. Välj **Ta bort resursgrupp** och bekräfta.
4. Efter en kort stund tas alla distribuerade tjänst komponenter bort.
5. Gå till **Azure Active Directory**  >  **Appregistreringar**.
6. Det bör finnas tre registreringar för varje distribuerad resurs grupp. Registreringarna har följande namn: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Ta bort varje registrering separat.

Nu tas alla distribuerade komponenter bort.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel

### <a name="resource-group-name"></a>Namn på resursgrupp

Använd ett kort och enkelt resurs grupp namn. Namnet används också för att namnge resurser och URL-prefixet för tjänsten. Det måste därför uppfylla kraven på resurs namn.  

### <a name="website-name-already-in-use"></a>Webbplats namnet används redan

Det är möjligt att namnet på webbplatsen redan används. Du måste använda ett annat resurs grupps namn. Värd namnen som används av distributions skriptet är: https: \/ /ResourceGroupName.azurewebsites.net och https: \/ /resourgroupname-service.azurewebsites.net.
Andra namn på tjänster skapas med kombinationen av korta namn-hashar och är sannolikt inte en konflikt med andra tjänster.

### <a name="azure-ad-registration"></a>Azure Active Directory-registrering 

Distributions skriptet försöker registrera tre Azure AD-program i Azure AD. Den här åtgärden kan Miss förväntas beroende på dina behörigheter i den valda Azure AD-klienten. Det finns två alternativ:

- Om du väljer en Azure AD-klient från en lista över klienter startar du om skriptet och väljer ett annat i listan.
- Du kan också distribuera en privat Azure AD-klient i en annan prenumeration. Starta om skriptet och välj att använda det.

## <a name="deployment-script-options"></a>Distributions skript alternativ

Skriptet använder följande parametrar:


```
-resourceGroupName
```

Detta kan vara namnet på en befintlig eller en ny resurs grupp.

```
-subscriptionId
```


Detta är det prenumerations-ID där resurser ska distribueras. Det är valfritt.

```
-subscriptionName
```


Du kan också använda prenumerations namnet.

```
-resourceGroupLocation
```


Det här är en resurs grupps plats. Om den här parametern anges försöker den här parametern skapa en ny resurs grupp på den här platsen. Den här parametern är också valfri.


```
-tenantId
```


Det här är Azure AD-klienten som ska användas. 

```
-development 0|1
```

Detta är att distribuera för utveckling. Använd fel söknings version och Ställ in ASP.NET-miljön på utveckling. Skapa `.publishsettings` för import i Visual Studio 2017 så att den kan distribuera appen och tjänsten direkt. Den här parametern är också valfri.

```
-onlyBuild 0|1
```

Detta är att bygga om och omdistribuera bara webbapparna och återskapa Docker-behållare. Den här parametern är också valfri.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-valvet från grunden kan du:

> [!div class="nextstepaction"]
> [Hantera OPC-valv](howto-opc-vault-manage.md)