---
title: Känna till villkoren för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Känna till villkoren för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719087"
---
# <a name="know-the-terms"></a>Förstå villkoren

Flera vanliga definitioner används ofta i guiden för arkitektur och teknisk distribution. Observera följande termer och deras innebörd:

- **IaaS:** Infrastruktur som en tjänst.
- **PaaS:** Plattform som en tjänst.
- **SaaS:** Programvara som en tjänst.
- **SAP-komponent:** Ett enskilt SAP-program, till exempel ERP Central Component (ECC), Business Warehouse (BW), Solution Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP- eller Java-tekniker eller ett icke-NetWeaver-baserat program, till exempel affärsobjekt.
- **SAP-miljö:** En eller flera SAP-komponenter som grupperas logiskt för att utföra en affärsfunktion, till exempel utveckling, kvalitetskontroll, utbildning, haveriberedskap eller produktion.
- **SAP-landskap:** Refererar till hela SAP-tillgångarna i ditt IT-landskap. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
- **SAP-system:** Kombinationen av DBMS-lager och programlager för, till exempel ett SAP ERP-utvecklingssystem, ett SAP BW-testsystem och ett SAP CRM-produktionssystem. Azure-distributioner har inte stöd för att dela upp dessa två lager mellan den lokala platsen och Azure. Ett SAP-system distribueras antingen lokalt eller distribueras i Azure. Du kan distribuera de olika systemen i ett SAP-landskap till antingen Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings- och testsystemen i Azure medan du distribuerar SAP CRM-produktionssystemet lokalt. För SAP HANA på Azure (stora instanser) är det tänkt att du ska vara värd för SAP-programlagret för SAP-system på virtuella datorer och den relaterade SAP HANA-instansen på en enhet i stämpeln SAP HANA på Azure (stora instanser).
- **Stämpel för stor** instans: En maskinvaruinfrastrukturstack som är SAP HANA TDI-certifierad och dedikerad för att SAP HANA instanser i Azure.
- **SAP HANA på Azure (stora instanser):** Det officiella namnet på erbjudandet i Azure för att köra HANA-instanser i på SAP HANA TDI-certifierad maskinvara som distribueras i stora instansstämplar i olika Azure-regioner. Den relaterade termen *hana stor instans* är en *förkortning SAP HANA azure (stora instanser)* och används ofta i den här tekniska distributionsguiden.
- **Flera platser:** Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, multiplats- eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. I den vanliga Azure-dokumentationen beskrivs även dessa typer av distributioner som scenarier över flera platser. Orsaken till anslutningen är att utöka lokala domäner, lokala domäner Azure Active Directory/OpenLDAP och lokal DNS till Azure. Det lokala landskapslandskapet utökas till Azure-tillgångarna i Azure-prenumerationerna. Med det här tillägget kan de virtuella datorerna ingå i den lokala domänen. 

   Domänanvändare av den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer (till exempel DBMS-tjänster). Kommunikation och namnmatchning mellan virtuella datorer som distribuerats lokalt och Azure-distribuerade virtuella datorer är möjligt. Det här scenariot är typiskt för hur de flesta SAP-tillgångar distribueras. Mer information finns i [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) och Skapa ett virtuellt nätverk med en [plats-till-plats-anslutning](../../../vpn-gateway/tutorial-site-to-site-portal.md)med hjälp av Azure Portal .
- **Klientorganisation:** En kund som distribueras i stämpeln HANA Large Instance isoleras till en *klientorganisation.* En klientorganisation är isolerad i nätverks-, lagrings- och beräkningslagret från andra klienter. Lagrings- och beräkningsenheter som är tilldelade till de olika klienterna kan inte se varandra eller kommunicera med varandra på stämpelnivån hana stor instans. En kund kan välja att ha distributioner till olika klienter. Inte ens då finns det någon kommunikation mellan klienter på stämpelnivån HANA Large Instance.
- **SKU-kategori:** För hana, stor instans erbjuds följande två kategorier av SKU:er:
    - **Typ I-klass:** S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224 m
    - **Typ II-klass:** S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m
- **Stämpel:** Definierar Microsofts interna distributionsstorlek för hana stora instanser. Innan stora HANA-instansenheter kan distribueras måste en stämpel för hana stor instans som består av beräknings-, nätverks- och lagringsrack distribueras på en datacenterplats. En sådan distribution kallas för en stor HANA-instansstämpel eller från Revision 4 (se nedan) på vi använder den alternativa termen **för raden för stora instanser**
- **Revision:** Det finns två olika stämpelrevisioner för hana stora instansstämplar. Dessa skiljer sig åt vad gäller arkitektur och närhet till värdar för virtuella Azure-datorer.
    - "Revision 3" (Rev 3) är den ursprungliga designen som distribuerades från mitten av 2016.
    - "Revision 4.2" (Rev 4.2) är en ny design som ger närmare anslutning till virtuella Azure-datorvärdar. Rev 4.2 erbjuder extremt låg nätverksfördröjning mellan virtuella Azure-datorer och HANA large Instance-enheter. Resurser i Azure Portal kallas BareMetal-infrastruktur. Kunder kan komma åt sina resurser som BareMetal-instanser från Azure Portal. 

Det finns en mängd ytterligare resurser för hur du distribuerar en SAP-arbetsbelastning i molnet. Om du planerar att köra en distribution av SAP HANA i Azure måste du ha erfarenhet av och känna till principerna för Azure IaaS och distributionen av SAP-arbetsbelastningar i Azure IaaS. Mer information finns i Använda [SAP-lösningar på virtuella Azure-datorer](get-started.md) innan du fortsätter. 

## <a name="next-steps"></a>Nästa steg
- Se [HLI-certifiering](hana-certification.md).