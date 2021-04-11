---
title: Självstudie – distribuera och konfigurera VMware HCX
description: Lär dig hur du distribuerar och konfigurerar en VMware HCX-lösning för ditt privata moln i Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9ee451ce4be59c51299d66e4042ed344026100cf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011008"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuera och konfigurera VMware HCX

Den här artikeln visar hur du distribuerar och konfigurerar den lokala VMware HCX-anslutningen för ditt privata moln i Azure VMware-lösningen. Med VMware HCX kan du migrera dina VMware-arbetsbelastningar till Azure VMware-lösningen och andra anslutna platser via olika typer av migrering. Eftersom Azure VMware-lösningen distribuerar och konfigurerar HCX Cloud Manager måste du ladda ned, aktivera och konfigurera HCX-anslutningen i ditt lokala VMware-datacenter.

VMware HCX Advanced Connector förinstalleras i Azure VMware-lösningen. Det har stöd för upp till tre plats anslutningar (lokalt till molnet eller molnet till molnet). Om du behöver fler än tre plats anslutningar skickar du en [supportbegäran](https://portal.azure.com/#create/Microsoft.Support) för att aktivera [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) -tillägget.  

>[!TIP]
>Även om verktyget för maximala VMware-konfiguration beskriver plats par maximalt 25 mellan den lokala anslutningen och moln hanteraren, begränsar licensen detta till tre för HCX Advanced och 10 för HCX Enterprise Edition.

>[!NOTE]
>VMware HCX Enterprise är tillgängligt med Azure VMware-lösningen som en för hands versions tjänst. Det är kostnads fritt och omfattas av villkor för en för hands versions tjänst. När VMware HCX Enterprise-tjänsten är allmänt tillgänglig får du ett meddelande om 30 dagar på att faktureringen ska växlas över. Du kan också välja att stänga av eller avanmäla tjänsten. Nedgradering från HCx Enterprise till HCX Advanced är möjlig utan att omdistribueras, men du måste logga ett support ärende för att åtgärden ska äga rum. Om du planerar en nedgradering bör du se till att inga migreringar är schemalagda och eller funktioner som RAV, mån inte används.

Börja med att granska [innan du börjar](#before-you-begin), [program varu versions krav och kraven](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) [.](#prerequisites) 

Sedan kommer vi att gå igenom alla nödvändiga procedurer för att:

> [!div class="checklist"]
> * Hämta VMware HCX Connector-ägg.
> * Distribuera lokal VMware HCX-ägg (VMware HCX-anslutning).
> * Aktivera VMware HCX-anslutningen.
> * Para ihop din lokala VMware HCX-anslutning med din Azure VMware-lösning HCX Cloud Manager.
> * Konfigurera Interconnect (nätverks profil, beräknings profil och service nät).
> * Slutför installationen genom att kontrol lera statusen för installationen och verifiera att migreringen är möjlig.

När du är klar följer du de rekommenderade nästa stegen i slutet av den här artikeln.  

## <a name="before-you-begin"></a>Innan du börjar

När du förbereder distributionen rekommenderar vi att du läser följande VMware-dokumentation:

* [Användar guide för VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Distributions överväganden för VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware-blogg serie – molnbaserad migrering](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Nätverks portar som krävs för VMware-HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Förutsättningar

Om du planerar att använda VMware HCX Enterprise bör du kontrol lera att du har begärt aktivering via Azure VMware Solution support Channels.


### <a name="on-premises-vsphere-environment"></a>Lokal vSphere-miljö

Se till att din lokala vSphere-miljö (käll miljö) uppfyller [minimi kraven](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Nätverk och portar

* [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) konfigureras mellan lokala och Azure VMware-lösningar privata moln ExpressRoute-kretsar.

* [Alla portar som krävs](https://ports.vmware.com/home/VMware-HCX) är öppna för kommunikation mellan lokala komponenter och Azure VMware-lösningen privat.

### <a name="ip-addresses"></a>IP-adresser

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Hämta VMware HCX Connector-ägg

Innan du distribuerar den virtuella installationen till din lokala vCenter måste du ladda ned VMware HCX Connector-ägg.  

1. I Azure Portal väljer du det privata molnet Azure VMware-lösning. 

1. Välj **Hantera**  >  **anslutning** och välj fliken **HCX** för att identifiera Azure VMware-lösningens HCX-hanterarens IP-adress. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Skärm bild av VMware HCX-IP-adressen." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Välj **Hantera**  >  **identitet**. 

   URL: er och användarautentiseringsuppgifter för privat moln vCenter och NSX-T Manager-visning.

   > [!TIP]
   > VCenter-lösenordet definierades när du konfigurerade det privata molnet. Det är samma lösen ord som du använder för att logga in på Azure VMware Solution HCX Manager. Du kan välja **skapa ett nytt lösen ord** för att generera nya vCenter-och NSX-lösenord.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visa URL: er och autentiseringsuppgifter för privata moln-vCenter och NSX Manager." border="true":::


1. Öppna ett webbläsarfönster och logga in på Azure VMware Solution HCX Manager på `https://x.x.x.9` port 443 med **cloudadmin \@ vSphere.** autentiseringsuppgifter för lokal användare

1. Välj **administrations**  >  **System uppdateringar** och välj sedan **begär nedladdnings länk**.

1. Välj alternativ för att ladda ned VMware HCX Connector-ägg filen.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Distribuera VMware HCX Connector-ägg lokalt

1. I din lokala vCenter väljer du en OVF- [mall](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) för att distribuera VMware HCX-anslutningen till din lokala vCenter. 

   > [!TIP]
   > Du väljer den ägg filen som du laddade ned i föregående avsnitt.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Välj ett namn och en plats och välj en resurs eller ett kluster där du distribuerar VMware HCX-anslutningen. Granska sedan informationen och de resurser som krävs och välj **Nästa**.  

1. Läs igenom licens villkoren. Om du samtycker väljer du önskad lagring och nätverk och väljer sedan **Nästa**.

1. Välj lagring och välj **Nästa**.

1. Välj det nätverks segment för VMware-HCX som du definierade i avsnittet [krav för IP-adresser](#ip-addresses) .  Välj **Nästa**.

1. I **Anpassa mall** anger du all nödvändig information och väljer sedan **Nästa**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Skärm bild av rutorna för att anpassa en mall." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Verifiera konfigurationen och välj sedan **Slutför** för att distribuera VMware HCX Connector-ägg.
   
   > [!IMPORTANT]
   > Du måste aktivera den virtuella enheten manuellt.  Efter strömförsörjningen väntar du 10-15 minuter innan du fortsätter till nästa steg.

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX installation](https://www.youtube.com/embed/UKmSTYrL6AY) av enhets distribution. 


## <a name="activate-vmware-hcx"></a>Aktivera VMware-HCX

När du har distribuerat VMware HCX Connector-dataäggen lokalt och startar installationen, är du redo att aktivera. Först måste du skaffa en licens nyckel från Azure VMware Solution Portal.

1. I Azure VMware Solution portal går du till **Hantera**  >  **anslutning**, väljer fliken **HCX** och väljer sedan **Lägg till**.

1. Använd **Administratörs** behörighet för att logga in på den lokala VMware HCX Manager på `https://HCXManagerIP:9443` . 

   > [!TIP]
   > Du definierade **Administratörs** användarens lösen ord under fil distributionen VMware HCX Manager.

   > [!IMPORTANT]
   > Se till att ta `9443` med port numret med VMware HCX Manager-IP-adressen.

1. I **licensiering** anger du nyckeln för **HCX Advanced Key** och väljer **Aktivera**.  
   
    > [!NOTE]
    > VMware HCX Manager måste ha öppen Internet åtkomst eller en proxy konfigurerad.

1. På **Data Center plats** anger du den närmaste platsen för att installera VMware HCX Manager lokalt. Välj sedan **Fortsätt**.

1. I **system namn** ändrar du namnet eller accepterar standardvärdet och väljer **Fortsätt**.
   
1. Välj **Ja, Fortsätt**.

1. I **Anslut din vCenter**, anger du FQDN-namnet eller IP-adressen för vCenter-servern och rätt autentiseringsuppgifter och väljer sedan **Fortsätt**.
   
   > [!TIP]
   > VCenter-servern är den plats där du distribuerar VMware HCX-anslutaren i ditt data Center.

1. I **Konfigurera SSO/PSC** anger du FQDN eller IP-adressen för din plattforms tjänst kontroll och väljer sedan **Fortsätt**.
   
   > [!NOTE]
   > Vanligt vis är det samma som vCenter-FQDN eller IP-adress.

1. Kontrol lera att den angivna informationen är korrekt och välj **starta om**.
    
   > [!NOTE]
   > Du får en fördröjning efter omstarten innan du uppmanas att ange nästa steg.

När tjänsterna har startats om visas vCenter som visas som grönt på skärmen som visas. Både vCenter och SSO måste ha lämpliga konfigurations parametrar, vilket bör vara samma som föregående skärm.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Skärm bild av instrument panelen med grön vCenter-status." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

En översikt över slut punkt till slut punkt för den här proceduren finns i [Azure VMware-lösningen: Aktivera HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) video.

   > [!IMPORTANT]
   > Oavsett om du använder HCX Advanced eller HCX Enterprise kan du behöva installera korrigeringen från VMwares KB- [artikel 81558](https://kb.vmware.com/s/article/81558). 

## <a name="configure-the-vmware-hcx-connector"></a>Konfigurera VMware HCX-anslutningen

Nu är du redo att lägga till en webbplats koppling, skapa en nätverks-och beräknings profil och aktivera tjänster som migrering, nätverks tillägg eller haveri beredskap. 

### <a name="add-a-site-pairing"></a>Lägg till en webbplats ihopparning

Du kan ansluta eller para ihop VMware HCX Cloud Manager i Azure VMware-lösningen med VMware HCX-anslutningsprogrammet i ditt data Center. 

1. Logga in på din lokala vCenter och välj **HCX** under **Start**.

1. Under **infrastruktur** väljer du **plats par** och väljer sedan alternativet **Anslut till fjärrplats** (i mitten av skärmen). 

1. Ange den Azure VMware-lösning HCX Cloud Manager-URL eller IP-adress som du noterade tidigare `https://x.x.x.9` , Azure VMware-lösningen cloudadmin \@ vSphere. local användar namn och lösen ordet. Välj **Anslut**.

   > [!NOTE]
   > För att kunna upprätta ett plats par:
   > * Din VMware HCX-anslutning måste kunna dirigera till din HCX Cloud Manager-IP via port 443.
   >
   > * Använd samma lösen ord som du använde för att logga in på vCenter. Du definierade det här lösen ordet på den första distributions skärmen.

   Du ser en skärm som visar att din VMware HCX Cloud Manager i Azure VMware-lösningen och din lokala VMware HCX-anslutning är ansluten (länkad).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Skärm bild som visar länkning av HCX Manager i Azure VMware-lösning och VMware HCX-anslutningen.":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX site ihopparning](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) video.

### <a name="create-network-profiles"></a>Skapa nätverks profiler

VMware HCX-anslutningen distribuerar en delmängd av virtuella apparater (automatisk) som kräver flera IP-segment. När du skapar nätverks profiler använder du de IP-segment som du identifierade under [förberedelse och planering av VMware HCX-nätverks segment för distribution](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Du skapar fyra nätverks profiler:

   - Hantering
   - vMotion
   - Replikering
   - Ordna

1. Under **infrastruktur** väljer du **Interconnect**  >  nätverks profiler för **multipl-site service nät**  >    >  **skapa nätverks profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Skärm bild av val för att börja skapa en nätverks profil." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. För varje nätverks profil väljer du nätverket och port gruppen, anger ett namn och skapar segmentets IP-pool. Välj sedan **Skapa**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Skärm bild av information om en ny nätverks profil.":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX Network Profile](https://www.youtube.com/embed/O0rU4jtXUxc) video.


### <a name="create-a-compute-profile"></a>Skapa en beräknings profil

1. Under **infrastruktur** väljer du **Interconnect**  >  **Compute-profiler**  >  **skapa beräknings profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Skärm bild som visar alternativen för att börja skapa en beräknings profil." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Ange ett namn för profilen och välj **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Skärm bild som visar posten för ett beräknings profil namn och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Välj de tjänster som ska aktive ras, till exempel migrering, nätverks tillägg eller haveri beredskap, och välj sedan **Fortsätt**.
  
   > [!NOTE]
   > Normalt ändras ingenting här.

1. I **Välj tjänst resurser** väljer du en eller flera tjänst resurser (kluster) för att aktivera de valda VMware HCX-tjänsterna.  

1. När du ser kluster i ditt lokala data Center väljer du **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Skärm bild som visar valda tjänst resurser och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Välj data lager lagrings resurs för att distribuera VMware HCX Interconnect-enheter från **Välj data lager**. Välj sedan **Fortsätt**.

   När flera resurser är markerade använder VMware HCX den första resursen som väljs tills dess kapacitet är slut.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Skärm bild som visar en vald data lagrings resurs och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Välj den nätverks profil för hantering som du skapade i föregående steg i **Välj hanterings nätverks profil**. Välj sedan **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Skärm bild som visar valet av en hanterings nätverks profil och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Välj den nätverks profil för överordnad länk som du skapade i föregående procedur från **Välj Nätverks profil för överordnad länk**. Välj sedan **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Skärm bild som visar valet av en nätverks profil för överordnad länk och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Från **Välj vMotion nätverks profil** väljer du nätverks profilen vMotion som du skapade i föregående steg. Välj sedan **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Skärm bild som visar valet av en vMotion nätverks profil och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Välj den nätverks profil för replikering som du skapade i föregående steg från **Välj vSphere-replikeringsgruppens nätverks profil**. Välj sedan **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Skärm bild som visar valet av en nätverks profil för replikering och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Från **Välj distribuerade växlar för nätverks tillägg** väljer du de växlar som innehåller de virtuella datorer som ska migreras till Azure VMware-lösningen i ett utökat nätverk på nivå 2. Välj sedan **Fortsätt**.

   > [!NOTE]
   > Om du inte migrerar virtuella datorer i Layer-2 (L2) utökade nätverk kan du hoppa över det här steget.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Skärm bild som visar valet av distribuerade virtuella växlar och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Granska anslutnings reglerna och välj **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Skärm bild som visar anslutnings reglerna och knappen Fortsätt." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Klicka på **Slutför** för att skapa beräknings profilen.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Skärm bild som visar information om beräknings profil." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: Compute Profile](https://www.youtube.com/embed/e02hsChI3b8) video.

### <a name="create-a-service-mesh"></a>Skapa ett service nät

Nu är det dags att konfigurera ett service nät mellan lokalt och privat moln i Azure VMware-lösningen.



> [!NOTE]
> Så här upprättar du ett service nät med Azure VMware-lösningen:
>
> * Portarna UDP 500/4500 är öppna mellan nätverks profil adresserna för din lokala VMware HCX-anslutning och Azure VMware-lösningen HCX molnets nätverks profil adresser.
>
> * Se till att granska de [VMware HCX-portar som krävs](https://ports.vmware.com/home/VMware-HCX).

1. Under **infrastruktur** väljer du **Interconnect**  >  **service-nät**  >  **skapa tjänst nät**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Skärm bild av val för att börja skapa ett service nät." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Granska de platser som är ifyllda och välj sedan **Fortsätt**. 

   > [!NOTE]
   > Om det här är din första tjänst nät konfiguration behöver du inte ändra den här skärmen.  

1. Välj käll-och fjärrberäknings profiler i list rutorna och välj sedan **Fortsätt**.  

   Valen definierar de resurser där de virtuella datorerna kan använda VMware HCX-tjänster.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Skärm bild som visar val av käll beräknings profil." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Skärm bild som visar val av fjärran sluten beräknings profil." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Granska tjänster som kommer att aktive ras och välj sedan **Fortsätt**.  

1. I **Avancerad konfiguration – Åsidosätt överordnad överordnad nätverks profil**, väljer du **Fortsätt**.  

   Nätverks profiler för överordnad länk ansluter till nätverket genom vilket fjärrplatsens sammanlänknings utrustning kan nås.  
  
1. I **Avancerad konfiguration – nätverks tilläggets utrustning skala ut**, granska och välj **Fortsätt**. 

   Du kan ha upp till åtta VLAN per enhet, men du kan distribuera en annan installation för att lägga till ytterligare åtta VLAN. Du måste också ha ett IP-utrymme för att kunna använda ytterligare enheter, och det är en IP-adress per apparat.  Mer information finns i [konfigurations gränser för VMware-HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Skärm bild som visar var du kan öka antalet VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. I **Avancerad konfiguration – trafik teknik**, granskar och gör du nödvändiga ändringar och väljer sedan **Fortsätt**.

1. Granska förhands granskning av topologi och välj **Fortsätt**.

1. Ange ett användarvänligt namn för det här Service nätet och välj **Slutför** för att slutföra.  

1. Välj **Visa aktiviteter** för att övervaka distributionen. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Skärm bild som visar knappen för att visa aktiviteter.":::

   När service nät distributionen har slutförts visas tjänsterna som gröna.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Skärm bild som visar gröna indikatorer på tjänster." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Kontrol lera tjänstens nät hälsa genom att kontrol lera status för enheten. 

1. Välj **Interconnect**-  >  **enheter**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Skärm bild som visar val för att kontrol lera status för enheten." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

En översikt över slut punkt till slut punkt för den här proceduren finns i [Azure VMware-lösningen: service nät-](https://www.youtube.com/embed/COY3oIws108) video.

### <a name="optional-create-a-network-extension"></a>Valfritt Skapa ett nätverks tillägg

Följ dessa steg om du vill utöka alla nätverk från din lokala miljö till Azure VMware-lösningen:

1. Under **tjänster** väljer du **nätverks tillägg**  >  **skapa ett nätverks tillägg**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Skärm bild som visar alternativ för att börja skapa ett nätverks tillägg." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Välj vart och ett av de nätverk som du vill utöka till Azure VMware-lösningen och välj sedan **Nästa**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Skärm bild som visar valet av nätverk.":::

1. Ange den lokala gateway-IP: en för vart och ett av de nätverk som du vill utöka och välj sedan **Skicka**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Skärm bild som visar posten för en gateway-IP-adress.":::

   Det tar några minuter för nätverks tillägget att slutföras. När du gör det visas status ändringen till **tillägget slutförd**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Skärm bild som visar statusen för tillägget slutfört." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: Network Extension](https://www.youtube.com/embed/gYR0nftKui0) video.


## <a name="next-steps"></a>Nästa steg

Om HCX Interconnect-tunnelns status är **upp** och grön, kan du migrera och skydda virtuella datorer i Azure VMware-lösningen med hjälp av VMware HCX. Azure VMware-lösningen stöder migrering av arbets belastning (med eller utan ett nätverks tillägg). Du kan fortfarande migrera arbets belastningar i din vSphere-miljö, tillsammans med lokala skapande av nätverk och distribution av virtuella datorer till dessa nätverk.  

Mer information om hur du använder HCX finns i den tekniska dokumentationen för VMware:

* [VMware HCX-dokumentation](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX nödvändiga portar](https://ports.vmware.com/home/VMware-HCX)
* [Konfigurera en HCX proxyserver innan du godkänner licens nyckeln](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
