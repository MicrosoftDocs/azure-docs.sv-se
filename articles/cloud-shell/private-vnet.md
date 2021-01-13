---
title: Cloud Shell i ett Azure-Virtual Network
description: Distribuera Cloud Shell till ett virtuellt Azure-nätverk
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: a820fb4ec5d6c2053b1104b68dbda0ae70f624c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179205"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Distribuera Cloud Shell till ett virtuellt Azure-nätverk

En vanlig Cloud Shell-session körs i en behållare i ett Microsoft-nätverk separat från dina resurser. Det innebär att kommandon som körs inuti behållaren inte kan komma åt resurser som endast kan nås från ett speciellt virtuellt nätverk. Du kan till exempel inte använda SSH för att ansluta från Cloud Shell till en virtuell dator som bara har en privat IP-adress eller använda kubectl för att ansluta till ett Kubernetes-kluster som har låst åtkomst. 

Den här valfria funktionen löser dessa begränsningar och låter dig distribuera Cloud Shell till ett virtuellt Azure-nätverk som du styr. Därifrån kan du interagera med resurser i det virtuella nätverk som du väljer.  

Nedan kan du se resurs arkitekturen som kommer att distribueras och användas i det här scenariot.

![Visar Cloud Shell isolerad VNET-arkitektur.](media/private-vnet/data-diagram.png)

Innan du kan använda Cloud Shell i din egen Azure-Virtual Network måste du skapa flera resurser som stöder den här funktionen. Den här artikeln visar hur du konfigurerar de resurser som krävs med en ARM-mall.

> [!NOTE]
> Dessa resurser behöver bara konfigureras en gång för det virtuella nätverket. De kan sedan delas av alla administratörer med åtkomst till det virtuella nätverket.

## <a name="required-network-resources"></a>Nödvändiga nätverks resurser

### <a name="virtual-network"></a>Virtuellt nätverk
Ett virtuellt nätverk definierar det adress utrymme som ett eller flera undernät skapas i.

Det önskade virtuella nätverket som ska användas för Cloud Shell måste identifieras. Detta är vanligt vis ett befintligt virtuellt nätverk som innehåller resurser som du vill hantera eller ett nätverk som peer-koppla med nätverk som innehåller dina resurser.

### <a name="subnet"></a>Undernät
I det valda virtuella nätverket måste ett dedikerat undernät användas för Cloud Shell behållare. Det här under nätet är delegerat till Azure Container Instances-tjänsten (ACI).  När en användare begär en Cloud Shell-behållare i ett virtuellt nätverk, Cloud Shell använder ACI för att skapa en behållare i det här delegerade under nätet.  Det går inte att skapa andra resurser i det här under nätet.

### <a name="network-profile"></a>Nätverksprofil
En nätverks profil är en mall för nätverks konfiguration för Azure-resurser som anger vissa nätverks egenskaper för resursen.

### <a name="azure-relay"></a>Azure Relay
En [Azure Relay](../azure-relay/relay-what-is-it.md) tillåter två slut punkter som inte kan kontaktas direkt för kommunikation. I det här fallet används den för att tillåta administratörens webbläsare att kommunicera med behållaren i det privata nätverket.

Azure Relay-instansen som används för Cloud Shell kan konfigureras för att styra vilka nätverk som kan komma åt behållar resurser: 
- Tillgängligt från det offentliga Internet: i den här konfigurationen är Cloud Shell ett sätt att nå ut i övrigt interna resurser utanför. 
- Tillgängligt från angivna nätverk: i den här konfigurationen kommer administratörer att ha åtkomst till Azure Portal från en dator som körs i ett lämpligt nätverk för att kunna använda Cloud Shell.

## <a name="storage-requirements"></a>Lagrings krav
Som i standard Cloud Shell krävs ett lagrings konto när du använder Cloud Shell i ett virtuellt nätverk. Varje administratör behöver en fil resurs för att lagra sina filer.  Lagrings kontot måste vara tillgängligt från det virtuella nätverk som används av Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Distributions begränsningar för virtuella nätverk
* På grund av de ytterligare nätverks resurser som berörs är det normalt långsammare att starta Cloud Shell i ett virtuellt nätverk än en standard-Cloud Shell-session.

* Alla Cloud Shell regioner förutom centrala Indien stöds för närvarande. 

* [Azure Relay](../azure-relay/relay-what-is-it.md) inte är en kostnads fri tjänst kan du se deras [priser](https://azure.microsoft.com/pricing/details/service-bus/). I Cloud Shell scenariot används en hybrid anslutning för varje administratör medan de använder Cloud Shell. Anslutningen stängs av automatiskt när Cloud Shell-sessionen har slutförts.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Microsoft. ContainerInstances-resurs leverantören måste registreras i den prenumeration som innehåller det virtuella nätverk som du vill använda. Välj lämplig prenumeration med `Set-AzContext -Subscription {subscriptionName}` och kör sedan:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Om **RegistrationState** är `Registered` krävs ingen åtgärd. Om det är `NotRegistered` kör du `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Distribuera nätverks resurser
 
### <a name="create-a-resource-group-and-virtual-network"></a>Skapa en resurs grupp och ett virtuellt nätverk
Om du redan har ett önskat VNET-nätverk som du vill ansluta till hoppar du över det här avsnittet.

I Azure Portal, eller med hjälp av Azure CLI, Azure PowerShell osv. skapa en resurs grupp och ett virtuellt nätverk i den nya resurs gruppen **måste resurs gruppen och det virtuella nätverket finnas i samma region**.

> [!NOTE]
> I offentlig för hands version måste resurs gruppen och det virtuella nätverket finnas i antingen WestCentralUS eller väst.

### <a name="arm-templates"></a>ARM-mallar
Använd [Azures snabb starts mall](https://aka.ms/cloudshell/docs/vnet/template) för att skapa Cloud Shell-resurser i ett virtuellt nätverk och i [Azure snabb starts mal len](https://aka.ms/cloudshell/docs/vnet/template/storage) för att skapa nödvändig lagring. Anteckna dina resurs namn, i huvudsak ditt fil resurs namn.

### <a name="open-relay-firewall"></a>Öppna Relay-brandvägg
Navigera till reläet som skapats med ovanstående mall, välj "nätverk" i inställningar och Tillåt åtkomst från webbläsarens nätverk till reläet. Som standard är reläet endast tillgängligt från det virtuella nätverk som det har skapats i. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Konfigurera Cloud Shell att använda ett virtuellt nätverk.
> [!NOTE]
> Det här steget måste utföras för att varje administratör ska kunna använda Cloud Shell.

När du har distribuerat klart stegen ovan navigerar du till Cloud Shell i Azure Portal eller på https://shell.azure.com . En av dessa upplevelser måste användas varje gången du vill ansluta till en isolerad Cloud Shell upplevelse.

> [!NOTE]
> Om Cloud Shell har använts tidigare, måste det befintliga clouddrive demonteras. Om du vill utföra den här körningen `clouddrive unmount` från en aktiv Cloud Shell-session uppdaterar du sidan.

Anslut till Cloud Shell får du en fråga om den första körningen. Välj önskad Shell-upplevelse, välj "Visa avancerade inställningar" och välj rutan "Visa inställningar för VNET-isolering". Fyll i fälten i popup-fönstret.  De flesta fält fylls i automatiskt på de tillgängliga resurser som kan associeras med Cloud Shell i ett virtuellt nätverk.  Fil resurs namnet måste fyllas i av användaren.


![Visar de Cloud Shell isolerade VNET-inställningarna för första upplevelse.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md)
