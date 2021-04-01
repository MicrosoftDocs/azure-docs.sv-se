---
title: Skapa en programinriktad miljö – Azure
description: Den här artikeln visar hur du skapar en programinriktad miljö med Cloud Shell koloni och Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94918261"
---
# <a name="create-an-application-centric-environment"></a>Skapar en programinriktad miljö

[Quali CloudShell-koloni](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) är en SaaS-plattform för att leverera infrastruktur automatisering i skala för komplexa programinriktade miljöer i moln teknologier, inklusive Azure och Kubernetes. CloudShell-kolonier kompletterar Azure DevTest Labs för att hjälpa utvecklare att distribuera komplexa program i värde strömmen, hela vägen till produktion.

Den här artikeln visar hur du skapar en programinriktad miljö med CloudShell koloni och Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Konfigurera miljön med CloudShell koloni och Microsoft Azure

1. Registrera dig för din kostnads fria utvärderings version av [kolon](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Registrera dig för en kostnadsfri utvärderingsversion":::    
1. Länka ditt Azure-konto ([Visa stegen här](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Välkommen till kolon":::     
1. Bjud in användare till ditt space.
1. Skapa din första skiss med en YAML-fil ([Visa stegen här](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Länka din skiss lagrings platsen i GitHub eller BitBucket till koloni.
    1. Använd en form av en koloni-exempel som grund och ändra efter behov.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Stresstester":::    
    1. Publicera din skiss så att andra kan använda den.
1. Starta din program miljö i ett begränsat läge med hjälp av koloni.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Starta din program miljö i ett begränsat läge med hjälp av kolon":::    

> [!NOTE]
> Du kan också integrera din skiss som en del av ett CI/CD-arbetsflöde i Azure DevOps ([Visa stegen här](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Ansluta till din Azure DevOps-pipeline":::    

## <a name="next-steps"></a>Nästa steg

[Begär en demonstration av kolon](https://info.quali.com/cloudshell-colony-demo-request)
