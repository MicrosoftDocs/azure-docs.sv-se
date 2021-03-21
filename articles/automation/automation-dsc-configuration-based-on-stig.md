---
title: Konfigurera data baserat på STIG för konfiguration av Azure Automation tillstånd
description: Den här artikeln beskriver hur du konfigurerar data baserat på DoD-STIG för konfiguration av Azure Automation tillstånd.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015144"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Konfigurera data baserat på teknisk informations guide för säkerhet (STIG)

> Gäller för: Windows PowerShell 5,1

Det kan vara svårt att skapa konfigurations innehåll för första gången.
I många fall är målet att automatisera konfigurationen av servrar som följer en "bas linje" som förhoppnings vis anpassas till en bransch rekommendation.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-powerstig"></a>Community-projekt: PowerSTIG

Ett community-projekt med namnet [PowerSTIG](https://github.com/microsoft/powerstig) syftar till att lösa det här problemet genom att generera DSC-innehåll baserat på [offentlig information](https://public.cyber.mil/stigs/) som tillhandahålls om Stig (Security Technical Implementation Guide).

Att hantera bas linjer är mer komplicerat än det låter.
Många organisationer behöver [dokumentera undantag](https://github.com/microsoft/powerstig#powerstigdata) för regler och hantera dessa data i stor skala.
PowerSTIG löser problemet genom att tillhandahålla [sammansatta resurser](https://github.com/microsoft/powerstig#powerstigdsc) för att adressera varje område i konfigurationen i stället för att försöka hantera hela intervallet med inställningar i en stor fil.

När konfigurationerna har genererats kan du använda DSC- [konfigurations skript](/powershell/scripting/dsc/configurations/configurations) för att generera MOF-filer och [Ladda upp MOF-filerna till Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) eller [i Azure](./automation-dsc-onboarding.md#enable-azure-vms) för att hämta konfigurationer.

Om du vill testa PowerSTIG går du till [PowerShell-galleriet](https://www.powershellgallery.com) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Nästa steg

- För att förstå PowerShell DSC, se [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Lär dig mer om PowerShell DSC-resurser i [DSC-resurser](/powershell/scripting/dsc/resources/resources).
- Information om lokal Configuration Manager konfiguration finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig).
