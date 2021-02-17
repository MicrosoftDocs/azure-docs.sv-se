---
title: Aktivera resurs loggning i Azure Traffic Manager
description: Lär dig hur du aktiverar resurs loggning för din Traffic Manager profil och hur du kommer åt loggfilerna som skapas som ett resultat.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 0c1273f4a2025658520118ed843d90b3378a129f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577880"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Aktivera resurs loggning i Azure Traffic Manager

I den här artikeln beskrivs hur du aktiverar insamling av diagnostiska resurs loggar och åtkomst till loggdata för en Traffic Manager-profil.

Azure Traffic Manager resurs loggar kan ge inblick i hur Traffic Manager profil resursen fungerar. Du kan till exempel använda profilens loggdata för att avgöra varför enskilda avsökningar har nått tids gränsen för en slut punkt.

## <a name="enable-resource-logging"></a>Aktivera resursloggning

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, 1.0.0 eller senare. Du kan köra `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att logga in på Azure.

1. **Hämta Traffic Manager profil:**

    Om du vill aktivera resurs loggning behöver du ID: t för en Traffic Manager-profil. Hämta den Traffic Manager profil som du vill aktivera resurs loggning för med [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Utdata innehåller ID-information för Traffic Manager-profilen.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Aktivera resurs loggning för Traffic Managers profilen:**

    Aktivera resurs loggning för Traffic Manager profilen med det ID som hämtades i föregående steg med [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Följande kommando lagrar utförliga loggar för Traffic Manager profilen till ett angivet Azure Storage konto. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verifiera diagnostikinställningar:**

      Verifiera diagnostikinställningar för Traffic Manager profilen med [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Följande kommando visar de kategorier som är loggade för en resurs.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Se till att alla logg kategorier som är associerade med Traffic Manager profil resursen visas som aktive rad. Kontrol lera också att lagrings kontot har angetts korrekt.

## <a name="access-log-files"></a>Åtkomst till loggfiler
1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till ditt Azure Storage-konto i portalen.
2. På sidan **Översikt** på ditt Azure Storage-konto, under **tjänster** väljer du **blobbar**.
3. För **behållare** väljer du **Insights-logs-probehealthstatusevents** och går till PT1H.jspå filen och klickar på **Ladda ned** för att ladda ned och spara en kopia av logg filen.

    ![Komma åt loggfiler för din Traffic Manager profil från en blob-lagring](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager logg schema

Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser. Schema för resurs loggar på högsta nivån finns i [tjänster, scheman och kategorier som stöds för Azure-resurs loggar](../azure-monitor/essentials/resource-logs-schema.md).

Följande tabell innehåller loggar schema som är speciellt för Azure Traffic Manager Profile-resursen.

|Fältnamn|Fälttyp|Definition|Exempel|
|----|----|---|---|
|EndpointName|Sträng|Namnet på Traffic Manager slut punkten vars hälso status registreras.|*myPrimaryEndpoint*|
|Status|Sträng|Hälso tillståndet för den Traffic Manager slut punkten som avsöktes. Statusen kan antingen vara **upp** eller **ned**.|**Upp**|
|||||

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Traffic Manager övervakning](traffic-manager-monitoring.md)