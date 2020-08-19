---
title: Självstudie om att distribuera till ett befintligt virtuellt nätverk med hjälp av PowerShell – Dedikerad HSM i Azure | Microsoft Docs
description: Självstudie som visar hur du distribuerar en dedikerad HSM med hjälp av PowerShell till ett befintligt virtuellt nätverk
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2020
ms.author: johndaw
ms.openlocfilehash: 93333f2f0a2ef4aaf44884aa30afaa959459aaad
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257430"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Självstudie – Distribuera HSM:er till ett befintligt virtuellt nätverk med hjälp av PowerShell

Tjänsten Dedikerad HSM i Azure tillhandahåller en fysisk enhet enbart för kundanvändning, med fullständig administrativ kontroll och fullständigt hanteringsansvar. Eftersom Microsoft tillhandahåller fysisk maskinvara måste Microsoft styra hur dessa enheter allokeras för att säkerställa att kapaciteten hanteras på ett effektivt sätt. I en Azure-prenumeration syns tjänsten Dedikerad HSM därför normalt inte för resursetablering. Azure-kunder som behöver åtkomst till tjänsten Dedikerad HSM måste först kontakta sin Microsoft-kontoansvarige och begära registrering för tjänsten Dedikerad HSM. Det är först när den här processen är klar som etablering blir möjlig.
Den här självstudien visar en typisk etableringsprocess där:

- En kund redan har ett virtuellt nätverk
- Kunden har en virtuell dator
- Kunde måste lägga till HSM-resurser i den befintliga miljön.

En typisk arkitektur med hög tillgänglighet för distribution i flera regioner kan se ut så här:

![distribution i flera regioner](media/tutorial-deploy-hsm-powershell/high-availability.png)

