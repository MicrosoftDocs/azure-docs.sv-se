---
title: Publicera till Azure Security Center med PowerShell
description: Det här dokumentet går igenom processen för att aktivera Azure Security Center med PowerShell-cmdlets.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: f9ab258f59279112d0b90c5d460e6761ac911a2d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713362"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatisera onboarding av Azure Security Center powershell

Du kan skydda dina Azure-arbetsbelastningar programmatiskt med hjälp Azure Security Center PowerShell-modulen.
Med PowerShell kan du automatisera uppgifter och undvika den mänskliga faktorn i manuella uppgifter. Detta är särskilt användbart i storskaliga distributioner som omfattar dussintals prenumerationer med hundratals och tusentals resurser – som alla måste skyddas från början.

Registrering Azure Security Center PowerShell gör att du programmatiskt kan automatisera registrering och hantering av dina Azure-resurser och lägga till nödvändiga säkerhetskontroller.

Den här artikeln innehåller ett PowerShell-exempelskript som kan ändras och användas i din miljö för att distribuera Security Center över dina prenumerationer. 

I det här exemplet aktiverar vi Security Center på en prenumeration med ID: d07c0080-170c-4c24-861d-9c817742786c och tillämpar de rekommenderade inställningarna som ger en hög skyddsnivå genom att aktivera Azure Defender, som ger avancerade funktioner för skydd och identifiering av hot:

1. Aktivera [Azure Defender](azure-defender.md). 
 
2. Ange Log Analytics-arbetsytan som Log Analytics-agenten ska skicka data som samlas in på de virtuella datorer som är associerade med prenumerationen – i det här exemplet en befintlig användardefinierad arbetsyta (myWorkspace).

3. Aktivera Security Center automatisk agentetablering som [distribuerar Log Analytics-agenten](security-center-enable-data-collection.md#auto-provision-mma).

5. Ange organisationens [CISO som säkerhetskontakt för Security Center och viktiga händelser](security-center-provide-security-contact-details.md).

6. Tilldela Security Center [standardsäkerhetsprinciper](tutorial-security-policy.md).

## <a name="prerequisites"></a>Förutsättningar

De här stegen bör utföras innan du kör Security Center cmdlets:

1. Kör PowerShell som administratör.

1. Kör följande kommandon i PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Publicera Security Center powershell

1. Registrera dina prenumerationer på Security Center resursprovidern:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Valfritt: Ange täckningsnivå (Azure Defender på/av) för prenumerationerna. Om det är odefinierat är Defender inaktiverat:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Konfigurera en Log Analytics-arbetsyta som agenterna ska rapportera till. Du måste ha en Log Analytics-arbetsyta som du redan har skapat, som prenumerationens virtuella datorer rapporterar till. Du kan definiera flera prenumerationer för att rapportera till samma arbetsyta. Om den inte definieras används standardarbetsytan.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Installera Log Analytics-agenten automatiskt på dina virtuella Azure-datorer:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Vi rekommenderar att du aktiverar automatisk etablering för att se till att dina virtuella Azure-datorer skyddas automatiskt av Azure Security Center.
    >

1. Valfritt: Vi rekommenderar [](security-center-provide-security-contact-details.md) starkt att du definierar kontaktuppgifter för säkerhet för de prenumerationer som du registrera, som kommer att användas som mottagare av aviseringar och meddelanden som genereras av Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert```

1. Tilldela standardprincipinitiativ Security Center princip:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Du har nu Azure Security Center med PowerShell.

Nu kan du använda dessa PowerShell-cmdlets med automatiseringsskript för att programmatiskt iterera över prenumerationer och resurser. Detta sparar tid och minskar sannolikheten för mänskliga fel. Du kan använda det här [exempelskriptet](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) som referens.




## <a name="see-also"></a>Se även
Mer information om hur du kan använda PowerShell för att automatisera onboarding till Security Center finns i följande artikel:

* [Az.Security](/powershell/module/az.security)

Mer information om Security Center finns i följande artikel:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och svarar på säkerhetsaviseringar.