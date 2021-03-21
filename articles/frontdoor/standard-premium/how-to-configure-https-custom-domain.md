---
title: Konfigurera HTTPS för din anpassade domän i en Azure front dörr standard/Premium SKU-konfiguration
description: I den här artikeln får du lära dig hur du integrerar en anpassad domän i Azures SKU för standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740825"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Konfigurera HTTPS på en SKU för standard-/Premium-tjänsten (förhands granskning) från en annan dator med hjälp av Azure Portal

> [!NOTE]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azure frontend standard/Premium möjliggör säker TLS-leverans till dina program som standard när en anpassad domän läggs till. Genom att använda HTTPS-protokollet på din anpassade domän ser du till att dina känsliga data levereras säkert med TLS/SSL-kryptering när det skickas via Internet. När webbläsaren är ansluten till en webbplats via HTTPS valideras webbplatsens säkerhetscertifikat och verifierar att det är utfärdat av en giltig certifikatutfärdare. Den här processen ger trygghet och skyddar dina webbprogram mot attacker.

Azure frontend-standarden/Premium stöder både Azure-hanterade certifikat och Kundhanterade certifikat. Azures frontend-dörr som standard aktiverar automatiskt HTTPS till alla dina anpassade domäner med hjälp av Azure Managed certificates. Inga ytterligare steg krävs för att hämta ett Azure-hanterat certifikat. Ett certifikat skapas under domän validerings processen. Du kan också använda ditt eget certifikat genom att integrera Azures främre dörr standard/Premium med din Key Vault.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i kompletterande användnings [**villkor för Microsoft Azure för hands**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan konfigurera HTTPS för din anpassade domän måste du först skapa en profil för standard/Premium för Azure-dörren. Mer information finns i [snabb start: skapa en Azure front dörr standard/Premium-profil](create-front-door-portal.md).

* Om du inte redan har en anpassad domän måste du först köpa en med en domän leverantör. Se exempelvis [Köpa ett anpassat domännamn](../../app-service/manage-custom-dns-buy-domain.md).

* Om du använder Azure som värd för dina [DNS-domäner](../../dns/dns-overview.md)måste du delegera DOMÄNens DNS-domännamn (Domain Name System) till en Azure DNS. Mer information finns i [delegera en domän till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Annars, om du använder en domän leverantör för att hantera DNS-domänen, måste du verifiera domänen manuellt genom att ange de begärda DNS-TXT-posterna.

## <a name="azure-managed-certificates"></a>Azure-hanterade certifikat

1. Under Inställningar för din Azure frontend-standard/Premium-profil väljer du **domäner** och väljer sedan **+ Lägg** till för att lägga till en ny domän.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Skärm bild av landnings sidan för domän konfiguration.":::

1. På sidan **Lägg till en domän** för *DNS-hantering* väljer du alternativet **Azure Managed DNS** . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Skärm bild som visar sidan Lägg till en domän med Azure Managed DNS valt.":::

1. Verifiera och koppla den anpassade domänen till en slut punkt genom att följa stegen i Aktivera [anpassad domän](how-to-add-custom-domain.md).

1. När den anpassade domänen har associerats till slut punkten distribueras ett Azure-hanterat certifikat till front dörren. Den här processen kan ta några minuter att slutföra.

## <a name="using-your-own-certificate"></a>Använda ditt eget certifikat

Du kan också välja att använda ditt eget TLS-certifikat. Det här certifikatet måste importeras till ett Azure Key Vault innan du kan använda det med Azures främre dörr standard/Premium. Se [Importera ett certifikat](../../key-vault/certificates/tutorial-import-certificate.md) till Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Förbered ditt Azure Key Vault-konto och certifikat
 
1. Du måste ha ett Azure Key Vault konto som körs under samma prenumeration som din Azure-frontend-standard/Premium som du vill aktivera anpassad HTTPS. Skapa ett Azure Key Vault-konto om du inte redan har ett.

    > [!WARNING]
    > Azures front dörr stöder för närvarande endast Key Vault-konton i samma prenumeration som konfigurationen av den främre dörren. Om du väljer en Key Vault under en annan prenumeration än din Azure-front dörr standard/Premium uppstår ett haveri.

1. Om du redan har ett certifikat kan du ladda upp det direkt till ditt Azure Key Vault-konto. Annars skapar du ett nytt certifikat direkt via Azure Key Vault från en av de partner certifikat utfärdare som Azure Key Vault integrera med. Ladda upp certifikatet som ett **certifikat** objekt, i stället för en **hemlighet**.

    > [!NOTE]
    > För ditt eget TLS/SSL-certifikat stöder inte front dörren certifikat med EG-algoritmer för kryptografi.

#### <a name="register-azure-front-door"></a>Registrera Azures front dörr

Registrera tjänstens huvud namn för Azures frontend-dörr som en app i din Azure Active Directory via PowerShell.

> [!NOTE]
> Den här åtgärden kräver globala administratörs behörigheter och behöver bara utföras **en gång** per klient.

1. Om det behövs installerar du [Azure PowerShell](/powershell/azure/install-az-ps) i PowerShell på den lokala datorn.

1. Kör följande kommando i PowerShell:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Bevilja åtkomst till Azure-dörren till ditt nyckel valv
 
Ge Azures frontend-behörighet för att komma åt certifikaten i ditt Azure Key Vault-konto.

1. Välj **åtkomst principer** under Inställningar i ditt nyckel valv konto. Välj sedan **Lägg till ny** för att skapa en ny princip.

1. I **Välj huvud konto** söker du efter **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** och väljer sedan * * Microsoft. AzureFrontDoor-CDN * *. Klicka på **Välj**.

1. I **hemliga behörigheter** väljer du **Hämta** för att tillåta front dörr att hämta certifikatet.

1. I **certifikat behörigheter** väljer du **Hämta** för att tillåta front dörren att hämta certifikatet.

1. Välj **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Välj det certifikat som ska användas för Azures frontend-dörr
 
1. Gå tillbaka till din Azures främre dörr standard/Premium i portalen.

1. Navigera till **hemligheter** under *Inställningar* och välj **Lägg till certifikat**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Skärm bild av Azures landnings sida för front dörr.":::

1. På sidan **Lägg till certifikat** markerar du kryss rutan för det certifikat som du vill lägga till i Azures främre dörr standard/Premium. Låt versions valet vara "senaste" och välj **Lägg till**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Skärm bild av sidan Lägg till certifikat.":::

1. När certifikatet har allokerats kan du använda det när du lägger till en ny anpassad domän.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Skärm bild av certifikat har lagts till i hemligheter.":::

1. Navigera till **domäner** under *inställning* och välj **+ Lägg** till för att lägga till en ny anpassad domän. På sidan **Lägg till en domän** väljer du "ta med ditt eget certifikat (BYOC)" för *https*. För *hemlighet* väljer du det certifikat som du vill använda från List rutan. 

    > [!NOTE]
    > Det valda certifikatet måste ha ett eget namn (CN) som är samma som den anpassade domän som läggs till.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Skärm bild av sidan Lägg till en anpassad domän med HTTPS.":::

1. Verifiera certifikatet genom att följa anvisningarna på skärmen. Koppla sedan den nyligen skapade anpassade domänen till en slut punkt enligt beskrivningen i [skapa en anpassad domän](how-to-add-custom-domain.md) guide.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Ändra från Azure Managed för att ta med ditt eget certifikat (BYOC)

1. Du kan ändra ett befintligt Azure-hanterat certifikat till ett användar hanterat certifikat genom att välja certifikat tillstånd för att öppna sidan **certifikat information** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Skärm bild av certifikat tillstånd på domäner landnings sida." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. På sidan **certifikat information** kan du ändra från alternativet "Azure Managed" till "ta med ditt eget certifikat (BYOC)". Följ sedan samma steg som tidigare för att välja ett certifikat. Välj **Uppdatera** för att ändra det associerade certifikatet med en domän.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Skärm bild av sidan certifikat information.":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [cachelagring med Azure frontend standard/Premium](concept-caching.md).
