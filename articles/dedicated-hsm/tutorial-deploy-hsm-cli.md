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
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: fa1c01c2d9da19ec1f60878de83a509b7cf561e8
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606835"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Självstudie: Distribuera HSM: er till ett befintligt virtuellt nätverk med hjälp av Azure CLI

Dedikerad HSM i Azure tillhandahåller en fysisk enhet enbart för kundanvändning, med fullständig administrativ kontroll och fullständigt hanteringsansvar. Användningen av fysiska enheter skapar ett behov av att Microsoft styr enhetsallokeringen för att säkerställa att kapacitet hanteras på ett effektivt sätt. I en Azure-prenumeration syns tjänsten Dedikerad HSM därför normalt inte för resursetablering. Azure-kunder som behöver åtkomst till tjänsten Dedikerad HSM måste först kontakta sin Microsoft-kontoansvarige och begära registrering för tjänsten Dedikerad HSM. Det är först när den här processen är klar som etablering blir möjlig. 

Den här självstudien visar en typisk etableringsprocess där:

- En kund redan har ett virtuellt nätverk
- Kunden har en virtuell dator
- Kunde måste lägga till HSM-resurser i den befintliga miljön.

En typisk arkitektur med hög tillgänglighet för distribution i flera regioner kan se ut så här:

![distribution i flera regioner](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Den här självstudien fokuserar på integreringen av ett par HSM:er och den nödvändiga ExpressRoute-gatewayen (se Undernät 1 ovan) i ett befintligt virtuellt nätverk (se VNET 1 ovan).  Alla andra resurser är Azure-standardresurser. Samma integreringsprocess kan användas för HSM:er i undernät 4 på VNET 3 ovan.

## <a name="prerequisites"></a>Förutsättningar

Dedikerad HSM i Azure är för närvarande inte tillgängligt på Azure-portalen. All interaktion med tjänsten sker via kommandoraden eller PowerShell. Den här självstudien använder kommandoradsgränssnittet (CLI) i Azure Cloud Shell. Om Azure CLI är nytt för dig följer du instruktionerna för att komma igång här: [Komma igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

Antaganden:

- Du har slutfört registreringsprocessen för Dedikerad HSM i Azure
- Du har godkänts för användning av tjänsten. Annars kontaktar du din Microsoft-kontorepresentant för att få information.
- Du har skapat en resursgrupp för dessa resurser, och de nya som distribueras i den här självstudien ansluts till den gruppen.
- Du redan har skapat nödvändiga virtuella nätverk, undernät och virtuella datorer enligt diagrammet ovan och vill nu integrera två HSM:er i den distributionen.

Alla instruktioner nedan förutsätter att du redan har navigerat till Azure Portal och att du har öppnat Cloud Shell (Välj " \> \_ " överst till höger i portalen).

## <a name="provisioning-a-dedicated-hsm"></a>Etablera en Dedikerad HSM

Etablering av HSM:er och integrering i ett befintligt virtuellt nätverk via ExpressRoute-Gateway verifieras med hjälp av SSH. Den här verifieringen hjälper till att säkerställa HSM-enhetens nätverksåtkomst och grundläggande tillgänglighet för ytterligare konfigurationsaktiviteter.

### <a name="validating-feature-registration"></a>Verifiera funktionsregistrering

Som nämnts ovan kräver alla etableringsaktiviteter att tjänsten Dedikerad HSM har registrerats för din prenumeration. Verifiera detta genom att köra följande kommandon i Azure Portal Cloud Shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Kommandona ska returnera statusen "registrerad" (som visas nedan). Om kommandona inte returnerar "registrerad" måste du registrera dig för den här tjänsten genom att kontakta din Microsoft-konto representant.

![prenumerationsstatus](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Skapa HSM-resurser

Innan du skapar HSM-resurser finns det vissa nödvändiga resurser som du behöver. Du måste ha ett virtuellt nätverk med undernätsintervall för beräkning, HSM:er och gateway. Följande kommandon utgör ett exempel på vad som skulle skapa ett sådant virtuellt nätverk.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
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

När du har konfigurerat nätverket kan du använda dessa Azure CLI-kommandon för att etablera din HSM: er.

1. Använd kommandot [AZ dedikerad-HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) för att etablera den första HSM. HSM heter hsm1. Ersätt din prenumeration:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Distributionen bör ta cirka 25 till 30 minuter att slutföra med den tid som HSM-enheterna är.

1. Om du vill se en aktuell HSM kör du kommandot [AZ dedikerat-HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) :

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Etablera den andra HSM: en med hjälp av det här kommandot:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Kör kommandot [AZ Dedicated-HSM List](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) för att visa information om din aktuella HSM: er:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Det finns andra kommandon som kan vara användbara. Använd kommandot [AZ Dedicated-HSM Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) för att uppdatera en HSM:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Om du vill ta bort en HSM använder du kommandot [AZ dedikerat-HSM Delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) :

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Kontrollera distributionen

Kontrollera att enheterna har etablerats och visa enhetsattribut genom att köra följande kommandouppsättning. Kontrollera att resursgruppen har angetts på rätt sätt och att resursnamnet är exakt samma som det du har i parameterfilen.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

Utdata ser ut ungefär så här:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

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

![Skärm bilden visar utdata i PowerShell-fönstret.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

I det här skedet har du allokerat alla resurser för en distribution med hög tillgänglighet och två HSM:er samt verifierat åtkomst och driftstatus. Ytterligare konfiguration eller testning medför mer arbete med själva HSM-enheten. För detta bör du följa anvisningarna i Thales Luna 7 HSM Administration Guide kapitel 7 för att initiera HSM och skapa partitioner. All dokumentation och program vara är tillgängliga direkt från Thales för hämtning när du har registrerat dig på [Thales kund support Portal](https://supportportal.thalesgroup.com/csm) och har ett kund-ID. Ladda ned klientprogramvara version 7.2 för att få alla nödvändiga komponenter.

## <a name="delete-or-clean-up-resources"></a>Ta bort eller rensa resurser

Om du är klar med bara HSM-enheten kan den tas bort som resurs och returneras till poolen. Det uppenbara problemet när du gör detta är eventuella känsliga kunddata som finns på enheten. Det bästa sättet att "zeroize" en enhet är att få lösen ordet för HSM-administratören fel tre gånger (Obs! det här är inte enhets administratören, det är den faktiska HSM-administratören). Som säkerhets åtgärd för att skydda nyckel material kan enheten inte tas bort som en Azure-resurs förrän den är i ett nollställt tillstånd.

> [!NOTE]
> Om du har problem med en Thales enhets konfiguration bör du kontakta [Thales kund support](https://supportportal.thalesgroup.com/csm).

Om du är färdig med alla resurser i den här resurs gruppen kan du ta bort dem med följande kommando:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Nästa steg

När du har slutfört stegen i självstudien etableras Dedikerad HSM-resurser och du har ett virtuellt nätverk med nödvändiga HSM:er samt ytterligare nätverkskomponenter som möjliggör kommunikation med HSM.  Du har nu möjligheten att komplettera den här distributionen med fler resurser efter behov för valfri distributionsarkitektur. Mer information som hjälper dig att planera distributionen finns i begreppsdokumenten.
En design med två HSM:er i en primär region som behandlar tillgänglighet på racknivån och två HSM:er i en sekundär region som behandlar regional tillgänglighet rekommenderas. 

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
