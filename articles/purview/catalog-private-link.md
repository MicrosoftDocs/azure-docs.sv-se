---
title: Använd privata slut punkter för säker åtkomst till avdelningens kontroll
description: Den här artikeln beskriver hur du kan skapa en privat slut punkt för ditt avdelningens kontroll-konto
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720921"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Använd privata slut punkter för ditt avdelningens kontroll-konto

Du kan använda privata slut punkter för dina avdelningens kontroll-konton för att tillåta klienter och användare i ett virtuellt nätverk (VNet) att säkert komma åt katalogen via en privat länk. Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för ditt avdelningens kontroll-konto. Nätverks trafik mellan klienterna i VNet och avdelningens kontroll-kontot passerar över VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

## <a name="create-purview-account-with-a-private-endpoint"></a>Skapa avdelningens kontroll-konto med en privat slut punkt

1. Navigera till [Azure Portal](https://portal.azure.com) och sedan till ditt avdelningens kontroll-konto.

1. Fyll grundläggande information och ange anslutnings metod till privat slut punkt i fliken **nätverk** . Konfigurera dina inmatnings privata slut punkter genom att ange information om **prenumeration, VNet och undernät** som du vill koppla till din privata slut punkt.

    > [!NOTE]
    > Skapa bara en privat slut punkt om du vill aktivera nätverks isolering för sökning från slut punkt till slut punkt för både dina Azure-och lokala källor. Vi stöder för närvarande inte inmatnings privata slut punkter som arbetar med dina AWS-källor.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Skapa en privat slut punkt i Azure Portal":::

1. Du kan också välja att ställa in en **privat DNS zon** för varje intag privat slut punkt.

1. Klicka på Lägg till för att lägga till en privat slut punkt för ditt avdelningens kontroll-konto.

1. På sidan Skapa privat slut punkt anger du avdelningens kontroll under resurs till **konto**, väljer ditt virtuella nätverk och undernät och väljer den privat DNS zon där DNS ska registreras (du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av filer på dina virtuella datorer).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Val av privat slut punkts skapande":::

1. Välj **OK**.

1. Välj **Granska + skapa**. Du tas till sidan Granska + skapa där Azure verifierar din konfiguration.

1. När du ser ett meddelande som anger att valideringen har slutförts klickar du på **Skapa**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Verifieringen har slutförts för skapande av konto":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Skapa en privat slut punkt för avdelningens kontroll-webbportalen

1. Navigera till det avdelningens kontroll-konto som du nyss skapade, Välj de privata slut punkts anslutningarna under avsnittet Inställningar.

1. Klicka på + privat slut punkt för att skapa en ny privat slut punkt.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Skapa Portal privat slut punkt":::

1. Fyll i grundläggande information.

1. På fliken resurs väljer du resurs typ som **Microsoft. avdelningens kontroll/Accounts**.

1. Välj den resurs som ska vara det nyskapade avdelningens kontroll-kontot och välj mål under resurs som **Portal**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Information för privat Portal-slutpunkt":::

1. Välj det virtuella nätverket och Privat DNS zon på fliken konfiguration. Gå till sidan Sammanfattning och klicka på **skapa** för att skapa portalens privata slut punkt.

## <a name="enabling-access-to-azure-active-directory"></a>Aktivera åtkomst till Azure Active Directory

> [!NOTE]
> Om din virtuella dator, VPN-gateway eller VNET peering Gateway har offentlig Internet åtkomst, kan den komma åt avdelningens kontroll-portalen och avdelningens kontroll-kontot som är aktiverat med privata slut punkter och du behöver inte följa resten av anvisningarna nedan. Om det privata nätverket har regler för nätverks säkerhets grupper som har angetts för att neka all offentlig Internet trafik, måste du lägga till vissa regler för att aktivera AAD-åtkomst. Följ anvisningarna nedan för att göra det.

Anvisningarna nedan är till för att komma åt avdelningens kontroll på ett säkert sätt från en virtuell Azure-dator. Liknande steg måste följas om du använder VPN eller andra VNet-peering-gatewayer.

1. Gå till den virtuella datorn på fliken Azure Portal, Välj nätverk under Inställningar. Välj sedan utgående port regler och klicka på Lägg till regel för utgående port.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Lägger till utgående regel":::

2. I Lägg till regel för utgående port regel väljer du *målet* som service tag, mål tjänst tag gen som ska vara **AzureActiveDirectory**, mål Port intervall som *, åtgärden som ska tillåtas, **prioriteten bör vara högre än regeln som nekade all Internet trafik**. Skapa regeln.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Lägger till information om utgående regel":::

3. Följ samma steg för att skapa en annan regel för att tillåta "**AzureResourceManager**"-tjänst tag gen. Om du behöver åtkomst till Azure Portal kan du också lägga till en regel för tjänst tag gen '*AzurePortal*'.

4. Anslut till den virtuella datorn, öppna webbläsaren och navigera till webb läsar konsolen (Ctrl + Shift + J) och växla till fliken nätverk för att övervaka nätverks begär Anden. Ange web.purview.azure.com i rutan URL och försök logga in med dina AAD-autentiseringsuppgifter. Inloggningen kan antagligen Miss lyckas och på fliken nätverk i-konsolen kan du se AAD försöker komma åt aadcdn.msauth.net men blockeras.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Information om inloggningen lyckades":::

5. I det här fallet öppnar du en kommando tolk på den virtuella datorn och pingar denna URL (aadcdn.msauth.net), hämtar dess IP-adress och lägger sedan till en utgående port regel för IP-adressen i VM: s nätverks säkerhets regler. Ange IP-adress för målet och ange IP-adresser för målet som IP-adress för aadcdn. På grund av belastningsutjämnare och Traffic Manager kan AAD CDN IP vara dynamisk. När du har tilldelat IP-adressen är det bättre att lägga till den i den virtuella datorns värd fil för att tvinga webbläsaren att besöka den IP-adressen för att få AAD CDN.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Testa ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN-regel":::

6. När den nya regeln har skapats går du tillbaka till den virtuella datorn och försöker logga in med autentiseringsuppgifterna för AAD igen. Om inloggningen lyckas är avdelningens kontroll-portalen redo att användas. Men i vissa fall omdirigerar AAD till andra domäner för inloggning baserat på kundens kontotyp. För ett live.com-konto omdirigeras t. ex. AAD till live.com för att logga in, kommer dessa förfrågningar att blockeras igen. För Microsoft Employee-konton kommer AAD att få åtkomst till msft.sts.microsoft.com för inloggnings information. Kontrol lera nätverks förfrågningarna i webb läsar fliken nätverk om du vill se vilka domän förfrågningar som blockeras, gör om föregående steg för att få dess IP-adress och lägga till utgående port regler i nätverks säkerhets gruppen för att tillåta begär Anden för den IP-adressen (om möjligt, Lägg till URL-adressen och IP-adressen till den virtuella datorns värd fil för att åtgärda Om du känner till den exakta inloggnings domänens IP-intervall kan du också lägga till dem direkt i nätverks regler.

7. Nu måste inloggningen till AAD lyckas. Avdelningens kontroll-portalen kommer att läsas in men det går inte att visa alla avdelningens kontroll-konton eftersom det bara kan komma åt ett speciellt avdelningens kontroll-konto. Ange *Web. avdelningens kontroll. Azure. com/Resource/{PurviewAccountName}* om du vill gå direkt till det avdelningens kontroll-konto som du har konfigurerat en privat slut punkt för.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Inmatnings privata slut punkter och skannings källor i privata nätverk, virtuella nätverk och bakom privata slut punkter

Om du vill säkerställa nätverks isolering för dina metadata som flödar från källan som genomsöks till avdelningens kontroll-DataMap måste du följa dessa steg:
1. Aktivera en privat inmatnings **slut punkt** genom att följa stegen i [det här](#creating-an-ingestion-private-endpoint) avsnittet
1. Skanna källan med hjälp av en **IR med egen värd**.
 
    1. Alla lokala käll typer, t. ex. SQL Server, Oracle, SAP och andra, stöds för närvarande endast via IR-baserade inläsningar med egen värd. IR med egen värd måste köras i ditt privata nätverk och sedan peer-kopplas med ditt VNet i Azure. Ditt Azure-VNet måste sedan aktive ras på den privata slut punkten för inmatningen genom att följa stegen [nedan](#creating-an-ingestion-private-endpoint) 
    1. För alla typer av **Azure** -källor, till exempel Azure Blob storage, Azure SQL Database och andra, måste du uttryckligen välja att köra genomsökningen med hjälp av egen värd-IR för att säkerställa nätverks isolering. Följ stegen [nedan](manage-integration-runtimes.md) för att konfigurera en lokal IR. Sedan ställer du in din genomsökning på Azure-källan genom att välja att använda IR med egen värd i list rutan **Anslut via integration runtime** för att säkerställa nätverks isolering. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Köra Azure Scan med egen värd-IR":::

> [!NOTE]
> Vi stöder för närvarande inte metoden MSI-autentiseringsuppgifter när du skannar dina Azure-källor med hjälp av en lokal IR. Du måste använda någon av de andra metoderna som stöds för Azure-källan.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Aktivera privat slut punkt för befintliga avdelningens kontroll-konton

Det finns två sätt att lägga till avdelningens kontroll privata slut punkter när du har skapat ditt avdelningens kontroll-konto:

- Använda Azure Portal (avdelningens kontroll-konto)
- Använda det privata länk centret

### <a name="using-the-azure-portal-purview-account"></a>Använda Azure Portal (avdelningens kontroll-konto)

1. Gå till avdelningens kontroll-kontot från Azure Portal och välj de privata slut punkts anslutningarna under avsnittet **nätverk** i **Inställningar**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Skapa privat slut punkt för konto":::

1. Klicka på + privat slut punkt för att skapa en ny privat slut punkt.

1. Fyll i grundläggande information.

1. På fliken resurs väljer du resurs typ som **Microsoft. avdelningens kontroll/Accounts**.

1. Välj den resurs som ska vara avdelningens kontroll-konto och välj mål under resurs som ska vara **konto**.

1. Välj det **virtuella nätverket** och **privat DNS zon** på fliken konfiguration. Gå till sidan Sammanfattning och klicka på **skapa** för att skapa portalens privata slut punkt.

> [!NOTE]
> Du måste följa samma steg som ovan för den mål under resurs som valts som **Portal** .

#### <a name="creating-an-ingestion-private-endpoint"></a>Skapar en privat slut punkt för inmatning

1. Gå till avdelningens kontroll-kontot från Azure Portal och välj de privata slut punkts anslutningarna under avsnittet **nätverk** i **Inställningar**.
1. Gå till fliken inmatning **privat slut punkt anslutningar** och klicka på **+ ny** för att skapa en ny inmatnings privat slut punkt.

1. Fyll i grundläggande information och VNET-information.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Fyll privat slut punkts information":::

1. Slutför inställningen genom att klicka på **skapa** .

> [!NOTE]
> Inmatnings privata slut punkter kan bara skapas via avdelningens kontroll Azure Portal erfarenhet som beskrivs ovan. Den kan inte skapas från det privata länk centret.

### <a name="using-the-private-link-center"></a>Använda det privata länk centret

1. Navigera till [Azure Portal](https://portal.azure.com).

2. I Sök fältet högst upp på sidan söker du efter "privat länk" och navigerar till bladet privat länk genom att klicka på det första alternativet.

3. Klicka på + Lägg till och fyll i grundläggande information.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Skapa PE från Private Link Center":::

4. Välj den resurs som ska vara det redan skapade avdelningens kontroll-kontot och välj mål under resurs som ska vara **konto**.

5. Välj det virtuella nätverket och Privat DNS zon på fliken konfiguration. Gå till sidan Sammanfattning och klicka på **skapa** för att skapa kontots privata slut punkt.

> [!NOTE]
> Du måste följa samma steg som ovan för den mål under resurs som valts som **Portal** .

## <a name="firewalls-to-restrict-public-access"></a>Brand väggar som begränsar offentlig åtkomst

Följ stegen nedan om du vill klippa ut åtkomsten till avdelningens kontroll-kontot helt från det offentliga Internet. Den här inställningen gäller för privata slut punkts anslutningar för privata slut punkter och inläsningar.

1. Gå till avdelningens kontroll-kontot från Azure Portal och välj de privata slut punkts anslutningarna under avsnittet **nätverk** i **Inställningar**.
1. Gå till fliken brand vägg och kontrol lera att växlingen är inställd på **neka**.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Brand Väggs inställningar för privata slut punkt":::

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll-Data Catalog](how-to-browse-catalog.md)

- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
