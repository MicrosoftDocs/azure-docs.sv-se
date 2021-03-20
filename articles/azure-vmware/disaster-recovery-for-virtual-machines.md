---
title: Fullständig haveri beredskap för virtuella datorer
description: Den här artikeln visar hur du utför haveri beredskap för virtuella datorer med hjälp av Azure VMware-lösningen
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779619"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Fullständig haveri beredskap för virtuella datorer med Azure VMware-lösning

Den här artikeln innehåller processen för att slutföra haveri beredskap för dina virtuella datorer med VMware HCX-lösning och med hjälp av ett privat moln i Azure VMware-lösningen som återställnings-eller mål plats.

VMware HCX tillhandahåller olika åtgärder som ger fin kontroll och granularitet i replikeringsprinciper. Tillgängliga åtgärder är:

- **Back** – när en katastrof har inträffat. Omvänd hjälper till att göra plats B till käll platsen och plats A, där den skyddade virtuella datorn nu är i liv.

- **Paus** – pausa den aktuella replikeringsprincipen som är associerad med den valda virtuella datorn.

- **Återuppta** – återuppta den aktuella replikeringsprincipen som är associerad med den valda virtuella datorn.

- **Ta bort** – ta bort den aktuella replikeringsprincipen som är associerad med den valda virtuella datorn.

- **Synkronisera nu** – utanför den virtuella datorns virtuella dator med den skyddade virtuella datorn.

Den här guiden omfattar följande scenarier för replikering:

- Skydda en virtuell dator eller en grupp med virtuella datorer.

- Slutför en test återställning av en virtuell dator eller en grupp med virtuella datorer.

- Återställa en virtuell dator eller en grupp med virtuella datorer.

- Omvänt skyddet av en virtuell dator eller en grupp med virtuella datorer.

## <a name="protect-vms"></a>Skydda virtuella datorer

1. Logga in på **vSphere-klienten** på käll platsen och få åtkomst till HCX-plugin- **programmet**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Alternativet HCX i vSphere" border="true":::

1. Ange **katastrof återställnings** ytan och välj **skydda virtuella datorer**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Välj skydda virtuella datorer" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Välj källa och fjärranslutna platser. Fjärrplatsen i det här fallet bör vara det privata molnet för Azure VMware-lösningen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="fönstret skydda virtuella datorer" border="true":::

1. Om det behövs väljer du **standard** alternativ för replikering:

   - **Aktivera komprimering:** Rekommenderas för låga data flödes scenarier.

   - **Aktivera Quiescence:** Pausar den virtuella datorn så att en konsekvent kopia synkroniseras till fjärrplatsen.

   - **Mål lagring:** Fjärrdata-datalager för de skyddade virtuella datorerna och i ett privat moln i Azure VMware-lösningen som ska vara virtuellt San data lager.

   - **Beräknings behållare:** Fjärran sluten vSphere kluster eller resurspool.

   - **Målmapp:** Målmapp, som är valfri, och om ingen mapp har valts placeras de virtuella datorerna direkt under det valda klustret.

   - Återställnings punkt **:** Frågeintervall mellan den virtuella käll datorn och den skyddade virtuella datorn. Det kan vara var som helst mellan 5 minuter och 24 timmar.

   - **Intervall för ögonblicks bild:** Intervall mellan ögonblicks bilder.

   - **Antal ögonblicks bilder:** Totalt antal ögonblicks bilder inom det konfigurerade intervallet för ögonblicks bilder.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="skydda alternativ för virtuella datorer" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Välj en eller flera virtuella datorer i listan och konfigurera de replikeringsalternativ som behövs.

   Som standard ärver de virtuella datorerna den globala inställnings principen som kon figurer ATS i standardreplikerings alternativen. För varje nätverks gränssnitt i den valda virtuella datorn konfigurerar du den fjärranslutna **nätverks port gruppen** och väljer **Slutför** för att starta skydds processen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="alternativ för nätverks gränssnitt" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Övervaka processen för var och en av de valda virtuella datorerna i samma katastrof återställnings plats.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="övervaka förloppet för skydd" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. När den virtuella datorn har skyddats kan du Visa de olika ögonblicks bilderna på fliken **ögonblicks bilder** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="lista över ögonblicks bilder" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Den gula triangeln innebär att ögonblicks bilderna och de virtuella datorerna inte har testats i en test återställnings åtgärd.

   Det finns viktiga skillnader mellan en virtuell dator som är avstängd och som är påslagen. Avbildningen visar synkroniseringsprocessen för en virtuell dator som är påslagen. Synkroniseringen startas tills den första ögonblicks bilden slutförs, vilket är en fullständig kopia av den virtuella datorn och sedan slutförs nästa steg i det konfigurerade intervallet. Den synkroniserar en kopia för en avstängd virtuell dator och sedan visas den virtuella datorn som inaktiv och skydds åtgärden visas som slutförd.  När den virtuella datorn är påslagen påbörjas synkroniseringen på fjärrplatsen.

