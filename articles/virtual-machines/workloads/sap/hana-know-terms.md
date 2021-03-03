---
title: Lär dig mer om villkoren i SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Lär dig mer om villkoren i SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ef2e59dab1921eae8e7d88249e75116601c597
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670866"
---
# <a name="know-the-terms"></a>Förstå villkoren

Flera vanliga definitioner används ofta i arkitektur-och teknisk distributions guide. Observera följande villkor och deras betydelser:

- **IaaS**: infrastruktur som en tjänst.
- **PaaS**: plattform som en tjänst.
- **SaaS**: program vara som en tjänst.
- **SAP-komponent**: ett enskilt SAP-program, t. ex. en ERP central komponent (ECC), Business WAREHOUSE (BW), lösnings ansvarig eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP-eller Java-tekniker eller icke-NetWeaver-baserade program som affärs objekt.
- **SAP-miljö**: en eller flera SAP-komponenter grupperade logiskt för att utföra en affärs funktion, till exempel utveckling, kvalitets säkring, utbildning, haveri beredskap eller produktion.
- **SAP, liggande**: refererar till hela SAP-resurserna i din IT-liggande. SAP-landskapet innehåller alla produktions miljöer och icke-produktions miljöer.
- **SAP-system**: kombinationen av DBMS-skikt och program nivå i, till exempel ett SAP ERP-utvecklingssystem, ett SAP BW test system och ett SAP CRM-produktionssystem. Azure-distributioner stöder inte uppdelning av de här två lagren mellan både lokala och Azure. Ett SAP-system är antingen distribuerat lokalt eller distribuerat i Azure. Du kan distribuera de olika systemen i ett SAP-landskap till antingen Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings-och test systemen i Azure samtidigt som du distribuerar SAP CRM-operativsystemet lokalt. För SAP HANA på Azure (stora instanser) är det avsett att du är värd för SAP-program skiktet i SAP-system i virtuella datorer och relaterad SAP HANA instansen på en enhet i stämpeln SAP HANA på Azure (stora instanser).
- **Stor instans stämpel**: en maskin varu infrastrukturs stack som är SAP HANA TDI-certifierad och dedikerad för att köra SAP HANA-instanser i Azure.
- **SAP HANA på Azure (stora instanser):** Officiellt namn på erbjudandet i Azure att köra HANA-instanser i på SAP HANA TDI-certifierad maskin vara som distribueras i stora instanser i olika Azure-regioner. Den relaterade termen *Hana-stor instans* är kort för *SAP HANA på Azure (stora instanser)* och används ofta i den här tekniska distributions guiden.
- **Olika platser**: beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, flera-plats-eller Azure ExpressRoute-anslutning mellan lokala data Center och Azure. I gemensam Azure-dokumentation beskrivs även de här typerna av distributioner som scenarier med olika platser. Orsaken till anslutningen är att utöka lokala domäner, lokala Azure Active Directory/OpenLDAP och lokala DNS i Azure. Den lokala miljön är utökad till Azure-till gångar i Azure-prenumerationerna. Med det här tillägget kan de virtuella datorerna ingå i den lokala domänen. 

   Domän användare av den lokala domänen kan komma åt servrarna och köra tjänster på de virtuella datorerna (till exempel DBMS-tjänster). Kommunikation och namn matchning mellan virtuella datorer som distribuerats lokalt och Azure-distribuerade virtuella datorer är möjligt. Det här scenariot är vanligt för det sätt på vilket de flesta SAP-till gångar distribueras. Mer information finns i [Azure VPN gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) och [skapa ett virtuellt nätverk med en plats-till-plats-anslutning med hjälp av Azure Portal](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Klient organisation**: en kund som distribueras i Hana stor instans-stämpel isoleras till en *klient.* En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på nivån HANA-stor instans stämpel. En kund kan välja att distribuera olika klienter. Även om det inte finns någon kommunikation mellan klienter på nivån HANA stor instans stämpel.
- **SKU-kategori**: för en stor instans av Hana erbjuds följande två kategorier av SKU: er:
    - **Typ I-klass**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m
    - **Typ II-klass**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m
- **Stamp**: definierar den interna distributions storleken på Hana för stora instanser i Microsoft. Innan HANA-stora instans enheter kan distribueras, består en stor instans av HANA som består av beräknings-, nätverks-och lagrings rack som måste distribueras på en plats i data centret. En sådan distribution kallas för en HANA-stor instans eller från revision 4 (se nedan) på vi använder det alternativa villkoret för **stor instans rad**
- **Revision**: det finns två olika stämplar revisioner för Hana-stora instans stämplar. Dessa skiljer sig från arkitekturen och närhet till Azures värdar för virtuella datorer
    - "Revision 3" (rev 3): är den ursprungliga design som har distribuerats från mitten av året 2016
    - "Revision 4" (rev 4): är en ny design som kan ge närmare närhet till värdar för virtuella Azure-datorer och med den lägre nätverks fördröjningen mellan virtuella Azure-datorer och HANA stora instans enheter 
    - "Revision 4,2" (rev 4,2): på befintliga revision 4-domänkontrollanter ommärkess resurserna till BareMetal-infrastrukturen.  Kunder kan komma åt sina resurser som BareMetal-instanser från Azure Portal. 

Det finns flera olika ytterligare resurser som du kan använda för att distribuera en SAP-arbetsbelastning i molnet. Om du planerar att köra en distribution av SAP HANA i Azure måste du ha erfarenhet av och känna till principerna för Azure-IaaS och distributionen av SAP-arbetsbelastningar på Azure IaaS. Innan du fortsätter kan du läsa [Använd SAP-lösningar på Azure Virtual Machines](get-started.md) för mer information. 

**Nästa steg**
- Se [HLI-certifiering](hana-certification.md)