Den här självstudien fokuserar på integreringen av ett par HSM:er och den nödvändiga ExpressRoute-gatewayen (se Undernät 1 ovan) i ett befintligt virtuellt nätverk (se VNET 1 ovan).  Alla andra resurser är Azure-standardresurser. Samma integreringsprocess kan användas för HSM:er i undernät 4 på VNET 3 ovan.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Dedikerad HSM i Azure är inte tillgängligt i Azure-portalen. Därför sker all interaktion med tjänsten via kommandoraden eller PowerShell. Den här självstudien använder PowerShell i Azure Cloud Shell. Om PowerShell är nytt för dig följer du instruktionerna för att komma igång här: [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

Antaganden:

- Du har gått igenom registreringsprocessen för Dedikerad HSM i Azure och godkänts för användning av tjänsten. Annars kontaktar du din Microsoft-kontorepresentant för att få information. 
- Du har skapat en resursgrupp för dessa resurser, och de nya som distribueras i den här självstudien ansluts till den gruppen.
- Du redan har skapat nödvändiga virtuella nätverk, undernät och virtuella datorer enligt diagrammet ovan och vill nu integrera två HSM:er i den distributionen.

Alla anvisningar nedan förutsätter att du redan har navigerat till Azure-portalen och öppnat Cloud Shell (Välj ”\>\_” längst upp till höger i portalen).

## <a name="provisioning-a-dedicated-hsm"></a>Etablera en Dedikerad HSM

Etableringen av HSM:er och integreringen i ett befintligt virtuellt nätverk via ExpressRoute-Gateway verifieras med SSH-kommandoradsverktyget för att säkerställa HSM-enhetens nåbarhet och grundläggande tillgänglighet för ytterligare konfigurationsaktiviteter. Följande kommandon använder en Resource Manager-mall för att skapa HSM-resurserna och tillhörande nätverksresurser.

### <a name="validating-feature-registration"></a>Verifiera funktionsregistrering

Som nämnts ovan kräver alla etableringsaktiviteter att tjänsten Dedikerad HSM har registrerats för din prenumeration. För att verifiera det kör du följande PowerShell-kommando i Azure-portalens Cloud Shell. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Kommandot ska returnera statusen "registrerad" (som visas nedan) innan du fortsätter.  Kontakta din Microsoft-konto representant om du inte har registrerat dig för den här tjänsten.

![prenumerationsstatus](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Skapa HSM-resurser

En HSM-enhet etableras till en kunds virtuella nätverk. Detta förutsätter kravet för ett undernät. Ett beroende som krävs för att HSM ska möjliggöra kommunikation mellan det virtuella nätverket och en fysisk enhet är en ExpressRoute-Gateway. Slutligen krävs en virtuell dator för att få åtkomst till HSM-enheten med hjälp av Gemalto-klientprogrammet. Dessa resurser har samlats i en mallfil med motsvarande parameterfil för enkel användning. Du kan hämta filerna genom att kontakta Microsoft direkt på HSMrequest@Microsoft.com.

När du har filerna behöver du redigera parameterfilen om du vill infoga dina önskade namn för resurser. Det innebär att redigera rader med ”value”: ””.

- `namingInfix` Prefix för namn på HSM-resurser
- `ExistingVirtualNetworkName` Namn på det virtuella nätverk som används för HSM:erna
- `DedicatedHsmResourceName1` Namnet på HSM-resursen i datacenterstämpel 1
- `DedicatedHsmResourceName2` Namnet på HSM-resursen i datacenterstämpel 2
- `hsmSubnetRange` Undernätets IP-adressintervall för HSM:er
- `ERSubnetRange` Undernätets IP-adressintervall för VNET-gateway

Ett exempel på dessa ändringar är följande:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Den associerade Resource Manager-mallfilen skapar 6 resurser med den här informationen:

- Ett undernät för HSM:erna i det angivna virtuella nätverket
- Ett undernät för den virtuella nätverksgatewayen 
- En virtuell nätverksgateway som ansluter det virtuella nätverket till HSM-enheterna
- En offentlig IP-adress för gatewayen
- En HSM i stämpel 1
- En HSM i stämpel 2

När parametervärden har angetts måste filerna laddas upp till Azure-portalens Cloud Shell-filresurs för användning. På Azure-portalen klickar du på Cloud Shell-symbolen ”\>\_” längst upp till höger så blir den nedre delen av skärmen en kommandomiljö. Alternativen för detta är BASH och PowerShell, och du bör välja BASH om det inte redan har angetts.

Kommandogränssnittet innehåller ett alternativ för uppladdning/nedladdning i verktygsfältet, och du bör ange det här till att ladda upp milen och parameterfilerna till din filresurs:

![filresurs](media/tutorial-deploy-hsm-powershell/file-share.png)

När filerna har laddats upp är du redo att skapa resurser.
Innan du skapar nya HSM-resurser finns det vissa nödvändiga resurser som du bör kontrollera så att de finns plats. Du måste ha ett virtuellt nätverk med undernätsintervall för beräkning, HSM:er och gateway. Följande kommandon utgör ett exempel på vad som skulle skapa ett sådant virtuellt nätverk.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Den viktigaste konfigurationen att observera för det virtuella nätverket är att undernätet för HSM-enheten måste ha delegeringar inställda på ”Microsoft.HardwareSecurityModules/dedicatedHSMs”.  HSM-etableringen fungerar inte utan detta.

När alla förutsättningar är uppfyllda kör du följande kommando för att använda Resource Manager-mallen. Se till att du har uppdaterat värdena med dina unika namn (åtminstone resursgruppens namn):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Det här kommandot tar cirka 20 minuter att slutföra. Alternativet ”-verbose” säkerställer att status visas kontinuerligt.

![etableringsstatus](media/tutorial-deploy-hsm-powershell/progress-status.png)

När det klart, vilket visas med ”provisioningState”: ”Succeeded” (Lyckades) kan du logga in på den befintliga virtuella datorn och använd SSH för att säkerställa tillgängligheten för HSM-enheten.

## <a name="verifying-the-deployment"></a>Kontrollera distributionen

Kontrollera att enheterna har etablerats och visa enhetsattribut genom att köra följande kommandouppsättning. Kontrollera att resursgruppen har angetts på rätt sätt och att resursnamnet är exakt samma som det du har i parameterfilen.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![etableringsstatus](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Du kan nu även se resurser med hjälp av [Azure-resursutforskaren](https://resources.azure.com/).   När du är i utforskaren expanderar du ”prenumerationer” till vänster, din specifika prenumeration för Dedikerad HSM, ”resursgrupper” samt den resursgrupp som du använde och väljer slutligen objektet ”resurser”.

## <a name="testing-the-deployment"></a>Testa distributionen

Att testa distributionen innebär att ansluta till en virtuell dator som kan komma åt HSM:er och sedan ansluta direkt till HSM-enheten. De här åtgärderna bekräftar att HSM kan nås.
SSH-verktyget används för att ansluta till den virtuella datorn. Kommandot liknar följande men med det administratörsnamn och DNS-namn som du angav i parametern.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Lösenordet är det som kommer från parameterfilen.
När du har loggat in på den virtuella Linux-datorn kan du logga in till HSM med hjälp av den privata IP-adress som finns i portalen för resursen \<prefix>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
När du har IP-adressen kör du följande kommando:

`ssh tenantadmin@<ip address of HSM>`

Om detta lyckas uppmanas du att ange ett lösenord. Standardlösenordet är PASSWORD. HSM ber dig att ändra ditt lösenord, så ange ett starkt lösenord och använd den mekanism som din organisation föredrar för att lagra lösenordet och förhindra förlust.  

>[!IMPORTANT]
>om du tappar bort det här lösenordet måste HSM återställas, vilket innebär att dina nycklar går förlorade.

När du är ansluten till HSM-enheten med hjälp av SSH kör du följande kommando för att se till att HSM är i drift.

`hsm show`

Utdata bör se ut som på bilden nedan:

![etableringsstatus](media/tutorial-deploy-hsm-powershell/output.png)

I det här skedet har du allokerat alla resurser för en distribution med hög tillgänglighet och två HSM:er samt verifierat åtkomst och driftstatus. Ytterligare konfiguration eller testning medför mer arbete med själva HSM-enheten. För detta bör du följa anvisningarna i kapitel 7 i administrationsguiden för Gemalto Luna Network HSM 7 för att initiera HSM och skapa partitioner. All dokumentation och programvara är tillgänglig direkt från Gemalto för nedladdning när du har registrerats i Gemalto Customer Support Portal och har ett kund-ID. Ladda ned klientprogramvara version 7.2 för att få alla nödvändiga komponenter.

## <a name="delete-or-clean-up-resources"></a>Ta bort eller rensa resurser

Om du är klar med bara HSM-enheten kan den tas bort som resurs och returneras till poolen. Det uppenbara problemet när du gör detta är eventuella känsliga kunddata som finns på enheten. Det bästa sättet att "zeroize" en enhet är att få lösen ordet för HSM-administratören fel tre gånger (Obs! det här är inte enhets administratören, det är den faktiska HSM-administratören). Som säkerhets åtgärd för att skydda nyckel material kan enheten inte tas bort som en Azure-resurs förrän den är i ett nollställt tillstånd.

> [!NOTE]
> om det är problem med någon Gemalto-enhetskonfiguration bör du kontakta [Gemaltos kundsupport](https://safenet.gemalto.com/technical-support/).

Om du vill ta bort HSM-resursen i Azure kan du använda följande kommando för att ersätta "$"-variablerna med dina unika parametrar:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Nästa steg

När du har slutfört stegen i självstudien etableras resurser för Dedikerad HSM och blir tillgängliga i ditt virtuella nätverk. Du har nu möjligheten att komplettera den här distributionen med fler resurser efter behov för valfri distributionsarkitektur. Mer information som hjälper dig att planera distributionen finns i begreppsdokumenten. En design med två HSM:er i en primär region som behandlar tillgänglighet på racknivån och två HSM:er i en sekundär region som behandlar regional tillgänglighet rekommenderas. Den mallfil som används i den här självstudien kan enkelt användas som utgångspunkt för en distribution med två HSM:er, men dess parametrar måste ändras så att de uppfyller dina krav.

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Övervakning](monitoring.md)
* [Support](supportability.md)
