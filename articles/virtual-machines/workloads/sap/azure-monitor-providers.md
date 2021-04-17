---
title: Azure Monitor för SAP-lösningar leverantörer| Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP-lösningsleverantörer.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: fe8ac81a8b04aa88ce91a978c2bc9b979a065370
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576167"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor för SAP-lösningsleverantörer (förhandsversion)

## <a name="overview"></a>Översikt  

När det gäller Azure Monitor för SAP-lösningar avser *en providertyp* en specifik *provider*. Till exempel *SAP HANA*, som är konfigurerat för en specifik komponent i SAP-miljön, till exempel SAP HANA databas. En provider innehåller anslutningsinformationen för motsvarande komponent och hjälper till att samla in telemetridata från komponenten. En Azure Monitor för SAP-lösningar resurs (även kallat SAP-övervakningsresurs) kan konfigureras med flera providers av samma providertyp eller flera providers av flera providertyper.
   
Kunder kan välja att konfigurera olika leverantörstyper för att aktivera datainsamling från motsvarande komponent i sap-miljön. Kunder kan till exempel konfigurera en provider för SAP HANA typ av provider, en annan provider för klusterprovidertyp med hög tillgänglighet och så vidare.  

Kunder kan också välja att konfigurera flera leverantörer av en viss providertyp så att de återanvänder samma SAP-övervakningsresurs och tillhörande hanterade grupp. Läs mer om hanterad resursgrupp. För offentlig förhandsversion stöds följande providertyper:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Kluster med hög tillgänglighet
- Operativsystem

