---
title: Azure HANA – stora instanser-kontroll via Azure Portal | Microsoft Docs
description: Beskriver hur du kan identifiera och interagera med Azure HANA stora instanser via portalen
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07094e399f153f56746a931a4a100b3c0d38f92f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772084"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Kontroll av Azure HANA – stora instanser med Azure Portal

>[!NOTE]
>För rev 4,2 följer du anvisningarna i avsnittet [Hantera BareMetal-instanser via Azure Portal](../../../baremetal-infrastructure/connect-baremetal-infrastructure.md) .

Det här dokumentet beskriver hur [Hana-stora instanser](./hana-overview-architecture.md) presenteras i [Azure Portal](https://portal.azure.com) och vilka aktiviteter som kan utföras med hjälp av Azure Portal med Hana-stora instans enheter som distribueras åt dig. Visning av HANA-stora instanser i Azure Portal tillhandahålls via en Azure Resource Provider för HANA-stora instanser, som för närvarande finns i en offentlig för hands version

## <a name="register-hana-large-instance-resource-provider"></a>Registrera HANA-resurs leverantör med hög instans
Vanligt vis har din Azure-prenumeration som du använde för HANA-stor instans distribution registrerats för resurs leverantören HANA stor instans. Men om du inte kan se distribuerade HANA stora instans enheter bör du registrera resurs leverantören i din Azure-prenumeration. Det finns två sätt att registrera resurs leverantören HANA-stor instans

### <a name="register-through-cli-interface"></a>Registrera via CLI-gränssnitt
Du måste vara inloggad på din Azure-prenumeration som används för distributionen HANA-stor instans via Azure CLI-gränssnittet. Du kan (re) registrera den HANA-stora instans leverantören med det här kommandot:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Mer information finns i artikeln Azure- [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)


### <a name="register-through-azure-portal"></a>Registrera dig via Azure Portal
Du kan (re) registrera resurs leverantören för HANA-stor instans via Azure Portal. Du måste ange din prenumeration i Azure Portal och dubbelklicka på prenumerationen som användes för att distribuera dina HANA-stora instans enheter. På sidan Översikt i din prenumeration väljer du "Resource providers" som visas nedan och skriver "HANA" i sökfönstret. 

![Registrera HLI RP via Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

I skärm bilden som visas har resurs leverantören redan registrerats. Om resurs leverantören ännu inte har registrerats trycker du på omregistrera eller registrera.

Mer information finns i artikeln Azure- [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visning av HANA-stora instans enheter i Azure Portal
När du skickar en begäran om storskalig stor instans distribution uppmanas du att ange den Azure-prenumeration som du ansluter till de stora och HANA-instanserna. Vi rekommenderar att du använder samma prenumeration som du använder för att distribuera SAP-program skiktet som fungerar mot de stora instans enheterna i HANA.
När de första HANA-stora instanserna distribueras skapas en ny [Azure-resurs grupp](../../../azure-resource-manager/management/manage-resources-portal.md) i Azure-prenumerationen som du skickade i distributions förfrågan för dina Hana-instanser.  Den nya resurs gruppen visar en lista över alla dina HANA stora instanser som du har distribuerat i den aktuella prenumerationen.

För att hitta den nya Azure-resurs gruppen visar du resurs gruppen i din prenumeration genom att navigera i det vänstra navigerings fönstret i Azure Portal

![Skärm bild som visar alternativet resurs grupper.](./media/hana-li-portal/portal-resource-group.png)

I listan över resurs grupper visas, kan du behöva filtrera på den prenumeration som du använde för att ha HANA-stora instanser distribuerade

![Filtrera resurs grupper i Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

När du har filtrerat efter rätt prenumeration kan du fortfarande ha en lång lista över resurs grupper. Leta efter en med post-Fix of **-TXXX** där "XXX" är tre siffror, t. ex. **-T050**. 

När du hittat resurs gruppen kan du ange information om den. Listan som du fick kan se ut så här:

![HLI-lista i Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Alla enheter i listan motsvarar en enda HANA-stor instans enhet som har distribuerats i din prenumeration. I det här fallet kan du titta på åtta olika HANA-enheter som har distribuerats i din prenumeration.

Om du har distribuerat flera HANA-klienter med stora instanser under samma Azure-prenumeration hittar du flera Azure-resurs grupper 


## <a name="look-at-attributes-of-single-hli-unit"></a>Titta på attribut för enskild HLI enhet
I listan med de stora instans enheterna i HANA kan du klicka på en enskild enhet och få information om den enskilda HANA-stora instans enheten. 

När du klickar på "Visa fler" på översikts skärmen får du en presentation av enheten, som ser ut så här:

![Visa översikt över en HLI-enhet](./media/hana-li-portal/portal-show-overview.png)

De olika attribut som visas ser till att attributen inte ser annorlunda ut än för Azure VM-attribut. På sidans vänstra rubrik visas resurs gruppen, Azure-regionen, prenumerations namnet och ID: t samt vissa taggar som du har lagt till. Som standard har HANAs stora instans enheter ingen tagg tilldelad. På den högra sidan i sidhuvudet visas namnet på enheten som tilldelad när distributionen genomfördes. Operativ systemet visas samt IP-adressen. Som med virtuella datorer visas även enhets typen HANA stor instans med antalet CPU-trådar och minne. Mer information om de olika HANA-stora instans enheterna som visas här:

- [Tillgängliga SKU:er för HLI](./hana-available-skus.md)
- [Lagrings arkitektur för SAP HANA (stora instanser)](./hana-storage-architecture.md) 

Ytterligare data på den högra nedre sidan är en revidering av den stora, HANA-instansens stämpel. Möjliga värden:

- Revision 3
- Revision 4

Revision 4 är den senaste arkitekturen som lanseras av HANA-stora instanser med större förbättringar av nätverks fördröjningen mellan virtuella Azure-datorer och HANA stora instans enheter som distribueras i revision 4-stämplar eller rader.
En annan viktig information finns i det nedre högra hörnet i översikten med namnet på den Azure närhets placerings grupp som skapas automatiskt för varje distribuerad HANA-stor instans enhet. Den här närhets placerings gruppen måste refereras när du distribuerar de virtuella Azure-datorer som är värdar för SAP-Programskiktet. Genom att använda [Azures närhets placering](../../co-location.md) som är associerad med den stora instans enheten i Hana, ser du till att de virtuella Azure-datorerna distribueras i nära närhet till den stora instans enheten i Hana. Hur närhets placerings grupper kan användas för att hitta SAP-program skiktet i samma Azure-datacenter som den revision 4 värdbaserade HANA-enheten beskrivs i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).

Ett extra fält i den högra kolumnen i rubriken informerar om energispar läget för den stora instans enheten i HANA.

> [!NOTE]
> Energi statusen beskriver om maskin varu enheten är påslagen eller inte. Det ger inte information om operativ systemet som körs. När du startar om en HANA-stor instans enhet kommer du att uppleva en liten stund där enhetens status ändras till **början** för att gå vidare till det **startade** stadiet. Att det är i läget för **startad** innebär att operativ systemet startas eller att operativ systemet har startats helt. Efter en omstart av enheten kan du efter en omstart av enheten omedelbart logga in i enheten så snart Tillstånds växlarna **har startats**.
> 

Om du trycker på "se mer" visas ytterligare information. En ytterligare information visar revideringen av den stora superinstansen i HANA, enheten har distribuerats i. Se artikeln [Vad är SAP HANA på Azure (stora instanser)](./hana-overview-architecture.md) för olika revisioner av Hana-stora instans stämplingar

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrol lera aktiviteter för en enskild HANA-stor instans enhet 
Utöver att ge en översikt av de stora instans enheterna i HANA kan du kontrol lera aktiviteter för en viss enhet. En aktivitets logg kan se ut så här:

![Navigerings fönster i Azure Portal](./media/hana-li-portal/portal-activity-list.png)

En av de viktigaste aktiviteterna som registreras är omstarter av en enhet. De data som visas i listan innehåller status för aktiviteten, tidsstämpeln som aktiviteten fick utlöstes, det prenumerations-ID som aktiviteten fick utlöstes till och den Azure-användare som utlöste aktiviteten. 

En annan aktivitet som registreras är ändringar i enheten i Azure metadata-data. Förutom att starta om startas kan du se aktiviteten **Skriv HANAInstances**. Den här typen av aktivitet utför inga ändringar i själva volymen i HANA-stor instans, men dokumenterar ändringar i enhetens metadata i Azure. I det fall som anges lade vi till och tog bort en tagg (se nästa avsnitt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Lägga till och ta bort en Azure-tagg i en stor HANA-instans enhet
En annan möjlighet är att lägga till en [tagg](../../../azure-resource-manager/management/tag-resources.md) i en stor Hana-instans enhet. Hur Taggar tilldelas skiljer sig inte från tilldelning av taggar till virtuella datorer. Precis som med virtuella datorer finns taggarna i Azure meta data och har samma begränsningar som taggar för virtuella datorer för HANA-stora instanser.

Borttagning av Taggar fungerar på samma sätt som med virtuella datorer. Både aktiviteter, att tillämpa och ta bort en tagg listas i aktivitets loggen för den specifika HANA-stora instans enheten.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Kontrol lera egenskaperna för en HANA-stor instans enhet
Avsnitts **egenskaperna** innehåller viktig information som du får när instanserna överlämnas till dig. Det är ett avsnitt där du får all information som du kan behöva i support ärenden eller som du behöver när du konfigurerar lagrings ögonblicks bilds konfigurationen. Det här avsnittet är en samling data runt din instans, anslutningens instans till Azure och lagrings Server delen. Överst i avsnittet ser ut så här:


![övre delen av HLI-egenskaperna i Azure Portal](./media/hana-li-portal/portal-properties-top.png)

De första data elementen, som du såg i översikts skärmen redan. Men en viktig del av data är ExpressRoute krets-ID: t, som du fick när de första distribuerade enheterna överlämnades. I vissa support fall kan du bli ombedd att ange dessa data. En viktig data inmatning visas längst ned på skärm bilden. De data som visas är IP-adressen till NFS-lagrings huvudet som isolerar lagringen till **klienten** i den stora instans stacken i Hana. Den här IP-adressen behövs också när du redigerar [konfigurations filen för säkerhets kopior av lagrings ögonblicks bilder](./hana-backup-restore.md#set-up-storage-snapshots). 

När du bläddrar nedåt i egenskaps fönstret får du ytterligare data, till exempel ett unikt resurs-ID för din HANA stor instans enhet eller det prenumerations-ID som har tilldelats till distributionen.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Starta om en HANA-stor instans enhet via Azure Portal
Att starta en omstart av Linux-operativsystemet, det finns olika situationer där det inte gick att slutföra en omstart av operativ systemet. Om du vill framtvinga en omstart måste du öppna en tjänstbegäran för att Microsoft-åtgärder ska kunna utföra en omstart av den stora instans enheten i HANA. Funktionerna i en omstart av en HANA stor instans enhet är nu integrerade i Azure Portal. Som du befinner dig i översikts delen av den stora instans enheten HANA visas knappen för omstart överst i data avsnittet

![Starta om steg #1 i Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

När du trycker på knappen starta om får du frågan om du verkligen vill starta om enheten. När du bekräftar genom att trycka på knappen "Ja" startas enheten om.

> [!NOTE]
> Under omstarten kommer du att uppleva en liten stund där enhetens status ändras till **början** för att gå vidare till läget för **Start**. Att det är i läget för **startad** innebär att operativ systemet startas eller att operativ systemet har startats helt. Efter en omstart av enheten kan du efter en omstart av enheten omedelbart logga in i enheten så snart Tillstånds växlarna **har startats**.

> [!IMPORTANT]
> Beroende av mängden minne i den stora volymen av HANA-stor instans, kan en omstart och omstart av maskin varan och operativ systemet ta upp till en timme


## <a name="open-a-support-request-for-hana-large-instances"></a>Öppna en supportbegäran för stora instanser av HANA
Från Azure Portal visning av HANA-stora instans enheter kan du också skapa support förfrågningar specifikt för en HANA stor instans enhet. När du följer länken **ny support förfrågan** 

![åtgärds steg för att starta service förfrågan #1 i Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

För att kunna hämta tjänsten för SAP HANA – stora instanser som visas på nästa skärm, kan du behöva välja alla tjänster som visas nedan

![Välj alla tjänster i Azure Portal](./media/hana-li-portal/portal-create-service-request.png)

I listan över tjänster kan du hitta tjänsten **SAP HANA stor instans**. När du väljer tjänsten kan du välja vissa problem typer som visas:


![Välj problem klass i Azure Portal](./media/hana-li-portal/portal-select-problem-class.png)

Under var och en av de olika problem typerna erbjuder du ett urval av problem under typer som du måste välja för att beskriva ditt problem ytterligare. När du har valt under typen kan du nu ge ämnet ett namn. När du är färdig med urvals processen kan du gå vidare till nästa steg i skapandet. I avsnittet **lösningar** pekar du på dokumentation om Hana-stora instanser, vilket kan ge en pekare till en lösning på problemet. Om du inte hittar någon lösning för ditt problem i den föreslagna dokumentationen går du till nästa steg. I nästa steg kommer du att bli tillfrågad om problemet är med virtuella datorer eller med HANA stora instans enheter. Den här informationen hjälper till att dirigera support förfrågan till rätt specialister. 

![Information om support ärende i Azure Portal](./media/hana-li-portal/portal-support-request-details.png)

När du svarade på frågorna och tillhandahöll ytterligare information kan du gå vidare till nästa steg för att granska support förfrågan och skicka den.

## <a name="next-steps"></a>Nästa steg

- [Övervaka SAP HANA (stora instanser) i Azure](./troubleshooting-monitoring.md)
- [Övervaka och felsöka från HANA-sida](./hana-monitor-troubleshoot.md)