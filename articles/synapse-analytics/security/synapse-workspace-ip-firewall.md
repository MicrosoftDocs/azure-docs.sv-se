---
title: Konfigurera IP-brandväggens regler
description: En artikel som lär dig att konfigurera IP-brandväggens regler i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 787e600df4185346b250d9a96bdb89927e134075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472028"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>IP-brandväggs regler för Azure Synapse Analytics

I den här artikeln förklaras IP-brandväggens regler och du får lära dig hur du konfigurerar dem i Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg

IP-brandväggsregler beviljar eller nekar åtkomst till din Synapse-arbetsyta baserat på den ursprungliga IP-adressen för varje förfrågan. Du kan konfigurera IP-brandväggsregler för din arbetsyta. Regler för IP-brandvägg som kon figurer ATS på arbets ytans nivå gäller för alla offentliga slut punkter för arbets ytan (dedikerade SQL-pooler, SQL-pool utan server och utveckling).

## <a name="create-and-manage-ip-firewall-rules"></a>Skapa och hantera IP-brandväggsregler

Det finns två sätt att lägga till IP-brandväggens regler i en Synapse-arbetsyta. Om du vill lägga till en IP-brandvägg till din arbets yta väljer du **säkerhet + nätverk** och markerar **Tillåt anslutningar från alla IP-adresser** när arbets ytan skapas.

![Skärm bild som visar knappen säkerhet + nätverk.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal Synapse-arbetsytans IP-konfiguration.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Du kan också lägga till regler för IP-brandvägg till en Synapse-arbetsyta när arbets ytan har skapats. Välj **brand väggar** under **säkerhet** från Azure Portal. Om du vill lägga till en ny regel för IP-brandvägg ger du den ett namn, en Start-IP och en slut-IP. Välj **Spara** när du är klar.

![Azure Synapse-arbetsytans IP-konfiguration i Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Anslutning till Synapse från ditt eget nätverk

Du kan ansluta till din Synapse-arbetsyta med hjälp av Synapse Studio. Du kan också använda SQL Server Management Studio (SSMS) för att ansluta till SQL-resurserna (dedikerade SQL-pooler och Server lös SQL-pool) i din arbets yta.

Kontrol lera att brand väggen i nätverket och den lokala datorn tillåter utgående kommunikation på TCP-portarna 80, 443 och 1443 för Synapse Studio.

Du måste också tillåta utgående kommunikation på UDP-port 53 för Synapse Studio. Om du vill ansluta med verktyg som SSMS och Power BI måste du tillåta utgående kommunikation på TCP-port 1433.


## <a name="next-steps"></a>Nästa steg

Skapa en [Azure DataSynapses-arbetsyta](../quickstart-create-workspace.md)

Skapa en Azure dataSynapses-arbetsyta med en [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)