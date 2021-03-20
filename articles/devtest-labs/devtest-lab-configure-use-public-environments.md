---
title: Konfigurera och använda offentliga miljöer i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar och använder offentliga miljöer (Azure Resource Manager mallar i en git-lagrings platsen) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482674"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurera och använda offentliga miljöer i Azure DevTest Labs
Azure DevTest Labs har ett [offentligt lager av Azure Resource Manager mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa. Den här lagrings platsen innehåller mallar som används ofta, till exempel Azure Web Apps, Service Fabric kluster och utveckling av SharePoint-servergrupper. Den här funktionen liknar den offentliga lagrings platsen för artefakter som ingår i varje labb som du skapar. Miljö lagrings platsen gör det möjligt för dig att snabbt komma igång med förredigerade miljömallar med lägsta indataparametrar för att ge dig en smidig upplevelse för att komma igång med PaaS-resurser inom labb. 

## <a name="configuring-public-environments"></a>Konfigurera offentliga miljöer
Som labb ägare kan du aktivera lagrings platsen för den offentliga miljön för ditt labb under skapandet av labbet. Om du vill aktivera offentliga miljöer för ditt labb väljer du **på** för fältet **offentliga miljöer** när du skapar ett labb. 

![Aktivera offentlig miljö för ett nytt labb](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


För befintliga labb är den offentliga miljöns lagrings plats inte aktive rad. Aktivera det manuellt för att använda mallar i lagrings platsen. För labb som skapats med Resource Manager-mallar är lagrings platsen även inaktive rad som standard.

Du kan aktivera/inaktivera offentliga miljöer för ditt labb och även göra endast vissa miljöer tillgängliga för labb användare med hjälp av följande steg: 

1. Välj **konfiguration och principer** för ditt labb. 
2. I avsnittet **Virtual Machine Bases** väljer du **offentliga miljöer**.
3. Välj **Ja** om du vill aktivera offentliga miljöer för labbet. Annars väljer du **Nej**. 
4. Om du har aktiverat offentliga miljöer är alla miljöer i lagrings platsen aktiverade som standard. Du kan välja en miljö för att göra den inte tillgänglig för dina labb användare. 

![Sidan offentliga miljöer](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Använda miljömallar som en labb användare
Som labb användare kan du skapa en ny miljö från den aktiverade listan över miljömallar genom att helt enkelt välja **+ Lägg till** i verktygsfältet på sidan labb. I listan över baser finns mallarna för offentliga miljöer som har Aktiver ATS av din labb administratör överst i listan.

![Mallar för offentliga nätverk](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Nästa steg
Den här lagrings platsen är en lagrings plats med öppen källkod som du kan använda för att lägga till vanliga och användbara Resource Manager-mallar. För att bidra ska du bara skicka en pull-begäran mot lagrings platsen.  
