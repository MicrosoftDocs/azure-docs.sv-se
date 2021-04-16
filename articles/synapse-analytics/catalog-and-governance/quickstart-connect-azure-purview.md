---
title: Ansluta ett Azure Purview-konto 
description: Ansluta ett Azure Purview-konto till en Synapse-arbetsyta.
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ad9250d022f02db06c9c5251c1fccfbfb730af67
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567884"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Snabbstart: Ansluta ett Azure Purview-konto till en Synapse-arbetsyta 


I den här snabbstarten registrerar du ett Azure Purview-konto på en Synapse-arbetsyta. Med den anslutningen kan du identifiera Azure Purview-tillgångar och interagera med dem via Synapse-funktioner. 

Du kan utföra följande uppgifter i Synapse: 
- Använd sökrutan längst upp för att hitta Purview-tillgångar baserat på nyckelord 
- Förstå data baserat på metadata, härledning, anteckningar 
- Ansluta dessa data till din arbetsyta med länkade tjänster eller integreringsdatauppsättningar 
- Analysera dessa datauppsättningar med Synapse Apache Spark, Synapse SQL och Dataflöde 

## <a name="prerequisites"></a>Förutsättningar 
- [Azure Purview-konto](../../purview/create-catalog-portal.md) 
- [Synapse-arbetsyta](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Logga in på en Synapse-arbetsyta 

Gå till  [https://web.azuresynapse.net](https://web.azuresynapse.net) och logga in på din arbetsyta. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Behörigheter för att ansluta ett Azure Purview-konto 

- Om du vill ansluta ett Azure Purview-konto till  en Synapse-arbetsyta behöver du rollen Deltagare i Synapse-arbetsytan från Azure Portal IAM och du behöver åtkomst till det Azure Purview-kontot. Mer information finns i [Azure Purview-behörigheter.](../../purview/catalog-permissions.md)

## <a name="connect-an-azure-purview-account"></a>Ansluta ett Azure Purview-konto  

- På Synapse-arbetsytan går du till **Hantera**  ->  **Azure Purview**. Välj **Anslut till ett Purview-konto.** 
- Du kan välja **Från Azure-prenumeration** eller **Ange manuellt.** **Från Azure-prenumerationen** kan du välja det konto som du har åtkomst till. 
- När du är ansluten bör du kunna se namnet på Purview-kontot på fliken **Azure Purview-konto.** 
- Du kan använda sökfältet längst upp i mitten av Synapse-arbetsytan för att söka efter data. 

## <a name="nextsteps"></a>Nästa steg 

[Registrera och skanna Azure Synapse i Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Identifiera, ansluta och utforska data i Synapse med hjälp av Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
