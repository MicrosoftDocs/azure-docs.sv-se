---
title: Skapa en Azure SQL-hanterad instans på Azure-bågen
description: Skapa en Azure SQL-hanterad instans på Azure-bågen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280412"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Skapa en Azure SQL-hanterad instans på Azure-bågen

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Logga in på data styrenheten för Azure-bågen

Innan du kan skapa en instans loggar du in på data styrenheten för Azure-bågen om du inte redan är inloggad.

```console
azdata login
```

Du kommer sedan att uppmanas att ange användar namn, lösen ord och system namn område.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Skapa en Azure SQL-hanterad instans

Använd följande kommando om du vill visa tillgängliga alternativ för att skapa forSQL-hanterade instanser:
```console
azdata arc sql mi create --help
```

Använd följande kommando för att skapa en SQL-hanterad instans:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Exempel:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Namn måste innehålla färre än 13 tecken och överensstämmer med [DNS-namngivnings konventioner](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  När du anger minnesallokering och vCore-allokering använder du den här formeln för att se till att skapandet lyckas – för varje 1 vCore behöver du minst 4 GB RAM-minne med kapacitet som är tillgängligt på Kubernetes-noden där SQL-hanterad instans Pod ska köras.
>
>  När du skapar en SQL-instans kan du inte använda versaler i namnet om du etablerar i Azure
>
>  För att lista tillgängliga lagrings klasser i Kubernetes-klustret kör `kubectl get storageclass` 


> [!NOTE]
> Om du vill automatisera skapandet av SQL-instanser och undvika den interaktiva prompten för administratörs lösen ordet, kan du ställa `AZDATA_USERNAME` in `AZDATA_PASSWORD` miljövariablerna till önskat användar namn och lösen ord innan du kör `azdata arc sql mi create` kommandot.
> 
>  Om du har skapat datakontrollanten med AZDATA_USERNAME och AZDATA_PASSWORD i samma terminalserversession, används värdena för AZDATA_USERNAME och AZDATA_PASSWORD för att skapa SQL-hanterad instans.

> [!NOTE]
> Att skapa en hanterad Azure SQL-instans registrerar inte resurserna i Azure. Steg för att registrera resursen finns i följande artiklar: 
> - [Visa loggar och mått med hjälp av Kibana och Grafana](monitor-grafana-kibana.md)
> - [Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Visa instans på Azure-båge

Använd följande kommando för att Visa instansen:

```console
azdata arc sql mi list
```

Utdata bör se ut så här:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Om du använder AKS eller `kubeadm` OpenShift osv. kan du kopiera den externa IP-adressen och port numret härifrån och ansluta till det med ditt favorit verktyg för att ansluta till en SQL-instans/Azure SQL-instans som Azure Data Studio eller SQL Server Management Studio. Men om du använder den virtuella datorn med snabb starts guiden finns mer information i artikeln [ansluta till Azure Arc-aktiverad SQL-hanterad instans](connect-managed-instance.md) .


## <a name="next-steps"></a>Nästa steg
- [Ansluta till Azure Arc-aktiverad SQL-hanterad instans](connect-managed-instance.md)
- [Registrera din instans med Azure och ladda upp mått och loggar om din instans](upload-metrics-and-logs-to-azure-monitor.md)
- [Distribuera Azure SQL-hanterad instans med Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
