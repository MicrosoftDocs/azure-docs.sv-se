---
title: Konfigurera data i skalning för Azure Automation tillstånds konfiguration
description: Den här artikeln beskriver hur du konfigurerar data i skala för Azure Automation tillstånds konfiguration.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc55a4c5ab20cac041a00a0f924b207eb256ae8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186528"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Konfigurera data i skalning för Azure Automation tillstånds konfiguration

> Gäller för: Windows PowerShell 5,1

Det kan vara en utmaning att hantera hundratals eller tusentals servrar.
Kunderna har fått feedback om att den svåraste aspekten faktiskt hanterar [konfigurations data](/powershell/scripting/dsc/configurations/configdata).
Organisera information över logiska konstruktioner som plats, typ och miljö.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-datum"></a>Community-projekt: datum

En community-baserad lösning med namnet [datum](https://github.com/gaelcolas/Datum) har skapats för att lösa den här utmaningen.
Datum bygger på fantastiska idéer från andra konfigurations hanterings plattformar och implementerar samma typ av lösning för PowerShell DSC.
Informationen är [ordnad i textfiler](https://github.com/gaelcolas/Datum#3-intended-usage) baserat på logiska idéer.
Exempel:

- Inställningar som ska gälla globalt
- Inställningar som ska gälla för alla servrar på en plats
- Inställningar som ska gälla för alla databas servrar
- Enskilda Server inställningar

Den här informationen är ordnad i det fil format som du föredrar (JSON, yaml eller PSD1).
Sedan tillhandahålls cmdlets för att generera konfigurationsfiler för data genom att [konsolidera informationen](https://github.com/gaelcolas/Datum#datum-tree) från varje fil i till en enskild vy av en server eller server roll.

När datafilerna har genererats kan du använda dem med [DSC-konfigurations skript](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) för att generera MOF-filer och [Ladda upp MOF-filerna till Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) eller [i Azure](./automation-dsc-onboarding.md#enable-azure-vms) för att hämta konfigurationer.

Om du vill prova datum går du till [PowerShell-galleriet](https://www.powershellgallery.com/packages/datum/) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Nästa steg

- För att förstå PowerShell DSC, se [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Lär dig mer om PowerShell DSC-resurser i [DSC-resurser](/powershell/scripting/dsc/resources/resources).
- Information om lokal Configuration Manager konfiguration finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig).
