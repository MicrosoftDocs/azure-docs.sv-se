---
title: 'SAP på Azure: vilken SAP-programvara som stöds i Azure'
description: Förklarar vad SAP-programvara stöds för distribution i Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673538"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Vilken SAP-programvara stöds för Azure-distributioner
Den här artikeln beskriver hur du kan ta reda på vilka SAP-program som stöds för Azure-distributioner och vilka operativ Systems utgåvor eller DBMS-versioner som behövs.

Utvärdera, om din aktuella SAP-programvara stöds och vilka OS-och DBMS-versioner som stöds med din SAP-program vara i Azure, kommer du att behöva åtkomst till:

- Support anteckningar för SAP
- Produkt tillgänglighets mat ris för SAP



## <a name="general-restrictions-for-sap-workload"></a>Allmänna begränsningar för SAP-arbetsbelastning
Azure IaaS-tjänster som kan användas för SAP-arbetsbelastningar är begränsade till x86-64-eller x64-maskinvara. Det finns inga sparc eller ström CPU-baserade erbjudanden som gäller för SAP-arbetsbelastningar. Kunder som kör sina program på ett operativ system som är tillverkarspecifika för maskin varu arkitekturer som IBM-stordatorer eller AS400, eller där operativ systemen HP-UX, Solaris eller AIX används, måste ändra sina SAP-program, inklusive DBMS till något av följande operativ system:

- Windows Server 64-bitars för x86-64-plattformen
- SUSE Linux 64-bitars för x86-64-plattformen
- Red Hat Linux 64-bitars plattform för x86-64
- Oracle Linux 64-bitars för x86-64-plattformen

I kombination med SAP-program, stöds inga andra operativ system versioner eller Linux-distributioner. Detaljerad information om specifika versioner och fall dokumenteras senare i dokumentet.


## <a name="you-start-here"></a>Du börjar här
Start punkten för dig är [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533). När du går igenom denna SAP-anteckning uppifrån och ned visas flera områden med program vara och virtuella datorer som stöds

I det första avsnittet anges minimi kraven för operativ system som stöds med SAP-program i virtuella Azure-datorer i allmänhet. Om du inte når dessa minimi krav och kör äldre versioner av dessa operativ system måste du uppgradera din OS-version till en minimal version eller till och med nyare versioner. Det är rätt att Azure i allmänhet stöder äldre versioner av vissa av dessa operativ system. Men begränsningarna eller de lägsta versionerna som listas baseras på tester och kvalifikationer som körs och inte kommer att utökas ytterligare. 


> [!NOTE]
>Det finns vissa typer av virtuella datorer, HANA-stora instanser eller SAP-arbetsbelastningar som kommer att kräva fler nya operativ system versioner. Ärenden som anges i hela dokumentet. Ärenden som är tydligt dokumenterade antingen i SAP-anteckningar eller andra SAP-publikationer.

I avsnittet nedan visas de allmänna SAP-plattformarna som stöds med de versioner som stöds och viktiga SAP-kerneler som stöds. Den listar NetWeaver/ABAP eller Java-stackar som stöds och, vilket kräver minst kernel-versioner. Nyare ABAP-stackar stöds i Azure, men de behöver inte minsta kernel-versioner eftersom ändringar för Azure har implementerats från början av utvecklingen av de senaste stackarna

Du måste kontrol lera:

- Oavsett om de SAP-program som du kör omfattas av de lägsta versionerna som anges. Om inte, måste du definiera en ny mål version, titta i matrisen SAP Product Availability, vilka kombinationer av operativ system och DBMS stöds med den nya mål versionen. Så att du kan välja rätt version av operativ systemet och DBMS
- Om du behöver uppdatera dina SAP-kärnor i en flytt till Azure
- Om du behöver uppdatera SAP-stödpaketen. Särskilt grundläggande support paket som kan krävas för fall där du måste flytta till en senare DBMS-version


Nästa avsnitt går till mer information om andra SAP-produkter och DBMS-versioner som stöds av SAP på Azure för Windows och Linux. 

> [!NOTE]
> De minsta versionerna av olika DBMS väljs noga och kanske inte alltid återspeglar hela spektrumet av DBMS-versioner som har stöd för olika DBMS-leverantörer på Azure i allmänhet. Många SAP-relaterade överväganden vid arbets belastnings överväganden har beaktats för att definiera dessa minimi versioner. Det finns ingen ansträngning för att testa och kvalificera äldre DBMS-versioner. 

> [!NOTE]
> De lägsta versionerna i listan motsvarar äldre versioner av operativ system och databas versioner. Vi rekommenderar att du använder de senaste versionerna av operativ system och databas versioner. I många fall har fler operativ system och databas versioner använt sig av att köra i offentligt moln i beaktande och anpassad kod för att optimera för körning i offentligt moln eller mer specifikt Azure