![Azure Monitor för SAP-lösningsleverantörer](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Kunder rekommenderas att konfigurera minst en provider från de tillgängliga providertyperna vid tidpunkten för distributionen av SAP Monitor-resursen. Genom att konfigurera en provider initierar kunderna datainsamling från motsvarande komponent som providern är konfigurerad för.   

Om kunderna inte konfigurerar några providers vid tidpunkten för distributionen av SAP-övervakningsresursen samlas inga telemetridata in, även om SAP-övervakningsresursen kommer att distribueras. Kunder har möjlighet att lägga till providers efter distributionen via SAP-övervakningsresursen inom Azure Portal. Kunder kan lägga till eller ta bort providers från SAP-övervakningsresursen när som helst.

## <a name="provider-type-sap-netweaver"></a>Providertyp: SAP NetWeaver

Kunder kan konfigurera en eller flera leverantörer av providertyp SAP NetWeaver för att aktivera datainsamling från SAP NetWeaver-lagret. AMS NetWeaver-providern använder det befintliga [SAPControl-webbtjänstgränssnittet](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) för att hämta lämplig telemetriinformation.

För den aktuella versionen är nedan de standardbaserade SOAP-webbmetoder som anropas av AMS.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

I den offentliga förhandsversionen kan kunder förvänta sig att se följande data med SAP NetWeaver-providern: 
- System- och instanstillgänglighet
- Användning av arbetsprocess
- Köanvändning
- Statistik över låsning i sekvens.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Providertyp: SAP HANA

Kunder kan konfigurera en eller flera leverantörer av providertyp *SAP HANA* att aktivera datainsamling från SAP HANA databas. Providern SAP HANA ansluter till SAP HANA-databasen via SQL-porten, hämtar telemetridata från databasen och push-lar dem till Log Analytics-arbetsytan i kundprenumerationen. Providern SAP HANA samlar in data var 1 minut från den SAP HANA databasen.  

I den offentliga förhandsversionen kan kunderna förvänta sig att se följande data hos SAP HANA-providern: Underliggande infrastrukturanvändning, SAP HANA-värdstatus, SAP HANA-systemreplikering och SAP HANA Backup-telemetridata. För att SAP HANA en provider krävs värd-IP-adress, HANA SQL-portnummer samt användarnamn och lösenord för SYSTEMDB. Kunder rekommenderas att konfigurera SAP HANA-providern mot SYSTEMDB, men fler leverantörer kan konfigureras mot andra databasklienter.

![Azure Monitor för SAP-lösningsleverantörer – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Providertyp: Microsoft SQL Server

Kunder kan konfigurera en eller flera leverantörer av leverantörstyp *Microsoft SQL Server* aktivera datainsamling från [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server-providern ansluter till Microsoft SQL Server via SQL-porten, hämtar telemetridata från databasen och push-erar dem till Log Analytics-arbetsytan i kundprenumerationen. Den SQL Server måste konfigureras för SQL-autentisering och en SQL Server inloggning, med SAP DB som standarddatabas för providern, måste skapas. SQL Server samlar in data från SQL Server var 60:e sekund upp till varje timme.  

I den offentliga förhandsversionen kan kunder förvänta sig att se följande data hos SQL Server-providern: underliggande infrastrukturanvändning, de främsta SQL-uttrycken, den största tabellen, problem som registrerats i SQL Server-felloggarna, blockerande processer med mera.  

För att Microsoft SQL Server en provider krävs SAP-system-ID, värd-IP-adress, SQL Server-portnummer och SQL Server inloggningsnamn och lösenord.

![Azure Monitor för SAP-lösningsleverantörer – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Providertyp: Kluster med hög tillgänglighet
Kunder kan konfigurera en eller flera leverantörer av providertyp kluster med hög tillgänglighet för att aktivera datainsamling från *Pacemaker-kluster* i SAP-liggande. Klusterleverantören med hög tillgänglighet ansluter till Pacemaker med [hjälp ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) slutpunkten, hämtar telemetridata från databasen och push-erar dem till Log Analytics-arbetsytan i kundprenumerationen. Klusterleverantören med hög tillgänglighet samlar in data var 60:e sekund från Pacemaker.  

I den offentliga förhandsversionen kan kunder förvänta sig att se följande data med klusterprovidern för hög tillgänglighet:   
 - Klusterstatus representeras som sammanslagning av nod- och resursstatus 
 - [Andra](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor sap-lösningsproviders – kluster med hög tillgänglighet](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Två primära steg ingår för att konfigurera en klusterprovider med hög tillgänglighet:

1. Installera [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) på *varje nod* i Pacemaker-klustret.

   Du har två alternativ för att installera ha_cluster_exporter:
   
   - Använd Azure Automation för att distribuera ett kluster med hög tillgänglighet. Skripten installeras [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) varje klusternod.  
   - Gör en [manuell installation.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Konfigurera en klusterprovider med hög tillgänglighet *för varje* nod i pacemakerklustret.

   Följande information krävs för att konfigurera klusterprovidern med hög tillgänglighet:
   
   - **Namn**. Ett namn för den här providern. Det bör vara unikt för den här Azure Monitor för SAP-lösningsinstansen.
   - **Prometheus-slutpunkt**. http \: // \<servername or ip address\> :9664/metrics.
   - **SID**. För SAP-system använder du SAP SID. För andra system (till exempel NFS-kluster) använder du ett namn med tre tecken för klustret. SID måste vara skild från andra kluster som övervakas.   
   - **Klusternamn**. Klusternamnet som används när klustret skapas. Klusternamnet finns i klusteregenskapen `cluster-name` .
   - **Värdnamn**. Linux-värdnamnet för den virtuella datorn.  

## <a name="provider-type-os-linux"></a>Providertyp: OS (Linux)
Kunder kan konfigurera en eller flera leverantörer av providertyp OS (Linux) för att aktivera datainsamling från BareMetal eller VM Node. OS-providern (Linux) ansluter till BareMetal- eller VM-noder med hjälp av Node_Exporter-slutpunkten, hämtar telemetridata från noderna och push-erar dem till Log Analytics-arbetsytan i kundprenumerationen. [](https://github.com/prometheus/node_exporter)   OS-providern (Linux) samlar in data var 60:e sekund för de flesta mått från noder. 

I den allmänt tillgängliga förhandsversionen kan kunderna förvänta sig att se följande data med OS-providern (Linux): 
   - CPU-användning, CPU-användning efter process 
   - Diskanvändning, I/O& skrivning 
   - Minnesdistribution, minnesanvändning, minnesväxlingsanvändning 
   - Nätverksanvändning, inkommande nätverkstrafik & utgående trafikinformation. 

För att konfigurera en OS-provider (Linux) ingår två huvudsakliga steg:
1. Installera [Node_Exporter på varje](https://github.com/prometheus/node_exporter)   BareMetal- eller VM-noder.
   Du har två alternativ för att [installera Node_exporter](https://github.com/prometheus/node_exporter): 
      - För Automation-installation med Ansible använder [Node_Exporter](https://github.com/prometheus/node_exporter) på varje BareMetal- eller VM-noder för att installera OS-providern (Linux).  
      - Gör en [manuell installation.](https://prometheus.io/docs/guides/node-exporter/)

2. Konfigurera en OS-provider (Linux) för varje BareMetal- eller VM-nodinstans i din miljö. 
   För att konfigurera OS-providern (Linux) krävs följande information: 
      - Namn. Ett namn för den här providern. Det bör vara unikt för den här Azure Monitor för SAP-lösningsinstansen. 
      - Nodexporterslutpunkt. Vanligtvis <servername or ip address> http://:9100/metrics 

> [!NOTE]
> 9100 är en port som exponeras för Node_Exporter slutpunkt.

> [!Warning]
> Se till att Node Exporter fortsätter att köras efter omstart av noden. 

## <a name="next-steps"></a>Nästa steg

- Se [onboarding-stegen](./azure-monitor-sap-quickstart.md) och skapa din första Azure Monitor för SAP-lösningsresursen.
- Har du frågor om Azure Monitor för SAP-lösningar? Läs avsnittet [vanliga frågor och](./azure-monitor-faq.md) svar
