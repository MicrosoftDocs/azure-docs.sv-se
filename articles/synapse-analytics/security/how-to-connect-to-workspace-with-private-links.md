---
title: Ansluta till en Synapse-arbetsyta med privata länkar
description: I den här artikeln får du lära dig hur du ansluter till din Azure Synapse-arbetsyta med privata länkar
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9782cce4165487b612c0295dc893d120ed043225
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218271"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links"></a>Ansluta till din Azure Synapse-arbetsyta via privat länk

I den här artikeln får du lära dig hur du skapar en privat slut punkt till din Azure Synapse-arbetsyta. Mer information finns i [privata länkar och privata slut punkter](../../private-link/index.yml) .

## <a name="step-1-register-network-resource-provider"></a>Steg 1: registrera nätverks resurs leverantör

Registrera nätverks resurs leverantören om du inte redan gjort det. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Välj *Microsoft. Network* i listan över resurs leverantörer när du [registrerar](../../azure-resource-manager/management/resource-providers-and-types.md)dig. Om nätverks resurs leverantören redan är registrerad, Fortsätt sedan till steg 2.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Steg 2: öppna din Azure Synapse-arbetsyta i Azure Portal

Välj **privat slut punkts anslutning** under **säkerhet**. 
![Öppna Azure dataSynapses-arbetsyta i Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

På nästa skärm väljer du **+ privat slut punkt**.

![Öppen privat slut punkt i Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Steg 3: Välj din prenumeration och regions information

Välj din **prenumeration** och **resurs grupp** under fliken **grundläggande** i fönstret **skapa en privat slut punkt** . Ange ett **namn** på den privata slut punkt som du vill skapa. Välj den **region** där du vill att den privata slut punkten ska skapas.

Privata slut punkter skapas i ett undernät. Prenumerationen, resurs gruppen och den region som valts filtrerar de privata slut punkterna. Välj **Nästa: resurs >** när du är färdig.
![Välj information om prenumeration och region 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Steg 4: Välj din information om din Azure Synapse-arbetsyta

Välj **Anslut till en Azure-resurs i min katalog** på fliken **resurs** . Välj den **prenumeration** som innehåller din Azure Synapse-arbetsyta. **Resurs typen** för att skapa privata slut punkter till en Azure Synapse-arbetsyta är *Microsoft. Synapse/arbets ytor*.

Välj din Azure Synapse-arbetsyta som **resurs**. Varje Azure Synapse-arbetsyta har tre **mål under resurser** som du kan skapa en privat slut punkt för: SQL, SqlOnDemand och dev.

Välj **Nästa: konfigurations>** för att gå vidare till nästa del av installationen.
![Välj information om prenumeration och region 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

På fliken **konfiguration** väljer du det **virtuella nätverket** och **under nätet** där den privata slut punkten ska skapas. Du måste också skapa en DNS-post som mappar till den privata slut punkten.

Välj **Ja** om du vill **integrera med en privat DNS-zon** för att integrera din privata slut punkt med en privat DNS-zon. Om du inte har en privat DNS-zon som är kopplad till din Microsoft Azure Virtual Network skapas en ny privat DNS-zon. Välj **Granska + skapa** när du är färdig.

![Välj information om prenumeration och region 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

När distributionen är klar öppnar du Azure dataSynapses-arbetsytan i Azure Portal och väljer **privata slut punkts anslutningar**. Den nya privata slut punkten och anslutnings namnet för den privata slut punkten som är associerat med den privata slut punkten visas.

![Välj information om prenumeration och region 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)