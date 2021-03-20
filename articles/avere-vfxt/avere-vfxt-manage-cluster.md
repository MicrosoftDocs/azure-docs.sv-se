---
title: Hantera ditt AVERT vFXT-kluster – Azure
description: Hantera AVERT kluster – lägga till eller ta bort noder, starta om, stoppa eller förstöra vFXT-klustret
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505502"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Hantera Avere vFXT-klustret

Vid någon tidpunkt i livs cykeln för ditt AVERT vFXT för Azure-kluster kan du behöva lägga till klusternoder eller starta eller starta om klustret. När projektet är klart behöver du veta hur du stoppar klustret och tar bort det permanent.

Den här artikeln förklarar hur du lägger till eller tar bort klusternoder och andra grundläggande kluster åtgärder. Om du behöver ändra kluster inställningarna eller övervaka sitt arbete använder du [kontroll panelen aver](avere-vfxt-cluster-gui.md).

Beroende på hanterings aktiviteten kan du behöva använda en av tre olika verktyg: AVERT kontroll panel, vfxt.py kommando rads kluster för kluster hantering och Azure Portal.

Den här tabellen ger en översikt över vilka verktyg som kan användas för varje uppgift.

| Action | AVERT kontroll panel | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Lägga till klusternoder | nej | ja | nej |
| Ta bort klusternoder | ja | nej | nej |
| Stoppa en klusternod | Ja (kan också starta om tjänster eller starta om) | nej | att stänga av en virtuell Node-dator från portalen tolkas som ett nodfel |
| Starta en stoppad nod | nej | nej | ja |
| Förstör en enskild klusternod | nej | nej | ja |
| Starta om klustret |  |  |  |
| Stänga av eller stoppa klustret på ett säkert sätt | ja | ja | nej |
| Förstör klustret  | nej | ja | Ja, men data integriteten är inte garanterad |

Detaljerade instruktioner för varje verktyg finns nedan.

## <a name="about-stopped-instances-in-azure"></a>Om stoppade instanser i Azure

När du stänger av eller stoppar en virtuell Azure-dator slutar den att debiteras för beräknings kostnader, men du måste fortfarande betala för lagringen. Om du stänger av en vFXT-nod eller hela vFXT-klustret och du inte tänker starta om den, bör du använda Azure Portal för att ta bort de relaterade virtuella datorerna.

I Azure Portal visar en *stoppad* nod (som kan startas om) statusen **stoppad** i Azure Portal. En *borttagen* nod visar statusen **stoppad (Frigjord)** och den tar inte längre betalt för beräknings-eller lagrings kostnader.

Innan du tar bort den virtuella datorn måste du kontrol lera att alla ändrade data har skrivits från cacheminnet till backend-lagring med hjälp av alternativen aver-kontroll panel eller vfxt.py för att stoppa eller stänga av klustret.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Hantera klustret med aver på kontroll panelen

Kontroll panelen aver kan användas för följande uppgifter:

* Stoppa eller starta om enskilda noder
* Ta bort en nod från klustret
* Stoppa eller starta om hela klustret

Aver på kontroll panelen prioriterar data integritet, så att det försöker skriva alla ändrade data till backend-lagringen innan en eventuell destruktiv åtgärd. Detta gör det ett säkrare alternativ än Azure Portal.

Åtkomst till AVERT kontroll panel från en webbläsare. Följ instruktionerna i [komma åt vFXT-klustret](avere-vfxt-cluster-gui.md) om du behöver hjälp.

### <a name="manage-nodes-with-avere-control-panel"></a>Hantera noder med AVERT kontroll panel

Sidan Inställningar för **FXT-noder** har kontroller för att hantera enskilda noder.

Om du vill stänga av, starta om eller ta bort en nod, letar du upp noden i listan på sidan **FXT noder** och klickar på motsvarande knapp i kolumnen **åtgärder** .

> [!NOTE]
> IP-adresser kan flyttas mellan klusternoder när antalet aktiva noder ändras.

Läs [kluster > FXT-noder](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) i guiden för kluster inställningar i aver-klustret för mer information.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Stoppa eller starta om klustret med aver kontroll panel