## <a name="oracle-dbms-support"></a>Stöd för Oracle-DBMS
Operativ systemet, Oracle-DBMS-versioner och Oracle-funktioner som stöds i Azure anges särskilt i [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619). En del av denna anteckning kan sammanfattas som:

- Den minsta Oracle-version som stöds på virtuella Azure-datorer som är certifierade för NetWeaver är Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Som gäst operativ system är Windows och Oracle Linux kvalificerat. De exakta versionerna av operativ systemet och de relaterade minsta DBMS-versionerna visas i kommentaren
- Supporten för Oracle Linux utökas också till Oracle-DBMS-klienten. Det innebär att alla SAP-komponenter, t. ex. dialog instanser av ABAP-eller Java-stacken, måste köras på Oracle Linux också. Endast SAP-komponenter i ett sådant SAP-system som inte ansluter till Oracle-DBMS tillåts köra ett annat Linux-operativsystem
- Oracle RAC stöds inte 
- Oracle ASM stöds i vissa fall. Information visas i kommentaren
- SAP-system som inte stöder Unicode stöds bara med program servrar som kör med Windows gäst operativ system. Gäst operativ systemet för DBMS kan vara Oracle Linux eller Windows. Orsaken till den här begränsningen är uppenbar när du kontrollerar SAP-matrisen för produkt tillgänglighet (PAM). För Oracle Linux frisläppte SAP aldrig icke-Unicode SAP-kernel

Om du vet vilka DBMS-versioner som stöds med den riktade Azure-infrastrukturen måste du kontrol lera matrisen för tillgänglighet för SAP-produkter på om de OS-versioner och DBMS som krävs stöds med dina SAP-produktsortiment som du avsåg att köra. 

## <a name="oracle-linux"></a>Oracle Linux
De mest framträdande frågorna runt Oracle Linux är om SAP stöder Red Hat-kärnan som är integrerad del av Oracle Linux också. Mer information finns i [#1565179 för SAP-support](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Annan databas än SAP HANA
Stöd för icke-HANA-databaser för SAP-arbetsbelastningar dokumenteras i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533).


## <a name="sap-hana-support"></a>SAP HANA support
I Azure finns det två tjänster som kan användas för att köra HANA-databasen:

- Azure Virtual Machines
- [HANA stora instanser](./hana-overview-architecture.md)

