---
title: Lösnings arkitekturer som använder Azure NetApp Files | Microsoft Docs
description: Innehåller referenser till metod tips för lösnings arkitekturer som använder Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 79ad95179f4ad6d332a848e59ca341b8a9f90b1f
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258233"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Lösningsarkitekturer med Azure NetApp Files
Den här artikeln innehåller referenser till bästa praxis som kan hjälpa dig att förstå lösnings arkitekturerna för att använda Azure NetApp Files.  

Följande diagram sammanfattar de kategorier av lösnings arkitekturer som Azure NetApp Files erbjuder:

![Arkitektur kategorier för lösning](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS-appar och databas lösningar

Det här avsnittet innehåller referenser till lösningar för OSS av Linux-program och databaser. 

### <a name="oracle"></a>Oracle

* [Metod guide för Oracle i Azure-distribution med hjälp av Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [VIRTUELLA Oracle-avbildningar och deras distribution på Microsoft Azure: konfigurations alternativ för delad lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Fördelar med att använda Azure NetApp Files med Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows-appar och SQL Server-lösningar

Det här avsnittet innehåller referenser för Windows-program och SQL Server-lösningar.

### <a name="file-sharing-and-global-file-caching"></a>Fildelning och global cachelagring av filer

* [Vill du bygga din egen Azure NFS? Wrestling Linux-filresurser till molnet](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Global fil-cache/Azure NetApp Files distribution](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Distribuera SQL Server över SMB med Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Distribuera SQL Server Always failover-kluster över SMB med Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Distribuera alltid tillgänglighets grupper med Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>SAP på Azure-lösningar

Det här avsnittet innehåller referenser till SAP på Azure-lösningar. 

### <a name="generic-sap-and-sap-netweaver"></a>Allmän SAP-och SAP-NetWeaver 

* [SAP-program på Microsoft Azure med Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP-program med flera SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>SAP-tekniska community-och blogg inlägg 

* [Azure NetApp Files – SAP HANA säkerhets kopiering på några sekunder](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – Återställ HANA-databasen från en ögonblicks bild säkerhets kopia](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA avlasta säkerhets kopiering med molnbaserad synkronisering](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Påskynda dina SAP HANA system kopior med Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Moln volymer ONTAP och Azure NetApp Files: SAP HANA systemmigrering enkelt](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Infrastruktur lösningar för virtuella datorer

Det här avsnittet innehåller referenser till infrastruktur lösningar för virtuella skriv bord.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Fördelar med att använda Azure NetApp Files med Windows Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Skapa en FSLogix profil behållare för en värd-pool med hjälp av Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [Windows Virtual Desktop i företags skala](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>HPC-lösningar

Det här avsnittet innehåller referenser för HPC-lösningar (data behandling med höga prestanda). 

### <a name="generic-hpc"></a>Allmän HPC

* [Azure NetApp Files: få ut mesta möjliga av din moln lagring](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Kör MPI-arbetsbelastningar med Azure Batch och Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud HPC-miljöer på Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Olja och gas

* [Data behandling med höga prestanda (HPC): olja och gas i Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Kör program vara för program varu simulering på Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Elektronisk design automatisering (EDA)

* [Fördelar med att använda Azure NetApp Files för elektronisk designautomatisering](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab med Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analytics

* [Azure NetApp Files: ett nytt delat fil system som ska användas med SAS-rutnät på Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Azure Platform Services-lösningar

Det här avsnittet innehåller lösningar för Azures plattforms tjänster. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services och Kubernetes

* [Integrera Azure NetApp Files med Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Kubernetes prestanda som inte är i kraft i Azure med Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident – Storage Orchestrator för behållare](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Kör MPI-arbetsbelastningar med Azure Batch och Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
