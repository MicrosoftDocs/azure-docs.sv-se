---
title: Självstudie om att distribuera till ett befintligt virtuellt nätverk med hjälp av Azure CLI – Dedikerad HSM i Azure | Microsoft Docs
description: Självstudie som visar hur du distribuerar en dedikerad HSM med hjälp av CLI i ett befintligt virtuellt nätverk
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e4403252396aab91fa960ed7258d433e27db950a
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258240"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Självstudie: Distribuera HSM: er till ett befintligt virtuellt nätverk med CLI

Dedikerad HSM i Azure tillhandahåller en fysisk enhet enbart för kundanvändning, med fullständig administrativ kontroll och fullständigt hanteringsansvar. Användningen av fysiska enheter skapar ett behov av att Microsoft styr enhetsallokeringen för att säkerställa att kapacitet hanteras på ett effektivt sätt. I en Azure-prenumeration syns tjänsten Dedikerad HSM därför normalt inte för resursetablering. Azure-kunder som behöver åtkomst till tjänsten Dedikerad HSM måste först kontakta sin Microsoft-kontoansvarige och begära registrering för tjänsten Dedikerad HSM. Det är först när den här processen är klar som etablering blir möjlig. 

Den här självstudien visar en typisk etableringsprocess där:

- En kund redan har ett virtuellt nätverk
- Kunden har en virtuell dator
- Kunde måste lägga till HSM-resurser i den befintliga miljön.

En typisk arkitektur med hög tillgänglighet för distribution i flera regioner kan se ut så här:

