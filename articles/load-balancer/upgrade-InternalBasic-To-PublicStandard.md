---
title: Uppgradera från Basic Internal till standard Public-Azure Load Balancer
description: Den här artikeln visar hur du uppgraderar interna Azure Basic-Load Balancer till offentliga standard Load Balancer
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3394754f2829018f7862b3775f8ab2cb2d07d005
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051368"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Uppgradera intern Azure-Load Balancer-utgående anslutning krävs
[Azure standard Load Balancer](load-balancer-overview.md) erbjuder en omfattande uppsättning funktioner och hög tillgänglighet genom zon redundans. Mer information om Load Balancer SKU finns i [jämförelse tabell](./skus.md#skus). Eftersom interna standard Load Balancer inte tillhandahåller utgående anslutning tillhandahåller vi en lösning för att skapa en offentlig standard Load Balancer i stället.

Det finns fyra steg i en uppgradering:

1. Migrera konfigurationen till offentliga standard Load Balancer
2. Lägg till virtuella datorer till backend-pooler för offentliga standard Load Balancer
3. Konfigurera NSG-regler för undernät/VM-nätverk som ska AVSÄGAS från/till Internet

Den här artikeln beskriver migrering av konfiguration. Att lägga till virtuella datorer i backend-pooler kan variera beroende på din speciella miljö. Några [övergripande rekommendationer finns](#add-vms-to-backend-pools-of-standard-load-balancer)dock.

## <a name="upgrade-overview"></a>Översikt över uppgradering

Det finns ett Azure PowerShell-skript tillgängligt som gör följande:

* Skapar en offentlig standard-Load Balancer för SKU i resurs gruppen och platsen som du anger.
* Kopierar sömlöst de konfigurationer som skapats av Basic-SKU: n internt Load Balancer till de nya offentliga standard Load Balancer.
* Skapar en utgående regel som möjliggör utgående anslutning.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skriptet stöder intern Load Balancer uppgradering där utgående anslutning krävs. Om utgående anslutning inte krävs för någon av de virtuella datorerna kan du se [den här sidan](upgrade-basicInternal-standard.md) för bästa praxis.
* Standard Load Balancer har en ny offentlig adress. Det går inte att flytta IP-adresserna som är kopplade till den befintliga grundläggande interna Load Balancer sömlöst till offentliga standard Load Balancer eftersom de har olika SKU: er.
* Om standard belastnings utjämning skapas i en annan region kan du inte associera de virtuella datorerna i den gamla regionen med den nya Standard Load Balancer. Du kan undvika den här begränsningen genom att skapa en ny virtuell dator i den nya regionen.
* Om din Load Balancer inte har någon IP-konfiguration för klient delen eller en backend-pool, kommer du förmodligen att träffa ett fel som kör skriptet.  Se till att de inte är tomma.

## <a name="download-the-script"></a>Hämta skriptet

Hämta migrerings skriptet från  [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0).
## <a name="use-the-script"></a>Använd skriptet

Det finns två alternativ för dig, beroende på din lokala PowerShell-Miljös konfiguration och inställningar:

* Om du inte har installerat Azure AZ-moduler, eller om du inte vill avinstallera Azure AZ-modulerna, är det bästa alternativet att använda `Install-Script` alternativet för att köra skriptet.
* Om du behöver behålla Azure AZ-modulerna är det bästa valet att ladda ned skriptet och köra det direkt.

Du kan ta reda på om du har installerat Azure AZ-moduler genom att köra `Get-InstalledModule -Name az` . Om du inte ser några installerade AZ-moduler kan du använda- `Install-Script` metoden.

### <a name="install-using-the-install-script-method"></a>Installera med hjälp av metoden Install-Script

Om du vill använda det här alternativet behöver du inte ha de Azure AZ-moduler som är installerade på datorn. Om de är installerade visar följande kommando ett fel. Du kan antingen avinstallera Azure AZ-moduler eller använda det andra alternativet för att ladda ned skriptet manuellt och köra det.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzurePublicLBUpgrade`

Det här kommandot installerar även de AZ-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med hjälp av skriptet direkt

Om du har några Azure AZ-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **manuell hämtning** i länken för hämtning av skript. Skriptet laddas ned som en RAW nupkg-fil. Om du vill installera skriptet från den här nupkg-filen, se [manuell paket hämtning](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Använd `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera AZ-modulerna.

1. Granska de nödvändiga parametrarna:

   * **oldRgName: [sträng]: krävs** – det här är resurs gruppen för din befintliga Basic-Load Balancer som du vill uppgradera. Om du vill hitta strängvärdet navigerar du till Azure Portal, väljer din grundläggande Load Balancer källa och klickar på **översikten** för belastningsutjämnaren. Resurs gruppen finns på sidan.
   * **oldLBName: [sträng]: krävs** – det här är namnet på den befintliga Basic-saldo som du vill uppgradera. 
   * **newrgName: [sträng]: krävs** – det här är resurs gruppen där standard Load Balancer skapas. Det kan vara en ny resurs grupp eller en befintlig. Om du väljer en befintlig resurs grupp, Observera att namnet på Load Balancer måste vara unikt inom resurs gruppen. 
   * **newlocation: [sträng]: krävs** – det här är den plats där standard Load Balancer skapas. Vi rekommenderar att du ärver samma plats för de valda Basic-Load Balancer till Standard Load Balancer för bättre Association med andra befintliga resurser.
   * **newLBName: [sträng]: obligatorisk** – det här är namnet på den standard Load Balancer som ska skapas.
1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att slutföra.

    **Exempel**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Lägg till virtuella datorer i backend-pooler för Standard Load Balancer

Börja med att kontrol lera att skriptet har skapat en ny standard offentlig Load Balancer med den exakta konfigurationen som migrerats från din grundläggande offentliga Load Balancer. Du kan kontrol lera detta från Azure Portal.

Se till att skicka en liten mängd trafik genom Standard Load Balancer som ett manuellt test.
  
Här följer några exempel på hur du lägger till virtuella datorer till backend-pooler för den nyligen skapade offentliga standard Load Balancer kan konfigureras och våra rekommendationer för var och en:

* **Flytta befintliga virtuella datorer från backend-pooler för gamla grundläggande offentliga Load Balancer till backend-pooler för nyligen skapade standard offentliga Load Balancer**.
    1. Logga in på [Azure-portalen](https://portal.azure.com) för att genomföra alla uppgifter i den här snabbstarten.
 
    1. Välj **alla resurser** på den vänstra menyn och välj sedan det **nyligen skapade standard Load Balancer** från resurs listan.
   
    1. Under **Inställningar** väljer du **backend-pooler**.
   
    1. Välj den backend-pool som matchar backend-poolen för Basic-Load Balancer, Välj följande värde: 
      - **Virtuell dator**: list rutan och välj de virtuella datorerna från den matchande backend-poolen för Basic-Load Balancer.
    1. Välj **Spara**.
    >[!NOTE]
    >För virtuella datorer som har offentliga IP-adresser måste du skapa standard-IP-adresser först där samma IP-adress inte garanteras. Ta bort associationen från de virtuella IP-adresserna och koppla dem till de nyligen skapade standard-IP-adresserna. Sedan kommer du att kunna följa instruktionerna för att lägga till virtuella datorer i backend-poolen med Standard Load Balancer. 

* **Skapa nya virtuella datorer som ska läggas till i backend-pooler för den nyligen skapade offentliga Standard Load Balancer**.
    * Du hittar mer information om hur du skapar en virtuell dator och associerar den med Standard Load Balancer [här](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Skapa en utgående regel för utgående anslutning

Följ [instruktionerna](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) för att skapa en utgående regel så att du kan
* Definiera utgående NAT från grunden.
* Skala och finjustera beteendet för befintlig utgående NAT.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Skapa NSG-regler för virtuella datorer som kan undvika kommunikation från eller till Internet
Om du vill undvika att Internet trafiken når dina virtuella datorer kan du skapa en [NSG-regel](../virtual-network/manage-network-security-group.md) för de virtuella datorernas nätverks gränssnitt.

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Växlar Azure PowerShell-skriptet också över trafiken från min grundläggande Load Balancer till den nyligen skapade Standard Load Balancer?

Nej. Azure PowerShell-skriptet migrerar bara konfigurationen. Den faktiska trafikmigreringen är ditt ansvar och i din kontroll.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Standard Load Balancer](load-balancer-overview.md)
