---
title: Uppgradera från grundläggande offentlig till standard-Azure Load Balancer
description: 'Den här artikeln visar hur du uppgraderar offentliga Azure-Load Balancer från Basic SKU till standard-SKU: n'
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045401"
---
# <a name="upgrade-azure-public-load-balancer"></a>Uppgradera offentliga Azure-Load Balancer
[Azure standard Load Balancer](load-balancer-overview.md) erbjuder en omfattande uppsättning funktioner och hög tillgänglighet genom zon redundans. Mer information om Load Balancer SKU finns i [jämförelse tabell](./skus.md#skus).

Det finns två steg i en uppgradering:

1. Ändra metod för IP-allokering från dynamisk till statisk.
2. Kör PowerShell-skriptet för att slutföra migreringen av uppgraderingen och trafiken.

## <a name="upgrade-overview"></a>Översikt över uppgradering

Det finns ett Azure PowerShell-skript tillgängligt som gör följande:

* Skapar en standard-SKU-Load Balancer med en plats som du anger i samma resurs grupp i Basic-Standard Load Balancer.
* Uppgraderar den offentliga IP-adressen från Basic SKU till standard-SKU på plats.
* Kopierar sömlöst konfigurationerna av Basic SKU-Load Balancer till det nya Standard Load Balancer.
* Skapar en standard regel för utgående trafik som möjliggör utgående anslutning.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skript stöder endast offentlig Load Balancer uppgradering. Information om interna grundläggande Load Balancer-uppgraderingar finns på [den här sidan](./upgrade-basicinternal-standard.md) .
* Du måste ändra tilldelnings metoden för den offentliga IP-adressen till "static" innan du kör skriptet. 
* Om din Load Balancer inte har någon IP-konfiguration för klient delen eller en backend-pool, kommer du förmodligen att träffa ett fel som kör skriptet. Kontrol lera att de inte är tomma.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Ändra tilldelnings metod för den offentliga IP-adressen till statisk

* * * Här är våra rekommenderade steg:

    1. Logga in på [Azure-portalen](https://portal.azure.com) för att genomföra alla uppgifter i den här snabbstarten.
 
    1. Välj **alla resurser** på den vänstra menyn och välj sedan den **grundläggande offentliga IP-adress som är associerad med Basic-Load Balancer** från resurs listan.
   
    1. Under **Inställningar** väljer du **konfigurationer**.
   
    1. Under **tilldelning** väljer du **statisk**.
    1. Välj **Spara**.
    >[!NOTE]
    >För virtuella datorer som har offentliga IP-adresser måste du skapa standard-IP-adresser först där samma IP-adress inte garanteras. Ta bort associationen från de virtuella IP-adresserna och koppla dem till de nyligen skapade standard-IP-adresserna. Sedan kommer du att kunna följa instruktionerna för att lägga till virtuella datorer i backend-poolen med Standard Load Balancer. 

* **Skapa nya virtuella datorer som ska läggas till i backend-pooler för den nyligen skapade offentliga Standard Load Balancer**.
    * Du hittar mer information om hur du skapar en virtuell dator och associerar den med Standard Load Balancer [här](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Hämta skriptet

Hämta migrerings skriptet från  [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
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
   * **newLBName: [sträng]: obligatorisk** – det här är namnet på den standard Load Balancer som ska skapas.
1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att slutföra.

    **Exempel**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Skapa en utgående regel för utgående anslutning

Följ [instruktionerna](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) för att skapa en utgående regel så att du kan
* Definiera utgående NAT från grunden.
* Skala och finjustera beteendet för befintlig utgående NAT.

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Hur lång tid tar uppgraderingen?

Det tar vanligt vis ungefär 5-10 minuter innan skriptet har slutförts och det kan ta längre tid beroende på hur komplex din Load Balancer-konfiguration är. Därför bör du hålla nedtid i åtanke och planera för redundans vid behov.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Växlar Azure PowerShell-skriptet också över trafiken från min grundläggande Load Balancer till den nyligen skapade Standard Load Balancer?

Ja. Azure PowerShell-skriptet uppgraderar inte bara den offentliga IP-adressen, kopierar konfigurationen från Basic till Standard Load Balancer, men migrerar även den virtuella datorn till bakom den nya offentliga standard Load Balancer. 

## <a name="next-steps"></a>Nästa steg

[Läs mer om Standard Load Balancer](load-balancer-overview.md)
