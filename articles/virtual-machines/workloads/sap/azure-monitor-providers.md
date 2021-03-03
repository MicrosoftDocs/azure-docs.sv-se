---
title: Azure Monitor för SAP-lösnings leverantörer | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP Solutions-leverantörer.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 1282d1916d669f1026707e15cc8d5437d885087f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669004"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor för SAP Solutions-providers (för hands version)

## <a name="overview"></a>Översikt  

I samband med Azure Monitor för SAP-lösningar refererar en *providertyp* till en speciell *Provider*. Till exempel *SAP HANA*, som har kon figurer ATS för en speciell komponent i SAP-landskap, t. ex. SAP HANA Database. En provider innehåller anslutnings informationen för motsvarande komponent och hjälper till att samla in telemetridata från den komponenten. En Azure Monitor för SAP-lösningar (även kallat SAP Monitor-resurs) kan konfigureras med flera leverantörer av samma providertyp eller flera providers för flera typer av leverantörer.
   
Kunderna kan välja att konfigurera olika typer av leverantörer för att aktivera insamling av data från motsvarande komponenter i deras SAP-landskap. Kunder kan till exempel konfigurera en provider för SAP HANA typ av Provider, en annan provider för kluster leverantör med hög tillgänglighet och så vidare.  

Kunder kan också välja att konfigurera flera leverantörer av en speciell typ av Provider för att återanvända samma SAP Monitor-resurs och tillhör ande hanterade grupp. Luta mer om hanterad resurs grupp. För offentlig för hands version stöds följande typer av providers:   
- SAP HANA
- Kluster med hög tillgänglighet
- Microsoft SQL Server

![Azure Monitor för SAP Solutions-leverantörer](./media/azure-monitor-sap/azure-monitor-providers.png)

Kunderna rekommenderas att konfigurera minst en provider från de tillgängliga leverantörs typerna när du distribuerar SAP Monitor-resursen. Genom att konfigurera en leverantör initierar kunden data insamling från motsvarande komponent som providern är konfigurerad för.   

Om kunderna inte konfigurerar några providrar vid distribution av SAP Monitor-resursen, kommer inga telemetridata att samlas in, även om SAP Monitor-resursen kommer att distribueras. Kunder har möjlighet att lägga till providrar efter distribution via SAP Monitor-resurs inom Azure Portal. Kunder kan lägga till eller ta bort providrar från SAP Monitor-resursen när som helst.

> [!Tip]
> Om du vill att Microsoft ska implementera en speciell Provider måste du lämna feedback via länken i slutet av det här dokumentet eller kontakta ditt konto team.  

## <a name="provider-type-sap-hana"></a>Typ av Provider SAP HANA

Kunder kan konfigurera en eller flera providers av leverantörs typ *SAP HANA* för att aktivera data insamling från SAP HANA-databasen. SAP HANA-providern ansluter till SAP HANA-databasen via SQL-port, hämtar telemetridata från databasen och skickar den till Log Analytics-arbetsytan i kund prenumerationen. SAP HANA-providern samlar in data var 1 minut från SAP HANA-databasen.  

I en offentlig för hands version kan kunderna se följande data med SAP HANA provider: underliggande infrastruktur användning, SAP HANA värd status, SAP HANA systemreplikering och SAP HANA data för att säkerhetskopiera telemetri. Om du vill konfigurera SAP HANA-providern måste värd-IP-adress, HANA SQL-portnummer och SYSTEMDB användar namn och lösen ord anges. Kunder rekommenderas att konfigurera SAP HANA-providern mot SYSTEMDB, men fler providers kan konfigureras mot andra databas klienter.

