---
title: Konfigurera Windows Update-inställningar för Uppdateringshantering i Azure Automation
description: Den här artikeln beskriver hur du konfigurerar Windows Update inställningar så att de fungerar med Azure Automation Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222425"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Konfigurera Windows Update-inställningar för Uppdateringshantering i Azure Automation

Azure Automation Uppdateringshantering använder [Windows Update-klienten](/windows/deployment/update/windows-update-overview) för att ladda ned och installera Windows-uppdateringar. Det finns vissa inställningar som används av den Windows Update klienten vid anslutning till Windows Server Update Services (WSUS) eller Windows Update. Många av de här inställningarna kan hanteras med:

- Redigerare för lokal gruppolicy
- Grupprincip
- PowerShell
- Redigera registret direkt

Uppdateringshantering respekterar många av de inställningar som anges för att kontrol lera Windows Update-klienten. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows kan Uppdateringshantering även hantera dessa uppdateringar. Om du vill aktivera nedladdning av uppdateringar innan en uppdaterings distribution sker kan uppdaterings distributionen bli snabbare, mer effektiv och mindre troligt att överskrida underhålls perioden.

Ytterligare rekommendationer för att konfigurera WSUS i din Azure-prenumeration och se till att dina virtuella Windows-datorer hålls uppdaterade genom att [Planera distributionen för uppdatering av virtuella Windows-datorer i Azure med hjälp av WSUS](/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>För nedladdning av uppdateringar

Om du vill konfigurera automatisk nedladdning av uppdateringar utan att installera dem automatiskt kan du använda grupprincip för att [Konfigurera inställningen för automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) till 3. Den här inställningen aktiverar hämtning av nödvändiga uppdateringar i bakgrunden och meddelar dig att uppdateringarna är klara att installeras. På så sätt har Uppdateringshantering fortfarande kontroll över scheman, men tillåter att uppdateringar laddas ned utanför Uppdateringshantering underhålls fönstret. Det här beteendet förhindrar `Maintenance window exceeded` fel i uppdateringshantering.

Du kan aktivera den här inställningen i PowerShell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurera inställningar för omstart

De register nycklar som visas i [Konfigurera automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan göra att datorerna startas om, även om du **inte anger starta** om i **uppdaterings distributions** inställningarna. Konfigurera de här register nycklarna så att de passar bäst för din miljö.

## <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard är Windows Update-klienten konfigurerad att endast tillhandahålla uppdateringar för Windows. Om du aktiverar alternativet **för att ge mig uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows** -inställningar får du också uppdateringar för andra produkter, inklusive säkerhets korrigeringar för Microsoft SQL Server och annan Microsoft-programvara. Du kan konfigurera det här alternativet om du har laddat ned och kopierat de senaste [administrativa mallarna](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) som är tillgängliga för Windows 2016 och senare.

Om du har datorer som kör Windows Server 2012 R2 kan du inte konfigurera den här inställningen via grupprincip. Kör följande PowerShell-kommando på följande datorer:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Gör inställningar för WSUS-konfiguration

Uppdateringshantering stöder WSUS-inställningar. Du kan ange källor för genomsökning och hämtning av uppdateringar med hjälp av anvisningarna i [Ange intranät Microsoft Update tjänst plats](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Som standard är Windows Update-klienten konfigurerad för att hämta uppdateringar från Windows Update. När du anger en WSUS-server som källa för dina datorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen. 

Om du vill begränsa datorerna till den interna uppdaterings tjänsten anger [du Anslut inte till någon Windows Update Internet-platser](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations).

## <a name="next-steps"></a>Nästa steg

Schemalägg en uppdaterings distribution genom att följa anvisningarna i [Hantera uppdateringar och korrigeringar för dina virtuella datorer](manage-updates-for-vm.md).
