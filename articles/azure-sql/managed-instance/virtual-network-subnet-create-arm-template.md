---
title: Skapa ett virtuellt nätverk
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du skapar ett virtuellt nätverk som har kon figurer ATS för att stödja distribution av hanterad Azure SQL-instans
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91617714"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Skapa ett virtuellt nätverk för Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln beskriver hur du skapar ett giltigt virtuellt nätverk och undernät där du kan distribuera en hanterad Azure SQL-instans.

Azure SQL-hanterad instans måste distribueras i ett [virtuellt](../../virtual-network/virtual-networks-overview.md)Azure-nätverk. Den här distributionen möjliggör följande scenarier:

- Säker privat IP-adress
- Ansluta till SQL-hanterad instans direkt från ett lokalt nätverk
- Ansluta SQL-hanterad instans till en länkad server eller till ett annat lokalt data lager
- Ansluta SQL-hanterad instans till Azure-resurser  

> [!NOTE]
> Du bör [fastställa storleken på under nätet för SQL-hanterad instans](vnet-subnet-determine-size.md) innan du distribuerar den första instansen. Du kan inte ändra storlek på under nätet när du har lagt till resurserna i.
>
> Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra nätverks konfigurationen för att hantera SQL-hanterad instans. Mer information finns i [ändra ett befintligt virtuellt nätverk för SQL-hanterad instans](vnet-existing-add-subnet.md).
>
> När en hanterad instans har skapats går det inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resurs grupp eller prenumeration.  Det går inte heller att flytta den hanterade instansen till ett annat undernät.
>

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda en mall för Azure Resource Manager distribution.

1. Logga in på Azure-portalen.

2. Välj knappen **distribuera till Azure** :

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Den här knappen öppnar ett formulär som du kan använda för att konfigurera nätverks miljön där du kan distribuera SQL-hanterad instans.

   > [!Note]
   > Den här Azure Resource Manager mallen distribuerar ett virtuellt nätverk med två undernät. Ett undernät, som kallas **ManagedInstances**, är reserverat för SQL-hanterad instans och har en förkonfigurerad routningstabell. Det andra under nätet som kallas **standard** används för andra resurser som ska komma åt SQL-hanterad instans (till exempel Azure Virtual Machines).

3. Konfigurera nätverks miljön. I följande formulär kan du konfigurera parametrar för din nätverks miljö:

   ![Resource Manager-mall för att konfigurera Azure-nätverket](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Du kan ändra namnen på det virtuella nätverket och undernät och justera IP-intervallen som är kopplade till dina nätverks resurser. När du har valt knappen **köp** skapas och konfigureras din miljö med det här formuläret. Om du inte behöver två undernät kan du ta bort standard ett.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Lär dig mer om [anslutnings arkitektur i SQL-hanterad instans](connectivity-architecture-overview.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för SQL-hanterad instans](vnet-existing-add-subnet.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en hanterad instans](instance-create-quickstart.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).
