---
title: Standard storleken för TEMP-mappar är för liten för en roll | Microsoft Docs
description: En moln tjänst roll har en begränsad mängd utrymme för TEMP-mappen. Den här artikeln innehåller några förslag på hur du undviker att ta slut på utrymme.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743210"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>Standard storleken för TEMP-mappar är för liten på en webb-/arbets roll i en moln tjänst (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den tillfälliga standard katalogen för en moln tjänst arbetare eller webb roll har en maximal storlek på 100 MB, vilket kan bli en fullständig tidpunkt. I den här artikeln beskrivs hur du undviker att använda utrymme för den tillfälliga katalogen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Varför får jag slut på utrymme?
Standard Windows-miljövariablerna TEMP och TMP är tillgängliga för kod som körs i ditt program. Både TEMP-och TMP-platser till en enda katalog med en maximal storlek på 100 MB. Alla data som lagras i den här katalogen sparas inte i moln tjänstens livs cykel. om roll instanserna i en moln tjänst återvinns rensas katalogen.

## <a name="suggestion-to-fix-the-problem"></a>Förslag för att åtgärda problemet
Implementera något av följande alternativ:

* Konfigurera en lokal lagrings resurs och få åtkomst till den direkt i stället för att använda TEMP eller TMP. Om du vill komma åt en lokal lagrings resurs från kod som körs i ditt program anropar du metoden [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Konfigurera en lokal lagrings resurs och peka TEMP-och TMP-katalogerna för att peka på sökvägen till den lokala lagrings resursen. Den här ändringen ska utföras i metoden [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

I följande kod exempel visas hur du ändrar mål katalogerna för TEMP och TMP inifrån metoden OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Läs en blogg som beskriver [hur du ökar storleken på den temporära mappen för Azure-Webbrollens ASP.net](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Visa fler [fel söknings artiklar](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) för Cloud Services.

Om du vill veta mer om hur du felsöker problem med Cloud Service-roller med hjälp av Azure PaaS Computer Diagnostics-data kan du Visa [Kevin Williamsons blogg serie](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)