Sidan **system underhålls** inställningar innehåller kommandon för att starta om kluster tjänster, starta om klustret eller på ett säkert sätt stänga av klustret. Mer information finns i [administrations > system underhåll](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (i guiden aver kluster inställningar).

När ett kluster börjar stängas, skickar det tillstånds meddelanden till fliken **instrument panel** . Efter en liten stund slutar meddelandena och slutligen att den Avera kontroll panels sessionen slutar svara, vilket innebär att klustret har stängts av.

## <a name="manage-the-cluster-with-vfxtpy"></a>Hantera klustret med vfxt.py

vfxt.py är ett kommando rads verktyg för att skapa och hantera kluster.

vfxt.py är förinstallerat på den virtuella kluster styrenheten. Om du vill installera det på ett annat system läser du dokumentationen på <https://github.com/Azure/AvereSDK> .

Vfxt.py-skriptet kan användas för följande kluster hanterings aktiviteter:

* Lägg till nya noder i ett kluster
* Stoppa eller starta ett kluster  
* Förstör ett kluster

Som AVERT på kontroll panelen försöker vfxt.py-åtgärder se till att ändrade data lagras permanent på Server dels lagringen innan du stänger av eller förstör klustret eller noden. Detta gör det ett säkrare alternativ än Azure Portal.

En fullständig användnings guide för vfxt.py finns på GitHub: [moln kluster hantering med vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Lägg till klusternoder med vfxt.py

Ett exempel på kommando skript för att lägga till klusternoder finns på kluster styrenheten. Leta upp ``./add-nodes`` på kontroll enheten och öppna den i ett redigerings program för att anpassa den med din kluster information.

Klustret måste köras för att kunna använda det här kommandot.

Ange följande värden:

* Resurs grupp namn för klustret och även för nätverks-och lagrings resurser om de inte finns i samma resurs grupp som klustret
* Kluster plats
* Kluster nätverk och undernät
* Åtkomst roll för klusternod (Använd den inbyggda rollen " [aver operator](../role-based-access-control/built-in-roles.md#avere-operator)")
* Kluster hanteringens IP-adress och administratörs lösen ord
* Antal noder som ska läggas till (1, 2 eller 3)
* Värden för Nodinstans och cache-storlek

Om du inte använder prototypen måste du skapa ett kommando som följande, inklusive all information som beskrivs ovan.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Mer information finns i [lägga till noder i ett kluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) i användnings guiden för vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Stoppa ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Starta ett stoppat kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Eftersom klustret har stoppats måste du skicka instans identifierare för att ange klusternoderna. Läs [Ange vilket kluster som ska ändras](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) i användnings guiden för vfxt.py för mer information.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Förstöra ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Alternativet ``--quick-destroy`` kan användas om du inte vill spara ändrade data från klustrets cacheminne.

Mer information finns i [användnings hand boken för vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) .

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Hantera virtuella kluster datorer från Azure Portal

Azure Portal kan användas för att förstöra klustrade virtuella datorer individuellt, men data integriteten är inte garanterad om klustret inte stängs av korrekt.

Azure Portal kan användas för dessa kluster hanterings aktiviteter:

* Starta en stoppad vFXT-nod
* Stoppa en enskild vFXT-nod (klustret tolkar detta som nodfel)
* Förstör ett vFXT-kluster *om* du inte behöver se till att ändrade data i klustrets cache skrivs till kärn filen
* Ta bort vFXT-noder och andra kluster resurser permanent när de har stängts av på ett säkert sätt

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Starta om vFXT-instanser från Azure Portal

Om du behöver starta om en stoppad nod måste du använda Azure Portal. Välj **virtuella datorer** på den vänstra menyn och klicka sedan på namnet på den virtuella datorn i listan för att öppna dess översikts sida.

Klicka på knappen **Start** överst på sidan Översikt för att återaktivera den virtuella datorn.

![Azure Portal skärmen som visar alternativet att starta en stoppad virtuell dator](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Ta bort klusternoder

Om du vill ta bort en nod från vFXT-klustret men behålla resten av klustret bör du först [ta bort noden från klustret](#manage-nodes-with-avere-control-panel) med hjälp av kontroll panelen aver.

> [!CAUTION]
> Om du tar bort en nod utan att först ta bort den från vFXT-klustret kan data gå förlorade.

Om du vill förstöra en eller flera instanser som används som vFXT-nod permanent använder du Azure Portal.
Välj **virtuella datorer** på den vänstra menyn och klicka sedan på namnet på den virtuella datorn i listan för att öppna dess översikts sida.

Klicka på knappen **ta bort** högst upp på sidan Översikt för att ta bort den virtuella datorn permanent.

Du kan använda den här metoden för att permanent ta bort klusternoder när de har stängts av på ett säkert sätt.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Förstör klustret från Azure Portal

> [!NOTE]
> Om du vill att eventuella återstående klient ändringar i cacheminnet ska skrivas till backend-lagring, antingen använder du `--destroy` alternativet vfxt.py eller använder alternativet aver på kontroll panelen för att stänga klustret på ett städat sätt innan du tar bort Node-instanserna i Azure Portal.

Du kan ta bort instanser av noder genom att ta bort dem i Azure Portal. Du kan ta bort dem en i taget enligt beskrivningen ovan, eller så kan du använda sidan **Virtual Machines** för att hitta alla de virtuella datorerna i klustret, markera dem med kryss rutorna och klicka på knappen **ta bort** för att ta bort alla i en åtgärd.

![Lista över virtuella datorer i portalen, filtrerat efter termen "kluster", med tre av de fyra markerade och markerade](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Ta bort ytterligare kluster resurser från Azure Portal

Om du har skapat ytterligare resurser specifikt för vFXT-klustret kanske du vill ta bort dem som en del av avrivningen av klustret. Förstör inte element som innehåller data du behöver eller objekt som delas med andra projekt.

Förutom att ta bort klusternoderna bör du ta bort dessa komponenter:

* Den virtuella kluster styrenheten
* Data diskar som är kopplade till klusternoder
* Nätverks gränssnitt och offentliga IP-adresser som är kopplade till kluster komponenter
* Virtuella nätverk
* Lagrings behållare och lagrings konton (**endast** om de inte innehåller viktiga data)
* Tillgänglighetsuppsättning

![Azure Portal listan alla resurser som visar resurser som har skapats för ett test kluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Ta bort ett klusters resurs grupp från Azure Portal

Om du har skapat en resurs grupp specifikt för att dela klustret kan du förstöra alla relaterade resurser för klustret genom att förstöra resurs gruppen.

> [!Caution]
> Förstör bara resurs gruppen om du är säker på att inget värde finns i gruppen. Kontrol lera till exempel att du har flyttat alla nödvändiga data från alla lagrings behållare i resurs gruppen.  

Om du vill ta bort en resurs grupp klickar du på **resurs grupper** i portalens vänstra meny och filtrerar listan över resurs grupper för att hitta den som du skapade för vFXT-klustret. Välj resurs gruppen och klicka på de tre punkterna till höger i panelen. Välj **Ta bort resursgrupp**. Portalen uppmanar dig att bekräfta borttagningen, vilket inte kan ångras.

![Resurs grupp som visar åtgärden ta bort resurs grupp](media/avere-vfxt-delete-resource-group.png)
