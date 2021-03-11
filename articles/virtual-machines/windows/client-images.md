---
title: Använda Windows Client-avbildningar i Azure
description: Så här använder du fördelarna med Visual Studio-prenumerationer för att distribuera Windows 7, Windows 8 eller Windows 10 i Azure för utveckling/testnings scenarier
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d09f27eebba5db2edb56185b658c8ae2b06e42d3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552277"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Använda en Windows-klient i Azure för dev/test-scenarier
Du kan använda Windows 7, Windows 8 eller Windows 10 Enterprise (x64) i Azure för utveckling/testning-scenarier förutsatt att du har en lämplig Visual Studio-prenumeration (tidigare MSDN). 

Om du vill köra Windows 10 i en produktions miljö, se [hur du distribuerar Windows 10 på Azure med värd rättigheter för flera innehavare](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Prenumerations berättigande
Aktiva Visual Studio-prenumeranter (personer som har köpt en prenumerations licens för Visual Studio) kan använda Windows-klient avbildningar för utvecklings-och testnings ändamål. Windows Client-avbildningar kan användas på din egen maskin vara eller på virtuella Azure-datorer.

Vissa Windows-klient-avbildningar är tillgängliga från Azure Marketplace. Visual Studio-prenumeranter inom vilken typ av erbjudande som helst kan också [förbereda och skapa](prepare-for-upload-vhd-image.md) 64-bitars Windows 7-, Windows 8-eller Windows 10-avbildningar och sedan [Ladda upp till Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Berättigade erbjudanden och klient avbildningar
I följande tabell visas de ID: n som är kvalificerade för att distribuera Windows-klient avbildningar via Azure Marketplace. Windows-klient avbildningarna är bara synliga för följande erbjudanden. 

| Erbjudandets namn | Erbjudande nummer | Tillgängliga klient avbildningar | 
|:--- |:---:|:---:|
| [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Visual Studio Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Visual Studio Premium med MSDN (förmån)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8,1 Enterprise N (x64) <br> Windows 7 Enterprise N med SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Kontrol lera din Azure-prenumeration
Om du inte känner till ditt erbjudande-ID kan du hämta det via Azure Portal på något av följande två sätt:  
- I fönstret *prenumerationer* : ![ erbjud ID-information från Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- Du kan också klicka på **fakturering** och sedan på ditt PRENUMERATIONS-ID. Erbjudande-ID visas i *fakturerings* fönstret.
Du kan också Visa erbjudande-ID: t från [fliken prenumerationer](https://account.windowsazure.com/Subscriptions) i Azure-konto portalen: ![ erbjud ID-information från Azure-konto portalen](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Nästa steg
Nu kan du distribuera dina virtuella datorer med hjälp av [PowerShell](quick-create-powershell.md), [Resource Manager-mallar](ps-template.md)eller [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).