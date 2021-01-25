---
title: Publicera till Azure Security Center med PowerShell
description: Det här dokumentet vägleder dig genom processen att aktivera Azure Security Center med PowerShell-cmdletar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 4979ff0010c1f959e8f8fc16f56da61971faf1e9
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757076"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatisera onboarding av Azure Security Center med hjälp av PowerShell

Du kan skydda dina Azure-arbetsbelastningar program mässigt med hjälp av Azure Security Center PowerShell-modulen.
Med hjälp av PowerShell kan du automatisera uppgifter och undvika det mänskliga felet i manuella uppgifter. Detta är särskilt användbart i storskaliga distributioner som innefattar dussin tals prenumerationer med hundratals och tusentals resurser – alla måste skyddas från början.

När du registrerar Azure Security Center med hjälp av PowerShell kan du automatisera registreringen och hanteringen av dina Azure-resurser via programmering och lägga till nödvändiga säkerhets kontroller.

Den här artikeln innehåller ett exempel på ett PowerShell-skript som kan ändras och användas i din miljö för att distribuera Security Center över dina prenumerationer. 

I det här exemplet aktiverar vi Security Center för en prenumeration med ID: d07c0080-170c-4c24-861d-9c817742786c och tillämpar de rekommenderade inställningarna som ger en hög skydds nivå genom att aktivera Azure Defender, som tillhandahåller funktioner för avancerad hot skydd och identifiering:

1. Aktivera [Azure Defender](azure-defender.md). 
 
2. Ange Log Analytics arbets ytan som Log Analytics agent ska skicka de data som samlas in på de virtuella datorer som är associerade med prenumerationen – i det här exemplet en befintlig användardefinierad arbets yta (min arbets yta).

3. Aktivera Security Centers automatiska agent etablering som [distribuerar Log Analytics-agenten](security-center-enable-data-collection.md#auto-provision-mma).

5. Ange organisationens [IT som säkerhets kontakt för Security Center aviseringar och viktiga händelser](security-center-provide-security-contact-details.md).

6. Tilldela Security Centers [Standard säkerhets principer](tutorial-security-policy.md).

## <a name="prerequisites"></a>Krav

De här stegen bör utföras innan du kör Security Center-cmdlet: ar:

1. Kör PowerShell som administratör.

1. Kör följande kommandon i PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Publicera Security Center med PowerShell

1. Registrera dina prenumerationer på Security Center Resource provider:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Valfritt: Ange täcknings nivå (Azure Defender på/av) för prenumerationerna. Om odefinierad, är Defender av:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Konfigurera en Log Analytics arbets yta som agenterna ska rapportera till. Du måste ha en Log Analytics arbets yta som du redan har skapat, som prenumerationens virtuella datorer ska rapportera till. Du kan definiera flera prenumerationer för att rapportera till samma arbets yta. Om inget värde anges används standard arbets ytan.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Automatisk etablering av installation av Log Analytics agent på dina virtuella Azure-datorer:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Vi rekommenderar att du aktiverar automatisk etablering för att se till att dina virtuella Azure-datorer skyddas automatiskt av Azure Security Center.
    >

1. Valfritt: Vi rekommenderar starkt att du definierar säkerhets kontakt uppgifterna för de prenumerationer du registrerar, som ska användas som mottagare av aviseringar och meddelanden som genereras av Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Tilldela standard Security Center policy initiativ:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Du har registrerat Azure Security Center med PowerShell.

Du kan nu använda dessa PowerShell-cmdlets med Automation-skript för att program mässigt iterera över prenumerationer och resurser. Detta sparar tid och minskar sannolikheten för mänskligt fel. Du kan använda det här [exempel skriptet](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) som referens.




## <a name="see-also"></a>Se även
Mer information om hur du kan använda PowerShell för att automatisera onboarding till Security Center finns i följande artikel:

* [AZ. Security](/powershell/module/az.security)

Mer information om Security Center finns i följande artikel:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar och åtgärdar säkerhets aviseringar.