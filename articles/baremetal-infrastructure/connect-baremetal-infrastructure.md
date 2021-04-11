---
title: Anslut BareMetal Infrastructure instances i Azure
description: Lär dig hur du identifierar och interagerar med BareMetal-instanser i Azure Portal eller Azure CLI.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: a7fdc17aa4271915f7dc02aaa2d7a688016bf892
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579199"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Anslut BareMetal Infrastructure instances i Azure

Den här artikeln visar hur [Azure Portal](https://portal.azure.com/) visar [BareMetal-instanser](concepts-baremetal-infrastructure-overview.md). Den här artikeln visar också vad du kan göra i Azure Portal med de distribuerade BareMetal-infrastruktur instanserna. 
 
## <a name="register-the-resource-provider"></a>Registrera resursprovidern
En Azure Resource Provider för BareMetal-instanser ger insyn i instanserna i Azure Portal. Som standard registrerar Azure-prenumerationen som du använder för BareMetal-instansen distribution av *BareMetalInfrastructure* -resurs leverantören. Om du inte ser dina distribuerade BareMetal-instanser måste du registrera resurs leverantören med din prenumeration. 

Du kan registrera BareMetal-instansens resurs leverantör genom att använda Azure Portal eller Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Du måste ange din prenumeration i Azure Portal och sedan dubbelklicka på prenumerationen som används för att distribuera dina BareMetal-instanser.
 
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** på menyn i Azure-portalen.

1. I rutan **alla tjänster** anger du **prenumeration** och väljer sedan **prenumerationer**.

1. Välj prenumerationen från prenumerations listan.

1. Välj **resurs leverantörer** och ange **BareMetalInfrastructure** i sökningen. Resurs leverantören bör vara **registrerad**, som bilden visar.
 
>[!NOTE]
>Om resurs leverantören inte är registrerad väljer du **Registrera**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Skärm bild som visar BareMetal-instanser som registrerats.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Logga in på Azure-prenumerationen som du använder för BareMetal-instansen av Azure CLI. Registrera `BareMetalInfrastructure` resurs leverantören med [AZ Provider register](/cli/azure/provider#az_provider_register) kommando:

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Du kan använda kommandot [AZ Provider List](/cli/azure/provider#az_provider_list) för att se alla tillgängliga providers.

---

Mer information om resurs leverantörer finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-instanser i Azure Portal
 
När du skickar en distributions förfrågan för BareMetal-instansen anger du den Azure-prenumeration som du ansluter till BareMetal-instanserna. Använd samma prenumeration som du använder för att distribuera program lagret som fungerar mot BareMetal-instanserna.
 
Under distributionen av BareMetal-instanserna skapas en ny [Azure-resurs grupp](../azure-resource-manager/management/manage-resources-portal.md) i Azure-prenumerationen som du använde i distributions förfrågan. I den här nya resurs gruppen visas alla BareMetal-instanser som du har distribuerat i den prenumerationen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I BareMetal-prenumerationen väljer du **resurs grupper** i Azure Portal.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Skärm bild som visar listan över resurs grupper.":::

1. Leta upp den nya resurs gruppen i listan.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Skärm bild som visar BareMetal-instansen i en lista med filtrerade resurs grupper." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Du kan filtrera på den prenumeration som du använde för att distribuera BareMetal-instansen. När du har filtrerat till rätt prenumeration kan du ha en lång lista över resurs grupper. Leta efter en med post-Fix of **-TXXX** där xxx är tre siffror som **-T250**.

1. Välj den nya resurs gruppen om du vill visa information om den. Bilden visar en BareMetal-instans som har distribuerats.
   
   >[!NOTE]
   >Om du har distribuerat flera BareMetal-instanser under samma Azure-prenumeration visas flera Azure-resurs grupper.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill se alla BareMetal-instanser kör du kommandot [AZ baremetalinstance List](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) för resurs gruppen:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output`Parametern är en global parameter som är tillgänglig för alla kommandon. **Tabellens** värde visar utdata i ett eget format. Mer information finns i [utdataformat för Azure CLI-kommandon](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Visa attributen för en enskild instans

Du kan visa information om en enda instans.

### <a name="portal"></a>[Portal](#tab/azure-portal)

I listan över BareMetal-instanser väljer du den enda instans som du vill visa.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Skärm bild som visar BareMetal-instansens attribut för en enskild instans." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Attributen i bilden ser inte likadana ut som attributen för virtuell Azure-dator. Till vänster visas resurs gruppen, Azure-regionen och prenumerations namnet och ID: t. Om du har tilldelat Taggar ser du dem även här. BareMetal-instanserna har som standard inte tilldelade taggar.
 
Till höger ser du namnet på BareMetal-instansen, operativ systemet (OS), IP-adressen och SKU: n som visar antalet CPU-trådar och minne. Du ser också energi tillstånds-och maskin varu versionen (revision av BareMetal-instansnamnet). Energi läget anger om maskin varu enheten är påslagen eller inte. Informationen om operativ systemet anger dock inte om den är igång.
 
Möjliga maskin varu revisioner är:

* Revision 3 (rev 3)

* Revision 4 (rev 4)
 
* Revision 4,2 (rev 4,2)
 
>[!NOTE]
>Rev 4,2 är den senaste ommärkta BareMetal-infrastrukturen med den befintliga rev 4-arkitekturen. Rev 4 ger närmare närhet till Azures virtuella dator värdar. Den har betydande förbättringar i nätverks fördröjningen mellan virtuella Azure-datorer och SAP HANA instanser. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal. Mer information finns i [BareMetal-infrastruktur på Azure](concepts-baremetal-infrastructure-overview.md).

 
På den högra sidan hittar du också namnet på [Azure närhets placerings gruppen](../virtual-machines/co-location.md) , som skapas automatiskt för varje distribuerad BareMetal-instans. Referera till närhets placerings gruppen när du distribuerar de virtuella Azure-datorer som är värdar för program lagret. När du använder placerings gruppen för närhet som är kopplad till BareMetal-instansen ser du till att de virtuella Azure-datorerna går att distribuera nära BareMetal-instansen.
 
>[!TIP]
>Om du vill hitta program lagret i samma Azure-datacenter som revision 4. x, se [placerings grupper för Azure närhet för optimal nätverks fördröjning](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill se information om en BareMetal-instans kör du kommandot [AZ baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Om du är osäker på instans namnet kör du `az baremetalinstance list` kommandot, som beskrivs ovan.

---
 
## <a name="check-activities-of-a-single-instance"></a>Kontrol lera aktiviteter för en enskild instans
 
Du kan kontrol lera aktiviteterna för en enskild BareMetal-instans. En av de viktigaste aktiviteterna som registreras är omstarter av instansen. De data som visas innehåller aktivitetens status, tidsstämpel för aktiviteten som utlöses, prenumerations-ID och den Azure-användare som utlöste aktiviteten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Skärm bild som visar BareMetal-instansens aktiviteter." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Ändringar av instansens metadata i Azure registreras också i aktivitets loggen. Förutom att starta om startas kan du se aktiviteten **Skriv BareMetallnstances**. Den här aktiviteten gör inga ändringar i själva BareMetal-instansen utan dokumenterar ändringarna i enhetens metadata i Azure.
 
En annan aktivitet som registreras är när du lägger till eller tar bort en [tagg](../azure-resource-manager/management/tag-resources.md) till en instans.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Lägga till och ta bort en Azure-tagg till en instans

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Du kan lägga till Azure-taggar till en BareMetal-instans eller ta bort dem. Taggarna tilldelas på samma sätt som när de tilldelas virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure-metadata. Taggar har samma begränsningar för BareMetal-instanser som för virtuella datorer.
 
Att ta bort taggar fungerar också på samma sätt som för virtuella datorer. Att tillämpa och ta bort en tagg visas i aktivitets loggen för BareMetal-instansen.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Att tilldela taggar till BareMetal-instanser fungerar på samma sätt som att tilldela taggar för virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure-metadata. Taggar har samma begränsningar för BareMetal-instanser som för virtuella datorer.

Om du vill lägga till taggar till en BareMetal-instans kör du kommandot [AZ baremetalinstance Update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Använd samma kommando för att ta bort en tagg:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Kontrol lera egenskaperna för en instans
 
När du hämtar instanserna kan du gå till avsnittet Egenskaper om du vill visa de data som samlats in om instanserna. Data som samlas in inkluderar Azure-anslutning, lagrings Server del, ExpressRoute krets-ID, unikt resurs-ID och prenumerations-ID. Du använder den här informationen i support förfrågningar eller när du konfigurerar konfiguration av lagrings ögonblicks bild.
 
En annan viktig information som du ser är lagrings-NFS-IP-adressen. Den isolerar lagringen till din **klient** organisation i BareMetal instance-stacken. Du använder den här IP-adressen när du redigerar [konfigurations filen för säkerhets kopior av lagrings ögonblicks bilder](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Skärm bild som visar BareMetal-instansens egenskaps inställningar." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Starta om en BareMetal-instans via Azure Portal

Det finns olika situationer där operativ systemet inte slutför en omstart, vilket kräver en omstart av BareMetal-instansen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Du kan göra en omstart av instansen direkt från Azure Portal:
 
Välj **starta om** och sedan **Ja** för att bekräfta omstarten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Skärm bild som visar hur du startar om BareMetal-instansen.":::
 
När du startar om en BareMetal-instans får du en fördröjning. Under den här fördröjningen flyttas energi spar läget från **Starta** till **startad**, vilket innebär att operativ systemet har startats helt. Efter en omstart kan du bara logga in i enheten när tillstånds växlarna **har startats**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill starta om en BareMetal-instans använder du kommandot [AZ baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Beroende på mängden minne i BareMetal-instansen kan en omstart och omstart av maskin varan och operativ systemet ta upp till en timme.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Öppna en support förfrågan för BareMetal-instanser
 
Du kan skicka support förfrågningar specifikt för BareMetal-instanser.
1. I Azure Portal, under **Hjälp + Support**, skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
 
   - **Typ av problem:** Välj en typ av problem.
 
   - **Prenumeration:** Välj din prenumeration.
 
   - **Tjänst:** BareMetal-infrastruktur
 
   - **Resurs:** Ange namnet på instansen.
 
   - **Sammanfattning:** Ange en sammanfattning av din begäran.
 
   - **Problem typ:** Välj en problem typ.
 
   - **Problem under typ:** Välj en undertyp för problemet.

1. Välj fliken **lösningar** för att hitta en lösning på problemet. Om du inte hittar någon lösning går du till nästa steg.

1. Välj fliken **information** och välj om problemet är med virtuella datorer eller BareMetal-instanser. Den här informationen hjälper till att dirigera support förfrågan till rätt specialister.

1. Ange när problemet började och välj instans region.

1. Ange mer information om begäran och ladda upp en fil om det behövs.

1. Välj **Granska + skapa** för att skicka begäran.
 
Det tar upp till fem arbets dagar för en support representant att bekräfta din begäran.

## <a name="next-steps"></a>Nästa steg

Läs mer om arbets belastningar:

- [Vad är SAP HANA på Azure (stora instanser)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
