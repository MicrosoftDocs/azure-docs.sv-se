---
title: Uppgradera Azure Backup Agent
description: Den här informationen förklarar varför du bör uppgradera Azure Backup agenten och var du kan ladda ned uppgraderingen.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294140"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av Microsoft Azure Recovery Services (MARS) agenten nedan 2.0.9083.0 har ett beroende av Azure Access Control-tjänsten. MARS-agenten kallas även för den Azure Backup agenten.

I 2018 har Microsoft [föråldrat Azure Access Control-tjänsten](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Från den 19 mars 2018 kommer alla versioner av MARS-agenten nedan att drabbas av säkerhets kopierings problem. [Uppgradera mars-agenten till den senaste versionen](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)för att undvika eller lösa säkerhets kopierings problem. Om du vill identifiera servrar som kräver en MARS-agent uppgradering följer du stegen i [uppgradera Microsoft Azure Recovery Services (mars) agenten](../articles/backup/upgrade-mars-agent.md).

MARS-agenten används för att säkerhetskopiera filer och mappar och system tillstånds data till Azure. System Center DPM och Azure Backup Server använder MARS-agenten för att säkerhetskopiera data till Azure.