![distribution i flera regioner](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Den här självstudien fokuserar på integreringen av ett par HSM:er och den nödvändiga ExpressRoute-gatewayen (se Undernät 1 ovan) i ett befintligt virtuellt nätverk (se VNET 1 ovan).  Alla andra resurser är Azure-standardresurser. Samma integreringsprocess kan användas för HSM:er i undernät 4 på VNET 3 ovan.

## <a name="prerequisites"></a>Krav

Dedikerad HSM i Azure är för närvarande inte tillgängligt på Azure-portalen. All interaktion med tjänsten sker via kommandoraden eller PowerShell. Den här självstudien använder kommandoradsgränssnittet (CLI) i Azure Cloud Shell. Om Azure CLI är nytt för dig följer du instruktionerna för att komma igång här: [Komma igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Antaganden:

- Du har slutfört registreringsprocessen för Dedikerad HSM i Azure
- Du har godkänts för användning av tjänsten. Annars kontaktar du din Microsoft-kontorepresentant för att få information.
- Du har skapat en resursgrupp för dessa resurser, och de nya som distribueras i den här självstudien ansluts till den gruppen.
- Du redan har skapat nödvändiga virtuella nätverk, undernät och virtuella datorer enligt diagrammet ovan och vill nu integrera två HSM:er i den distributionen.

Alla instruktioner nedan förutsätter att du redan har navigerat till Azure Portal och att du har öppnat Cloud Shell (Välj " \> \_ " överst till höger i portalen).

## <a name="provisioning-a-dedicated-hsm"></a>Etablera en Dedikerad HSM

Etablering av HSM:er och integrering i ett befintligt virtuellt nätverk via ExpressRoute-Gateway verifieras med hjälp av SSH. Den här verifieringen hjälper till att säkerställa HSM-enhetens nätverksåtkomst och grundläggande tillgänglighet för ytterligare konfigurationsaktiviteter. Följande kommandon använder en Azure Resource Manager-mall för att skapa HSM-resurserna och tillhörande nätverksresurser.

### <a name="validating-feature-registration"></a>Verifiera funktionsregistrering

Som nämnts ovan kräver alla etableringsaktiviteter att tjänsten Dedikerad HSM har registrerats för din prenumeration. För att verifiera det kör du följande kommandon i Azure-portalens Cloud Shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Kommandona ska returnera statusen "registrerad" (som visas nedan). Om kommandona inte returnerar "registrerad" måste du registrera dig för den här tjänsten genom att kontakta din Microsoft-konto representant.

![prenumerationsstatus](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Skapa HSM-resurser

En HSM är etablerad i ett kunds virtuella nätverk så att det krävs ett virtuellt nätverk och ett undernät. Ett beroende som krävs för att HSM ska möjliggöra kommunikation mellan det virtuella nätverket och en fysisk enhet är en ExpressRoute-Gateway. Slutligen krävs en virtuell dator för att få åtkomst till HSM-enheten med hjälp av Gemalto-klientprogrammet. Dessa resurser har samlats i en mallfil med motsvarande parameterfil för enkel användning. Du kan hämta filerna genom att kontakta Microsoft direkt på HSMrequest@Microsoft.com.

När du har filerna behöver du redigera parameterfilen om du vill infoga dina önskade namn för resurser. Redigera rader med "värde": "".

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

Den associerade Azure Resource Manager-mallfilen skapar 6 resurser med den här informationen:

- Ett undernät för HSM:erna i det angivna virtuella nätverket
- Ett undernät för den virtuella nätverksgatewayen
- En virtuell nätverksgateway som ansluter det virtuella nätverket till HSM-enheterna
- En offentlig IP-adress för gatewayen
- En HSM i stämpel 1
- En HSM i stämpel 2

När parametervärden har angetts måste filerna laddas upp till Azure-portalens Cloud Shell-filresurs för användning. Klicka på " \> \_ " Cloud Shell-symbolen överst till höger i Azure Portal så att den nedre delen av skärmen visas i en kommando miljö. Alternativen för detta är BASH och PowerShell, och du bör välja BASH om det inte redan har angetts.

Kommandogränssnittet innehåller ett alternativ för uppladdning/nedladdning i verktygsfältet, och du bör ange det här till att ladda upp milen och parameterfilerna till din filresurs:

![filresurs](media/tutorial-deploy-hsm-cli/file-share.png)

När filerna har laddats upp är du redo att skapa resurser. Innan du skapar nya HSM-resurser finns det vissa nödvändiga resurser som du bör kontrollera så att de finns plats. Du måste ha ett virtuellt nätverk med undernätsintervall för beräkning, HSM:er och gateway. Följande kommandon utgör ett exempel på vad som skulle skapa ett sådant virtuellt nätverk.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>Den viktigaste konfigurationen för att anteckna för det virtuella nätverket, är att under nätet för HSM-enheten måste ha delegeringarna inställt på "Microsoft. HardwareSecurityModules/dedicatedHSMs".  HSM-etableringen fungerar inte utan att det här alternativet anges.

När alla förutsättningar är uppfyllda kör du följande kommando för att använda Azure Resource Manager-mallen. Se till att du har uppdaterat värdena med dina unika namn (åtminstone resursgruppens namn):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Den här distributionen bör ta ungefär 25 till 30 minuter att slutföra, varav större delen av den tiden går till HSM-enheterna

![etableringsstatus](media/tutorial-deploy-hsm-cli/progress-status.png)

När distributionen har slutförts visas "provisioningState": "lyckades". Du kan ansluta till den befintliga virtuella datorn och använd SSH för att säkerställa HSM-enhetens tillgänglighet.

## <a name="verifying-the-deployment"></a>Kontrollera distributionen

Kontrollera att enheterna har etablerats och visa enhetsattribut genom att köra följande kommandouppsättning. Kontrollera att resursgruppen har angetts på rätt sätt och att resursnamnet är exakt samma som det du har i parameterfilen.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![etableringsutdata](media/tutorial-deploy-hsm-cli/progress-status2.png)

Du kan nu även se resurser med hjälp av [Azure-resursutforskaren](https://resources.azure.com/).   När du är i Utforskaren expanderar du "prenumerationer" till vänster, expanderar din särskilda prenumeration för dedikerad HSM, expanderar "resurs grupper", expanderar den resurs grupp som du använde och slutligen väljer du alternativet "resurser".

## <a name="testing-the-deployment"></a>Testa distributionen

Att testa distributionen innebär att ansluta till en virtuell dator som kan komma åt HSM:er och sedan ansluta direkt till HSM-enheten. De här åtgärderna bekräftar att HSM kan nås.
SSH-verktyget används för att ansluta till den virtuella datorn. Kommandot liknar följande men med det administratörsnamn och DNS-namn som du angav i parametern.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

IP-adressen för den virtuella datorn kan också användas i stället för DNS-namnet i kommandot ovan. Om kommandot lyckas anger du det lösenord som efterfrågas. När du har loggat in på den virtuella datorn kan du logga in på HSM med hjälp av den privata IP-adress som finns i portalen för den nätverksgränssnittsresurs som associeras med HSM.

![lista över komponenter](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Lägg märke till kryss rutan Visa dolda typer, som när det här alternativet är valt visas HSM-resurser.

I skärm bilden ovan visas en lämplig privat IP-adress om du klickar på "HSM1_HSMnic" eller "HSM2_HSMnic". Annars är kommandot `az resource show` som används ovan ett sätt att identifiera rätt IP-adress. 

När du har rätt IP-adress kör du följande kommando och ersätter den adressen:

`ssh tenantadmin@10.0.2.4`

Om detta lyckas uppmanas du att ange ett lösenord. Standardlösenordet är PASSWORD, och HSM ber dig först att ändra ditt lösenord. Ange därför ett starkt lösenord och använd den mekanism som din organisation föredrar för att lagra lösenordet och förhindra förlust.

>[!IMPORTANT]
>om du tappar bort det här lösenordet måste HSM återställas, vilket innebär att dina nycklar går förlorade.

När du är ansluten till HSM med hjälp av SSH kör du följande kommando för att se till att HSM är i drift.

`hsm show`

Utdata bör se ut som på bilden nedan:

![lista över komponenter](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

I det här skedet har du allokerat alla resurser för en distribution med hög tillgänglighet och två HSM:er samt verifierat åtkomst och driftstatus. Ytterligare konfiguration eller testning medför mer arbete med själva HSM-enheten. För detta bör du följa anvisningarna i kapitel 7 i administrationsguiden för Gemalto Luna Network HSM 7 för att initiera HSM och skapa partitioner. All dokumentation och programvara är tillgänglig direkt från Gemalto för nedladdning när du har registrerats i Gemalto Customer Support Portal och har ett kund-ID. Ladda ned klientprogramvara version 7.2 för att få alla nödvändiga komponenter.

## <a name="delete-or-clean-up-resources"></a>Ta bort eller rensa resurser

Om du är klar med bara HSM-enheten kan den tas bort som resurs och returneras till poolen. Det uppenbara problemet när du gör detta är eventuella känsliga kunddata som finns på enheten. Det bästa sättet att "zeroize" en enhet är att få lösen ordet för HSM-administratören fel tre gånger (Obs! det här är inte enhets administratören, det är den faktiska HSM-administratören). Som säkerhets åtgärd för att skydda nyckel material kan enheten inte tas bort som en Azure-resurs förrän den är i ett nollställt tillstånd.

> [!NOTE]
> om det är problem med någon Gemalto-enhetskonfiguration bör du kontakta [Gemaltos kundsupport](https://safenet.gemalto.com/technical-support/).


Om du är färdig med alla resurser i den här resurs gruppen kan du ta bort dem med följande kommando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Nästa steg

När du har slutfört stegen i självstudien etableras Dedikerad HSM-resurser och du har ett virtuellt nätverk med nödvändiga HSM:er samt ytterligare nätverkskomponenter som möjliggör kommunikation med HSM.  Du har nu möjligheten att komplettera den här distributionen med fler resurser efter behov för valfri distributionsarkitektur. Mer information som hjälper dig att planera distributionen finns i begreppsdokumenten.
En design med två HSM:er i en primär region som behandlar tillgänglighet på racknivån och två HSM:er i en sekundär region som behandlar regional tillgänglighet rekommenderas. Den mallfil som används i den här självstudien kan enkelt användas som utgångspunkt för en distribution med två HSM:er, men dess parametrar måste ändras så att de uppfyller dina krav.

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
