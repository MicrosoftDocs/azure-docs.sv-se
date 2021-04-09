---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623461"
---
* När du skapar en ny arbets yta kan du antingen skapa tjänster som behövs av arbets ytan automatiskt eller använda befintliga tjänster. Om du vill använda __befintliga tjänster från en annan Azure-prenumeration__ än arbets ytan måste du registrera Azure Machine Learning namn området i prenumerationen som innehåller tjänsterna. Om du till exempel skapar en arbets yta i prenumeration A som använder ett lagrings konto från prenumeration B måste Azure Machine Learning namn området registreras i prenumeration B innan du kan använda lagrings kontot med arbets ytan.

    Resurs leverantören för Azure Machine Learning är __Microsoft. MachineLearningServices__. Information om hur du kan se om den är registrerad och hur du registrerar den finns i artikeln [Azure-resurs leverantörer och typer](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > Detta gäller endast för resurser som anges när arbets ytan skapas. Azure Storage konton, Azure Container register, Azure Key Vault och Application Insights.
