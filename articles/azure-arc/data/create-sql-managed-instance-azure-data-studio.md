---
title: Skapa en Azure SQL-hanterad instans med hjälp av Azure Data Studio
description: Skapa en Azure SQL-hanterad instans med hjälp av Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280474"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Skapa SQL-hanterad instans – Azure Arc med Azure Data Studio

Det här dokumentet vägleder dig genom stegen för att installera Azure SQL-hanterad instans – Azure Arc med Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Logga in på data styrenheten för Azure-bågen

Innan du kan skapa en instans loggar du in på data styrenheten för Azure-bågen om du inte redan är inloggad.

```console
azdata login
```

Du uppmanas sedan att ange namn området där datakontrollanten skapas, användar namn och lösen ord för att logga in på kontrollanten.  

> Om du behöver validera namn området kan du köra ```kubectl get pods -A``` för att hämta en lista över alla namn områden i klustret.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Skapa en hanterad Azure SQL-instans på Azure-bågen

- Starta Azure Data Studio
- På fliken anslutningar klickar du på de tre punkterna längst upp till vänster och väljer "ny distribution"
- Från distributions alternativen väljer du **Azure SQL-hanterad instans – Azure Arc** 
  > [!NOTE]
  > Du kan uppmanas att installera det [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] här om det inte redan är installerat.
- Godkänn sekretess-och licens villkoren och klicka på **Välj** längst ned



- På bladet Distribuera Azure SQL-hanterad instans – Azure Arc anger du följande information:
  - Ange ett namn för SQL Server-instansen
  - Ange och bekräfta ett lösen ord för SQL Server-instansen
  - Välj den lagrings klass som passar för data
  - Välj den lagrings klass som passar för loggar

- Klicka på knappen **distribuera**

- Detta bör initiera skapandet av Azure SQL-hanterad instans – Azure-bågen på data styrenheten.

- På några minuter bör din framställning bli slutförd

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Ansluta till Azure SQL Managed instance – Azure Arc från Azure Data Studio

- Logga in på data styrenheten för Azure-bågen genom att ange namn området, användar namnet och lösen ordet för datakontrollanten: 
```console
azdata login
```

- Visa alla Azure SQL-hanterade instanser som har tillhandahållits med hjälp av följande kommandon:

```console
azdata arc sql mi list
```

Utdata bör se ut så här, kopiera ServerEndpoint (inklusive Port numret) härifrån.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- I Azure Data Studio, under fliken **anslutningar** , klickar du på den **nya anslutningen** i vyn **servrar**
- I bladet **anslutning** klistrar du in ServerEndpoint i Server text rutan
- Välj **SQL-inloggning** som autentiseringstyp
- Ange *sa* som användar namn
- Ange kontots lösen ord `sa`
- Du kan också ange det angivna databas namnet för att ansluta till
- Du kan också välja/lägga till en ny server grupp efter behov
- Välj **Anslut** för att ansluta till den Azure SQL-hanterade instansen – Azure-bågen




## <a name="next-steps"></a>Nästa steg

Försök nu att [övervaka din SQL-instans](monitor-grafana-kibana.md)