![Azure Monitor för SAP Solutions-providers – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Typ av leverantörs kluster med hög tillgänglighet
Kunder kan konfigurera en eller flera leverantörer av leverantörs typ *kluster med hög tillgänglighet* för att aktivera data insamling från pacemaker-kluster i SAP-landskapet. Kluster leverantören med hög tillgänglighet ansluter till pacemaker, använder [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) slut punkt, hämtar telemetridata från databasen och skickar den till Log Analytics arbets yta i kund prenumerationen. Kluster leverantören med hög tillgänglighet samlar in data var 60 sekund från pacemaker.  

I en offentlig för hands version kan kunderna se följande data med kluster leverantör med hög tillgänglighet:   
 - Kluster status som visas som sammanslagning av nod-och resurs status 
 - [andra](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor för SAP Solutions-leverantörer – kluster med hög tillgänglighet](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

För att konfigurera en kluster leverantör med hög tillgänglighet ingår två primära steg:

1. Installera [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) i *varje* nod i pacemaker-klustret.

   Du har två alternativ för att installera ha_cluster_exporter:
   
   - Använd Azure Automation skript för att distribuera ett kluster med hög tillgänglighet. Skripten installeras [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) på varje klusternod.  
   - Gör en [manuell installation](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Konfigurera en kluster leverantör med hög tillgänglighet för *varje* nod i pacemaker-klustret.

   Följande information krävs för att konfigurera kluster leverantören med hög tillgänglighet:
   
   - **Namn**. Ett namn för den här providern. Det måste vara unikt för den här Azure Monitor för SAP-lösningar-instans.
   - **Prometheus-slutpunkt**. http \: // \<servername or ip address\> : 9664/Metrics.
   - **Sid**. För SAP-system använder du SAP SID. För andra system (t. ex. NFS-kluster) använder du ett namn med tre bokstäver för klustret. SID måste skilja sig från andra kluster som övervakas.   
   - **Kluster namn**. Kluster namnet som används när klustret skapas. Kluster namnet kan hittas i kluster egenskapen `cluster-name` .
   - **Hostname**. Linux-värdnamn för den virtuella datorn.  


## <a name="provider-type-os-linux"></a>Provider typ OS (Linux)
Kunder kan konfigurera en eller flera providrar för providerns typ av operativ system (Linux) för att aktivera data insamling från BareMetal eller VM-noden. OS-providern (Linux) ansluter till BareMetal eller VM-noder, med [Node_Exporter](https://github.com/prometheus/node_exporter)   slut punkt, hämtar telemetridata från noderna och skickar dem till Log Analytics arbets yta i kund prenumerationen. OS (Linux) samlar in data var 60: e sekund för de flesta måtten från noderna. 

I en offentlig för hands version kan kunderna se följande data med OS-Provider (Linux): 
   - CPU-användning, CPU-användning per process 
   - Disk användning, I/O-läsning & skrivning 
   - Minnes distribution, minnes användning, växlings minnes användning 
   - Nätverks användning, inkommande & utgående trafik information i nätverket. 

För att konfigurera en OS-Provider (Linux) är två primära steg inblandade:
1. Installera [Node_Exporter](https://github.com/prometheus/node_exporter)   på varje BareMetal eller VM-noder.
   Du har två alternativ för att installera [Node_exporter](https://github.com/prometheus/node_exporter): 
      - För Automation-installation med Ansible använder du [Node_Exporter](https://github.com/prometheus/node_exporter) på varje BAREMETAL eller VM-noder för att installera OS-providern (Linux).  
      - Gör en [manuell installation](https://prometheus.io/docs/guides/node-exporter/).

2. Konfigurera en OS-Provider (Linux) för varje BareMetal eller instans av VM-noden i din miljö. 
   Om du vill konfigurera OS (Linux)-providern krävs följande information: 
      - Namn. Ett namn för den här providern. Det måste vara unikt för den här Azure Monitor för SAP-lösningar-instans. 
      - Endpoint för Node-verktyget. Vanligt vis http:// <servername or ip address> : 9100/Metrics 

> [!NOTE]
> 9100 är en port som exponeras för Node_Exporter slut punkt.

> [!Warning]
> Se till att Node-exporten fortsätter att köras efter nodens omstart. 


## <a name="provider-type-microsoft-sql-server"></a>Typ av Provider Microsoft SQL Server

Kunder kan konfigurera en eller flera providers av leverantörs typ *Microsoft SQL Server* för att aktivera data insamling från [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server providern ansluter till Microsoft SQL Server över SQL-porten hämtar telemetridata från databasen och skickar dem till arbets ytan Log Analytics i kund prenumerationen. SQL Server måste konfigureras för SQL-autentisering och en SQL Server inloggning, med SAP DB som standard databas för providern, måste skapas. SQL Server-Provider samlar in data mellan var 60: e sekund i varje timme från SQL Server.  

I en offentlig för hands version kan kunderna se följande data med SQL Server provider: underliggande infrastruktur användning, översta SQL-uttryck, högsta största tabell, problem som registrerats i SQL Server fel loggar, blockera processer och andra.  

Om du vill konfigurera Microsoft SQL Server providern måste du ange ID för SAP-system, värd-IP-adress, SQL Server port nummer och SQL Server inloggnings namn och lösen ord.

![Azure Monitor för SAP Solutions-leverantörer – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Nästa steg

- Skapa din första Azure Monitor för SAP-lösnings resurs.
- Har du frågor om Azure Monitor för SAP-lösningar? Läs avsnittet med [vanliga frågor och svar](./azure-monitor-faq.md)