För att kunna köra SAP HANA, har SAP mer och starkare villkor att infrastrukturen måste uppfylla för att kunna köra NetWeaver eller andra SAP-program och-DBMS. Det innebär att ett mindre antal virtuella Azure-datorer är kvalificerade för att köra SAP HANA-DBMS. En lista med Azure-infrastruktur som stöds för SAP HANA finns i den kallas [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Enheterna som börjar med bokstaven ' är Hana-enheter med [stora instanser](./hana-overview-architecture.md) . 

> [!NOTE]
> SAP har ingen särskild certifiering beroende av SAP HANA större versioner. I motsats till ett vanligt yttrande, är kolumnen **certifierings scenario** i de [Hana-certifierade IaaS-plattformarna](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)att det **inte finns någon information om den mest Hana-eller del versionen som certifierats av Hana**. Du måste anta att alla enheter i listan som kan användas för HANA 1,0 och HANA 2,0 så länge som certifierade operativ system versioner för de enskilda enheterna stöds av HANA 1,0-versioner. 

För användningen av SAP HANA kan olika lägsta OS-versioner gälla än för de allmänna NetWeaver-fallen. Du måste checka ut de operativ system som stöds för varje enhet, individuellt eftersom de kan variera. Du gör det genom att klicka på varje enhet. Mer information kommer att visas. En av de uppgifter som anges är de olika operativ system som stöds för den här enheten.

> [!NOTE]
> Azure HANA stor instans enheter är mer restriktiva med operativ system som stöds jämfört med virtuella Azure-datorer. Å andra sidan kan virtuella Azure-datorer upprätthålla nyare operativ system versioner som minimi krav. Detta gäller särskilt för några av de större VM-enheter som krävde ändringar av Linux-Kernels

Om du vet vilka operativ system som stöds för Azure-infrastrukturen måste du kontrol lera [#2235581 av SAP-supporten](https://launchpad.support.sap.com/#/notes/2235581) för de exakta SAP HANA-versionerna och korrigerings nivåer som stöds med de Azure-enheter som du riktar in dig på. 

> [!IMPORTANT]
> Steget för att kontrol lera de exakta SAP HANA-versionerna och de uppdaterings nivåer som stöds är mycket viktigt. I många fall är stöd för en viss OS-version beroende av en specifik korrigerings nivå för SAP HANA körbara filer.

När du känner till de specifika HANA-versioner som du kan köra på den aktuella Azure-infrastrukturen måste du checka in produkt tillgänglighets matrisen för SAP för att ta reda på om det finns några begränsningar med de SAP-produkt versioner som stöder HANA-utgåvor som du filtrerat ut


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certifierade virtuella Azure-datorer och HANA-stora instans enheter och affärs transaktions data flöde
Förutom att utvärdera de operativ Systems versioner som stöds, DBMS-versioner och beroende support SAP-programversioner för Azures infrastruktur enheter, har du behov av att kvalificera dessa enheter efter affärs transaktions data flöde, som uttrycks i enheten "SAP" av SAP. All SAP-storlek beror på SAPS-beräkningar. Utvärdering av befintliga SAP-system. du kan vanligt vis, med hjälp av infrastruktur leverantören, beräkna SAPS för enheterna. För DBMS-skiktet samt för program skiktet. I andra fall där nya funktioner skapas, kan en storleks övning med SAP avslöja de nödvändiga SAPS-numren för program lagret och DBMS-skiktet. Som infrastruktur leverantör Microsoft är skyldig att tillhandahålla SAP-genomflödet för de olika enheter som är antingen NetWeaver och/eller HANA-certifierade.

För virtuella Azure-datorer dokumenteras dessa SAPS data flödes nummer i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533). För Azure HANA stor instans-enheter dokumenteras SAPS data flödes nummer i [SAP support note #2316233](https://launchpad.support.sap.com/#/notes/2316233)

I [#1928533 för SAP-support noterar](https://launchpad.support.sap.com/#/notes/1928533)du följande anmärkningar:

- **För virtuella Azure-datorer i M-serien och Mv2-Series virtuella Azure-datorer gäller olika lägsta OS-versioner än för andra typer av virtuella Azure-datorer**. Kravet på fler nyare OS-versioner baseras på ändringar som de olika operativ system leverantörer måste ge i sina operativ system versioner för att antingen aktivera sina operativ system på de olika typerna av virtuella Azure-datorer eller optimera prestanda och data flöde för SAP-arbetsbelastningar på dessa VM-typer
- Det finns två tabeller som anger olika typer av virtuella datorer. Den andra tabellen anger SAPS-dataflöde för Azure VM-typer som endast stöder Azure standard Storage. DBMS-distribution i de enheter som anges i den andra tabellen av anteckningen stöds inte


## <a name="other-sap-products-supported-on-azure"></a>Andra SAP-produkter som stöds i Azure
I allmänhet är antagandet att med läget för storskaliga moln som Azure, så bör de flesta av SAP-program köras utan att fungera i Azure. Men i motsats till visualisering av privata moln, uttrycker SAP fortfarande stöd för de olika SAP-produkterna explicit för de olika hyerpscale moln leverantörer. Därför finns det olika stöd anteckningar för SAP som visar stöd för Azure för olika SAP-produkter. 

[SAP-supporten #2145537](https://launchpad.support.sap.com/#/notes/2145537) innehåller en lista över SAP Business Objects-produkter som stöds i Azure för Business Objects BI-plattformen. Om det finns frågor kring komponenter eller kombinationer av program varu versioner och OS-versioner som inte verkar vara listade eller stöds och som är nyare än de lägsta versionerna som anges i listan, måste du öppna en SAP-supportbegäran mot den komponent som du frågar support för.

För Business Objects Data Services [#22288344 för SAP-supporten](https://launchpad.support.sap.com/#/notes/2288344) en förklaring av det lägsta stödet för SAP Data Services som körs på Azure. 

> [!NOTE]
> Som du ser i stöd för SAP-support måste du checka in SAP PAM för att identifiera rätt support paket nivå som ska stödjas på Azure

Stöd för SAP Datahub/Vora i Azure Kubernetes Services (AKS) beskrivs i [SAP support note #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Stöd för SAP BPC 10,1 SP08 beskrivs i [SAP support note #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Stöd för SAP Hybris Commerce Platform på Azure beskrivs i Hybris- [dokumentationen](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Från och med stöd för DBMS för SAP Hybris Commerce-plattformen visas den som:

- SQL Server och Oracle på plattformen för operativ systemet Windows. Samma minimi versioner gäller för SAP NetWeaver. Mer information finns i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA för Red Hat och SUSE Linux. SAP HANA certifierade VM-typer krävs enligt beskrivningen tidigare i [det här dokumentet](#sap-hana-support). SAP (Hybris) Commerce Platform anses vara OLTP-arbetsbelastning
- SQL Azure DB från och med SAP (Hybris) Commerce Platform version 1811




## <a name="next-steps"></a>Nästa steg
Läs nästa steg i [Azure Virtual Machines planera och implementera för SAP NetWeaver](./planning-guide.md)
