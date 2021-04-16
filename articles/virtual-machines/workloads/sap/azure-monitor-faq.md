---
title: Vanliga frågor och svar – Azure Monitor för SAP-lösningar | Microsoft Docs
description: I den här artikeln får du svar på vanliga frågor och svar om Azure Monitor SAP-lösningar.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377132"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor vanliga frågor och svar om SAP-lösningar (förhandsversion)
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor (FAQ) om Azure Monitor för SAP-lösningar.  

 - **Måste jag betala för Azure Monitor för SAP-lösningar?**  
Det finns ingen licensavgift för Azure Monitor för SAP-lösningar.  
Kunderna ansvarar dock för kostnaden för hanterade resursgruppskomponenter.  

 - **I vilka regioner är den här tjänsten tillgänglig för offentlig förhandsversion?**  
För offentlig förhandsversion är den här tjänsten tillgänglig i USA, östra 2, USA, västra 2, USA, östra och Europa, västra.  

 - **Måste jag ange behörigheter för att tillåta distribution av en hanterad resursgrupp i min prenumeration?**  
Nej, explicita behörigheter krävs inte.  

 - **Var finns den virtuella insamlardatorn?**  
När du distribuerar en Azure Monitor för SAP-lösningar-resurs rekommenderar vi att kunderna väljer samma virtuella nätverk för övervakningsresursen som SAP HANA servern. Därför rekommenderar vi att den virtuella insamlardatorn finns i samma virtuella nätverk som SAP HANA server. Om kunder använder en icke-HANA-databas finns den virtuella insamlardatorn i samma virtuella nätverk som icke-HANA-databasen.  

 - **Vilka versioner av HANA stöds?**  
HANA 1.0 SPS 12 (rev. 120 eller senare) och HANA 2.0 SPS03 eller senare  

 - **Vilka HANA-distributionskonfigurationer stöds?**  
Följande konfigurationer stöds:
   - En nod (uppskalning) och flera noder (utskalning)  
   - En databascontainer (HANA 1.0 SPS 12) och flera databascontainrar (HANA 1.0 SPS 12 eller HANA 2.0)  
   - Automatisk värd redundans (n+1) och HSR  

 - **Vilka SQL Server versioner stöds?**  
SQL Server 2012 SP4 eller senare.  

 - **Vilka SQL Server konfigurationer stöds?**  
Följande konfigurationer stöds:
   - Standardinstanser eller namngivna fristående instanser på en virtuell dator  
   - Klustrade instanser eller instanser i en AlwaysOn-konfiguration när antingen det virtuella namnet på den klustrade resursen eller AlwaysOn-lyssnaren används. För närvarande samlas inga kluster eller AlwaysOn-specifika mått in    
   - Azure SQL Database (PAAS) stöds inte för närvarande  

 - **Vad händer om jag tar bort den hanterade resursgruppen av misstag?**  
Den hanterade resursgruppen är låst som standard. Därför är risken för oavsiktlig borttagning av den hanterade resursgruppen av kunder minimal.  
Om en kund tar bort den hanterade resursgruppen Azure Monitor för SAP-lösningar slutar fungera. Kunden måste distribuera en ny resurs Azure Monitor för SAP-lösningar börja om.  

 - **Vilka roller behöver jag i min Azure-prenumeration för att distribuera Azure Monitor för SAP-lösningar resurs?**  
Deltagarroll.  

 - **Vad är serviceavtalet för den här produkten?**  
Förhandsversioner undantas från serviceavtal. Läs den fullständiga licensperioden via marketplace Azure Monitor för SAP-lösningar avbildningen.  

 - **Kan jag övervaka hela mitt landskap med den här lösningen?**  
För närvarande kan du övervaka HANA-databasen, den underliggande infrastrukturen, klustret med hög tillgänglighet, Microsoft SQL Server, SAP Netweaver-tillgänglighet och tillgänglighetsmått för SAP-programinstansen i offentlig förhandsversion.  

 - **Ersätter den här tjänsten SAP Solution Manager?**  
Nej. Kunder kan fortfarande använda SAP Solution Manager för övervakning av affärsprocess.  

 - **Vad är värdet av den här tjänsten i traditionella lösningar som SAP HANA Cockpit/Studio?**  
Azure Monitor för SAP-lösningar är inte HANA-databasspecifik. Azure Monitor för SAP-lösningar har även stöd för AnyDB.  

- **Vilka SAP NetWeaver-versioner stöds?**  
SAP NetWeaver 7.0 eller senare.  

- **Vilka SAP NetWeaver-konfigurationer stöds?**  
Stöder KONFIGURATIONER för ABAP, Java och SAP NetWeaver-programserver med dubbla stackar.

- **Varför behöver jag ta bort skyddet från metoder för SAP NetWeaver-programövervakning?**  
I SAP-versioner >= 7.3 skyddas de flesta webbtjänstmetoder som standard. För att hämta tillgänglighets- och prestandamått genom att anropa dessa metoder måste du ta bort skyddet från följande metoder: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic och GetSystemInstancelist.

- **Finns det någon risk med att ta bort skyddet från SAPCONTROL-webmethods?**  
I allmänhet innebär inte oskyddade SAPCONTROL-webmethods någon säkerhetsrisk som [sådan,](https://launchpad.support.sap.com/#/notes/1439348)men om kunderna vill begränsa/förbjuda åtkomst till oskyddade webmethods via serverportar (5XX13 /5XX14) för sapstartsrv kan du göra det genom att lägga till filter i SAP Access Control List (ACL), [OSS-anteckningen](https://service.sap.com/sap/support/notes/1495075) beskriver den konfiguration som krävs för att åstadkomma detta. 

- **Måste jag starta om mina SAP-instanser när jag har utfört systemkonfigurationer för att konfigurera SAP NetWeaver-providern?**  
Ja, när du har oskyddade metoder via SAP-konfigurationsändringar måste du starta om respektive SAP-system för att säkerställa att konfigurationsändringarna uppdateras.  

## <a name="next-steps"></a>Nästa steg

- Skapa din första Azure Monitor för SAP-lösningsresursen.
