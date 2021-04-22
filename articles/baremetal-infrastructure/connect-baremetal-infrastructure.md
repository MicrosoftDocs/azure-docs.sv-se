---
title: Ansluta BareMetal-infrastrukturinstanser i Azure
description: Lär dig hur du identifierar och interagerar med BareMetal-instanser i Azure Portal eller Azure CLI.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871659"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Ansluta BareMetal-infrastrukturinstanser i Azure

Den här artikeln visar hur [Azure Portal](https://portal.azure.com/) visar [BareMetal-instanser](concepts-baremetal-infrastructure-overview.md). Den här artikeln visar också vad du kan göra i Azure Portal med dina distribuerade BareMetal-infrastrukturinstanser. 
 
## <a name="register-the-resource-provider"></a>Registrera resursprovidern
En Azure-resursprovider för BareMetal-instanser ger insyn i instanserna i Azure Portal. Som standard registrerar Azure-prenumerationen som du använder för BareMetal-instansdistributioner *resursprovidern BareMetalInfrastructure.* Om du inte ser dina distribuerade BareMetal-instanser måste du registrera resursprovidern med din prenumeration. 

Du kan registrera BareMetal-instansresursprovidern med hjälp av Azure Portal eller Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Du måste visa din prenumeration i listan Azure Portal dubbelklicka på prenumerationen som används för att distribuera dina BareMetal-instanser.
 
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** på menyn i Azure-portalen.

1. I rutan **Alla tjänster** anger du **prenumeration** och väljer sedan **Prenumerationer.**

1. Välj prenumerationen i prenumerationslistan.

1. Välj **Resursproviders** **och ange BareMetalInfrastructure** i sökningen. Resursprovidern ska **vara Registrerad**, som visas på bilden.
 
>[!NOTE]
>Om resursprovidern inte är registrerad väljer du **Registrera**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Skärmbild som visar registrerade BareMetal-instanser.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här börjar du använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Logga in på Azure-prenumerationen som du använder för distributionen av BareMetal-instansen via Azure CLI. Registrera `BareMetalInfrastructure` resursprovidern med [kommandot az provider](/cli/azure/provider#az_provider_register) register:

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Du kan använda kommandot [az provider list](/cli/azure/provider#az_provider_list) för att se alla tillgängliga providers.

---

Mer information om resursproviders finns i [Azure-resursproviders och resurstyper.](../azure-resource-manager/management/resource-providers-and-types.md)  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-instanser i Azure Portal
 
När du skickar en distributionsbegäran för BareMetal-instansen anger du den Azure-prenumeration som du ansluter till BareMetal-instanserna. Använd samma prenumeration som du använder för att distribuera programlagret som fungerar mot BareMetal-instanserna.
 
Under distributionen av dina BareMetal-instanser skapas en ny [Azure-resursgrupp](../azure-resource-manager/management/manage-resources-portal.md) i den Azure-prenumeration som du använde i distributionsbegäran. Den här nya resursgruppen visar en lista över alla BareMetal-instanser som du har distribuerat i prenumerationen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I BareMetal-prenumerationen går du Azure Portal väljer **Resursgrupper**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Skärmbild som visar listan över resursgrupper.":::

1. Leta upp den nya resursgruppen i listan.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Skärmbild som visar BareMetal-instansen i en filtrerad lista över resursgrupper." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Du kan filtrera på den prenumeration som du använde för att distribuera BareMetal-instansen. När du har filtrerat på rätt prenumeration kan du ha en lång lista över resursgrupper. Leta efter en med en efterkorrigering av **-Txxx** där xxx är tre siffror som **-T250**.

1. Välj den nya resursgruppen för att visa dess information. Avbildningen visar en BareMetal-instans som har distribuerats.
   
   >[!NOTE]
   >Om du har distribuerat flera BareMetal-instansklienter under samma Azure-prenumeration visas flera Azure-resursgrupper.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill se alla dina BareMetal-instanser kör du [kommandot az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) för din resursgrupp:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Parametern `--output` är en global parameter som är tillgänglig för alla kommandon. **Tabellvärdet** visar utdata i ett användarvänligt format. Mer information finns i [Utdataformat för Azure CLI-kommandon.](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>Visa attributen för en enskild instans

Du kan visa information om en enskild instans.

### <a name="portal"></a>[Portal](#tab/azure-portal)

I listan över BareMetal-instanser väljer du den enskilda instans som du vill visa.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Skärmbild som visar BareMetal-instansattributen för en enskild instans." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Attributen i avbildningen ser inte mycket annorlunda ut än attributen för virtuella Datorer i Azure. Till vänster visas resursgruppen, Azure-regionen och prenumerationsnamnet och ID:t. Om du har tilldelat taggar visas de även här. Som standard har BareMetal-instanserna inte tilldelade taggar.
 
Till höger ser du namnet på BareMetal-instansen, operativsystemet (OS), IP-adressen och SKU:n som visar antalet CPU-trådar och minne. Du ser även energitillståndet och maskinvaruversionen (revision av BareMetal-instansstämpeln). Energispartillståndet anger om maskinvaruenheten är påslagen eller avstängd. Operativsystemets information anger dock inte om det är igång eller inte.
 
Möjliga maskinvarurevisioner är:

* Revision 3 (Rev 3)

* Revision 4 (Rev 4)
 
* Revision 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 är den senaste omdöpta BareMetal-infrastrukturen med hjälp av den befintliga Rev 4-arkitekturen. Rev 4 ger närmare närhet till värdar för virtuella Azure-datorer. Det har betydande förbättringar av nätverksfördröjningen mellan virtuella Azure-datorer och SAP HANA instanser. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal. Mer information finns i [BareMetal Infrastructure on Azure](concepts-baremetal-infrastructure-overview.md).

 
På höger sida hittar du även [](../virtual-machines/co-location.md) namnet på Azure-närhetsplaceringsgruppen, som skapas automatiskt för varje distribuerad BareMetal-instans. Referera till närhetsplaceringsgruppen när du distribuerar de virtuella Azure-datorer som är värdar för programlagret. När du använder närhetsplaceringsgruppen som är associerad med BareMetal-instansen ser du till att de virtuella Azure-datorerna distribueras nära BareMetal-instansen.
 
>[!TIP]
>Information om hur du hittar programlagret i samma Azure-datacenter som Revision 4.x finns i Närhetsplaceringsgrupper i [Azure för optimal nätverksfördröjning.](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill se information om en BareMetal-instans kör du [kommandot az baremetalinstance show:](/cli/azure/baremetalinstance#az_baremetalinstance_show)

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Om du är osäker på instansnamnet kör du kommandot `az baremetalinstance list` som beskrivs ovan.

---
 
## <a name="check-activities-of-a-single-instance"></a>Kontrollera aktiviteter för en enskild instans
 
Du kan kontrollera aktiviteterna för en enda BareMetal-instans. En av de viktigaste aktiviteterna som registreras är omstarter av instansen. De data som anges innehåller aktivitetens status, tidsstämpel för den utlösta aktiviteten, prenumerations-ID och den Azure-användare som utlöste aktiviteten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Skärmbild som visar BareMetal-instansaktiviteterna." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Ändringar av instansens metadata i Azure registreras också i aktivitetsloggen. Förutom omstarten som initieras kan du se aktiviteten **Skriv Bare BareNstances**. Den här aktiviteten gör inga ändringar på själva BareMetal-instansen, men dokumenterar ändringarna i enhetens metadata i Azure.
 
En annan aktivitet som registreras är när du lägger till eller tar bort [en tagg](../azure-resource-manager/management/tag-resources.md) i en instans.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Lägga till och ta bort en Azure-tagg till en instans

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Du kan lägga till Azure-taggar till en BareMetal-instans eller ta bort dem. Taggar tilldelas precis som de gör när du tilldelar taggar till virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure-metadata. Taggar har samma begränsningar för BareMetal-instanser som för virtuella datorer.
 
Att ta bort taggar fungerar också på samma sätt som för virtuella datorer. Att tillämpa och ta bort en tagg visas i aktivitetsloggen för BareMetal-instansen.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Att tilldela taggar till BareMetal-instanser fungerar på samma sätt som att tilldela taggar för virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure-metadata. Taggar har samma begränsningar för BareMetal-instanser som för virtuella datorer.

Om du vill lägga till taggar till en BareMetal-instans kör du [kommandot az baremetalinstance update:](/cli/azure/baremetalinstance#az_baremetalinstance_update)

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Använd samma kommando för att ta bort en tagg:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Kontrollera egenskaperna för en instans
 
När du hämtar instanserna kan du gå till avsnittet Egenskaper för att visa de data som samlats in om instanserna. Data som samlas in omfattar Azure-anslutning, lagringsbackend, ExpressRoute-krets-ID, unikt resurs-ID och prenumerations-ID. Du använder den här informationen i supportbegäranden eller när du ställer in konfiguration av ögonblicksbilder av lagring.
 
En annan viktig information du ser är NFS IP-adressen för lagring. Det isolerar lagringen till din **klientorganisation** i BareMetal-instansstacken. Du använder den här IP-adressen när du redigerar konfigurationsfilen [för säkerhetskopior av ögonblicksbilder av lagring.](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Skärmbild som visar egenskapsinställningarna för BareMetal-instansen." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Starta om en BareMetal-instans via Azure Portal

Det finns olika situationer där operativsystemet inte slutför en omstart, vilket kräver en omstart av BareMetal-instansen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Du kan göra en omstart av instansen direkt från Azure Portal:
 
Välj **Starta om** och sedan **Ja** för att bekräfta omstarten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Skärmbild som visar hur du startar om BareMetal-instansen.":::
 
När du startar om en BareMetal-instans får du en fördröjning. Under den här fördröjningen flyttas energitillståndet **från Startar** **till Startad,** vilket innebär att operativsystemet har startats helt. Efter en omstart kan du därför bara logga in på enheten när tillståndet växlar till **Startad**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill starta om en BareMetal-instans använder du [kommandot az baremetalinstance restart:](/cli/azure/baremetalinstance#az_baremetalinstance_restart)

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Beroende på mängden minne i BareMetal-instansen kan en omstart och en omstart av maskinvaran och operativsystemet ta upp till en timme.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Öppna en supportbegäran för BareMetal-instanser
 
Du kan skicka supportbegäranden specifikt för BareMetal-instanser.
1. I Azure Portal hjälp **+ support skapar** du en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och anger följande information för supportbegäran:
 
   - **Typ av problem:** Välj en problemtyp.
 
   - **Prenumeration:** Välj din prenumeration.
 
   - **Tjänst:** BareMetal-infrastruktur
 
   - **Resurs:** Ange namnet på instansen.
 
   - **Sammanfattning:** Ange en sammanfattning av din begäran.
 
   - **Problemtyp:** Välj en problemtyp.
 
   - **Undergrupp av problem:** Välj en undertyp för problemet.

1. Välj fliken **Lösningar** för att hitta en lösning på problemet. Om du inte hittar någon lösning går du till nästa steg.

1. Välj fliken **Information** och välj om problemet gäller virtuella datorer eller BareMetal-instanser. Den här informationen hjälper dig att dirigera supportbegäran till rätt specialister.

1. Ange när problemet började och välj instansregion.

1. Ange mer information om begäran och ladda upp en fil om det behövs.

1. Välj **Granska + skapa för** att skicka begäran.
 
Det tar upp till fem arbetsdagar för en supportrepresentant att bekräfta din begäran.

## <a name="next-steps"></a>Nästa steg

Läs mer om arbetsbelastningar:

- [Vad är SAP HANA på Azure (stora instanser)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