## <a name="complete-a-test-recover-of-vms"></a>Slutför en test återställning av virtuella datorer

1. Logga in på **vSphere-klienten** på fjärrplatsen, som är det privata molnet för Azure VMware-lösningen. 
1. I **HCX-plugin-programmet** i Disaster Recovery-avsnittet väljer du de lodräta ellipserna på valfri virtuell dator för att Visa menyn åtgärder och väljer sedan **testa återställning av virtuell dator**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Välj Testa återställning av virtuell dator" border="true":::

1. Välj alternativ för testet och den ögonblicks bild som du vill använda för att testa olika tillstånd för den virtuella datorn.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Välj en ögonblicks bild och välj test" border="true":::

1. När du har valt **testet** börjar återställnings åtgärden.

1. När du är färdig kan du kontrol lera den nya virtuella datorn i Azure VMware-lösningen privat moln vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="kontrol lera återställnings åtgärden" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. När testningen har utförts på den virtuella datorn eller ett program som körs på den, gör du en rensning för att ta bort test instansen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Rensa test instans" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Återställa virtuella datorer

1. Logga in på **vSphere-klienten** på fjärrplatsen, som är Azure VMware-lösningens privata moln, och få åtkomst till HCX-plugin- **programmet**.

   För återställnings scenariot används en grupp med virtuella datorer i det här exemplet.

1. Välj den virtuella dator som ska återställas i listan, öppna menyn **åtgärder** och välj **Återställ virtuella datorer**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="återställa virtuella datorer" border="true":::

1. Konfigurera återställnings alternativen för varje instans och välj **Återställ** för att starta återställnings åtgärden.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="bekräfta återställning av virtuella datorer" border="true":::

1. När återställningen har slutförts visas de nya virtuella datorerna i den fjärranslutna vCenter Server inventeringen.

## <a name="complete-a-reverse-replication-on-vms"></a>Slutför en omvänd replikering på virtuella datorer

1. Logga in på **vSphere-klienten** i ditt privata moln i Azure VMware-lösningen och få åtkomst till HCX-plugin- **programmet**.
   
   >[!NOTE]
   > Se till att de ursprungliga virtuella datorerna på käll platsen är avstängda innan du startar den omvända replikeringen. Åtgärden kan inte utföras om de virtuella datorerna inte är avstängd.

1. I listan väljer du de virtuella datorer som ska replikeras tillbaka till käll platsen, öppnar menyn **åtgärder** och väljer **Omvänd**. 
1. Välj **Omvänd** för att starta replikeringen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Välj Omvänd åtgärd under skydda åtgärder" border="true":::

1. Övervaka i avsnittet information om varje virtuell dator.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="granska resultatet av en omvänd åtgärd" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatisering av katastrof återställnings plan

VMware HCX har för närvarande inte någon inbyggd mekanism för att skapa och automatisera en katastrof återställnings plan. VMware HCX innehåller dock en uppsättning REST API: er, inklusive API: er för haveri beredskaps åtgärden. API-specifikationen kan nås i VMware HCX Manager i URL: en.

Dessa API: er behandlar följande åtgärder vid haveri beredskap.

- Skydda

- Återställa

- Testa återställning

- Planerad återställning

- Reverse

- Fråga

- Test rensning

- Pausa

- Återuppta

- Ta bort skydd

- Konfigurera om

Ett exempel på en nytto last för återställnings åtgärd i JSON visas nedan.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Med dessa API: er kan du skapa en anpassad mekanism för att automatisera en katastrof återställnings plan för att skapa och köra.
