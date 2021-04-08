---
title: Uppgradera från Basic Internal till standard Internal-Azure Load Balancer
description: 'Den här artikeln visar hur du uppgraderar interna Azure-Load Balancer från Basic SKU till standard-SKU: n'
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050705"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uppgradera Azure Internal Load Balancer – ingen utgående anslutning krävs
[Azure standard Load Balancer](load-balancer-overview.md) erbjuder en omfattande uppsättning funktioner och hög tillgänglighet genom zon redundans. Mer information om Load Balancer SKU finns i [jämförelse tabell](./skus.md#skus).

Den här artikeln introducerar ett PowerShell-skript som skapar en Standard Load Balancer med samma konfiguration som den grundläggande Load Balancer tillsammans med migrering av trafik från Basic Load Balancer till Standard Load Balancer.

## <a name="upgrade-overview"></a>Översikt över uppgradering

Det finns ett Azure PowerShell-skript tillgängligt som gör följande:

* Skapar ett internt standard-SKU-Load Balancer på den plats som du anger. Observera att ingen [utgående anslutning](./load-balancer-outbound-connections.md) kommer att tillhandahållas av interna standard Load Balancer.
* Kopierar sömlöst konfigurationerna av Basic SKU-Load Balancer till den nyligen skapade Standard Load Balancer.
* Flytta enkelt de privata IP-adresserna från Basic-Load Balancer till den nyligen skapade Standard Load Balancer.
* Flytta sömlöst de virtuella datorerna från backend-poolen av Basic-Load Balancer till backend-poolen för Standard Load Balancer

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skript stöder endast intern Load Balancer uppgradering där ingen utgående anslutning krävs. Om du behöver [utgående anslutning](./load-balancer-outbound-connections.md) för några av dina virtuella datorer kan du gå till den här [sidan](upgrade-InternalBasic-To-PublicStandard.md) för instruktioner. 
* Basic-Load Balancer måste finnas i samma resurs grupp som de virtuella datorerna och nätverkskorten.
* Om standard belastnings utjämning skapas i en annan region kan du inte associera de virtuella datorerna i den gamla regionen med den nya Standard Load Balancer. Du kan undvika den här begränsningen genom att skapa en ny virtuell dator i den nya regionen.
* Om din Load Balancer inte har någon IP-konfiguration för klient delen eller en backend-pool, kommer du förmodligen att träffa ett fel som kör skriptet. Se till att de inte är tomma.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Ändra metod för IP-allokering till statisk för klient delens IP-konfiguration (ignorera det här steget om det redan är statiskt)

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan din grundläggande Load Balancer i listan resurser.

2. Under **Inställningar** väljer du **IP-konfiguration för klient** del och väljer den första IP-konfigurationen för klient delen. 

3. För **tilldelning** väljer du **statisk**

4. Upprepa steg 3 för alla IP-konfigurationer för klient delen av Basic-Load Balancer.


## <a name="download-the-script"></a>Hämta skriptet

Hämta migrerings skriptet från  [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0).
## <a name="use-the-script"></a>Använd skriptet

Det finns två alternativ för dig, beroende på din lokala PowerShell-Miljös konfiguration och inställningar:

* Om du inte har installerat Azure AZ-moduler, eller om du inte vill avinstallera Azure AZ-modulerna, är det bästa alternativet att använda `Install-Script` alternativet för att köra skriptet.
* Om du behöver behålla Azure AZ-modulerna är det bästa valet att ladda ned skriptet och köra det direkt.

Du kan ta reda på om du har installerat Azure AZ-moduler genom att köra `Get-InstalledModule -Name az` . Om du inte ser några installerade AZ-moduler kan du använda- `Install-Script` metoden.

### <a name="install-using-the-install-script-method"></a>Installera med hjälp av metoden Install-Script

Om du vill använda det här alternativet behöver du inte ha de Azure AZ-moduler som är installerade på datorn. Om de är installerade visar följande kommando ett fel. Du kan antingen avinstallera Azure AZ-moduler eller använda det andra alternativet för att ladda ned skriptet manuellt och köra det.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzureILBUpgrade`

Det här kommandot installerar även de AZ-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med hjälp av skriptet direkt

Om du har några Azure AZ-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **manuell hämtning** i länken för hämtning av skript. Skriptet laddas ned som en RAW nupkg-fil. Om du vill installera skriptet från den här nupkg-filen, se [manuell paket hämtning](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Använd `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera AZ-modulerna.

1. Granska de nödvändiga parametrarna:

   * **rgName: [sträng]: krävs** – det här är resurs gruppen för din befintliga grundläggande Load Balancer och nya standard Load Balancer. Om du vill hitta strängvärdet navigerar du till Azure Portal, väljer din grundläggande Load Balancer källa och klickar på **översikten** för belastningsutjämnaren. Resurs gruppen finns på sidan.
   * **oldLBName: [sträng]: krävs** – det här är namnet på den befintliga Basic-saldo som du vill uppgradera. 
   * **newlocation: [sträng]: krävs** – det här är den plats där standard Load Balancer skapas. Vi rekommenderar att du ärver samma plats för de valda Basic-Load Balancer till Standard Load Balancer för bättre Association med andra befintliga resurser.
   * **newLBName: [sträng]: obligatorisk** – det här är namnet på den standard Load Balancer som ska skapas.
1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att slutföra.

    **Exempel**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Växlar Azure PowerShell-skriptet också över trafiken från min grundläggande Load Balancer till den nyligen skapade Standard Load Balancer?

Ja, det migrerar trafik. Om du vill migrera trafiken personligen använder du [det här skriptet](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) som inte flyttar virtuella datorer åt dig.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Standard Load Balancer](load-balancer-overview.md)
