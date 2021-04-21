---
title: Använda kundmangerade nycklar för att kryptera data i Azure HPC Cache
description: Så här använder du Azure Key Vault med Azure HPC Cache för att styra krypteringsnyckelns åtkomst i stället för att använda microsoft-hanterade standardkrypteringsnycklar
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: ae4c52ec1390166eccb0e73d6f81a8553c445b2e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813298"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Använda kund hanterade krypteringsnycklar för Azure HPC Cache

Du kan använda Azure Key Vault för att styra ägarskapet för de nycklar som används för att kryptera data i Azure HPC Cache. Den här artikeln förklarar hur du använder kund hanterade nycklar för kryptering av cachedata.

> [!NOTE]
> Alla data som lagras i Azure, inklusive på cachediskarna, krypteras i vila med Microsoft-hanterade nycklar som standard. Du behöver bara följa stegen i den här artikeln om du vill hantera de nycklar som används för att kryptera dina data.

Azure HPC Cache skyddas också av [](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) VM-värdkryptering på de hanterade diskar som innehåller dina cachelagrade data, även om du lägger till en kundnyckel för cachediskarna. Genom att lägga till en kund hanterad nyckel för dubbel kryptering får du en extra säkerhetsnivå för kunder med höga säkerhetsbehov. Mer [information finns i Kryptering på serversidan av Azure-disklagring.](../virtual-machines/disk-encryption.md)

Det finns tre steg för att aktivera kundstyrd nyckelkryptering för Azure HPC Cache:

1. Konfigurera en Azure Key Vault att lagra nycklarna.
1. När du skapar Azure HPC Cache väljer du kund hanterad nyckelkryptering och anger det nyckelvalv och den nyckel som ska användas.
1. När cachen har skapats ger du den behörighet att komma åt nyckelvalvet.

Krypteringen är inte helt konfigurerad förrän du har auktoriserat den från den nyligen skapade cachen (steg 3). Det beror på att du måste skicka cachens identitet till nyckelvalvet för att göra det till en behörig användare. Du kan inte göra detta innan du skapar cachen, eftersom identiteten inte finns förrän cachen har skapats.

När du har skapat cacheminnet kan du inte ändra mellan kund hanterade nycklar och Microsoft-hanterade nycklar. Men om din cache använder kund [](#update-key-settings) hanterade nycklar kan du ändra krypteringsnyckeln, nyckelversionen och nyckelvalvet efter behov.

## <a name="understand-key-vault-and-key-requirements"></a>Förstå nyckelvalv och nyckelkrav

Nyckelvalvet och nyckeln måste uppfylla dessa krav för att fungera med Azure HPC Cache.

Egenskaper för nyckelvalv:

* **Prenumeration** – Använd samma prenumeration som används för cachen.
* **Region** – Nyckelvalvet måste finnas i samma region som Azure HPC Cache.
* **Prisnivå –** Standardnivån räcker för användning med Azure HPC Cache.
* **Mjuk borttagning** – Azure HPC Cache mjuk borttagning om den inte redan har konfigurerats i nyckelvalvet.
* **Rensningsskydd** – Rensningsskydd måste vara aktiverat.
* **Åtkomstprincip** – Standardinställningarna räcker.
* **Nätverksanslutning** – Azure HPC Cache måste kunna komma åt nyckelvalvet, oavsett vilka slutpunktsinställningar du väljer.

Nyckelegenskaper:

* **Nyckeltyp** – RSA
* **RSA-nyckelstorlek** – 2048
* **Aktiverad** – Ja

Åtkomstbehörigheter för nyckelvalv:

* Den användare som skapar Azure HPC Cache ha behörigheter som motsvarar den Key Vault [deltagarrollen](../role-based-access-control/built-in-roles.md#key-vault-contributor). Samma behörigheter krävs för att konfigurera och hantera Azure Key Vault.

  Mer [information finns i Säker åtkomst till](../key-vault/general/security-features.md) ett nyckelvalv.

## <a name="1-set-up-azure-key-vault"></a>1. Konfigurera Azure Key Vault

Du kan konfigurera ett nyckelvalv och en nyckel innan du skapar cacheminnet, eller göra det som en del av cacheskapandet. Se till att dessa resurser uppfyller kraven som beskrivs [ovan.](#understand-key-vault-and-key-requirements)

När cacheminnet skapas måste du ange ett valv, en nyckel och en nyckelversion som ska användas för cachens kryptering.

Läs dokumentationen [Azure Key Vault för](../key-vault/general/overview.md) mer information.

> [!NOTE]
> Den Azure Key Vault måste använda samma prenumeration och finnas i samma region som Azure HPC Cache. Kontrollera att den region som du väljer [stöder båda produkterna.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Skapa cachen med kund hanterade nycklar aktiverade

Du måste ange krypteringsnyckelkällan när du skapar Azure HPC Cache. Följ instruktionerna i [Skapa en Azure HPC Cache](hpc-cache-create.md)och ange nyckelvalvet och nyckeln på sidan **Diskkrypteringsnycklar.** Du kan skapa ett nytt nyckelvalv och en nyckel när du skapar cacheminnet.

> [!TIP]
> Om sidan **Diskkrypteringsnycklar** inte visas kontrollerar du att cacheminnet finns i någon av de [regioner som stöds.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

Den användare som skapar cachen måste ha behörigheter som motsvarar rollen [Key Vault deltagare](../role-based-access-control/built-in-roles.md#key-vault-contributor) eller högre.

1. Klicka på knappen för att aktivera privat hanterade nycklar. När du har ändrat den här inställningen visas inställningarna för nyckelvalvet.

1. Klicka **på Välj ett nyckelvalv** för att öppna sidan för nyckelval. Välj eller skapa nyckelvalvet och nyckeln för kryptering av data på cacheminnets diskar.

   Om ditt Azure Key Vault inte visas i listan kontrollerar du följande krav:

   * Finns cachen i samma prenumeration som nyckelvalvet?
   * Finns cachen i samma region som nyckelvalvet?
   * Finns det nätverksanslutning mellan Azure Portal och nyckelvalvet?

1. När du har valt ett valv väljer du den enskilda nyckeln bland de tillgängliga alternativen eller skapar en ny nyckel. Nyckeln måste vara en 2 048-bitars RSA-nyckel.

1. Ange versionen för den valda nyckeln. Läs mer om versionshantering i [dokumentationen Azure Key Vault .](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)

Fortsätt med resten av specifikationerna och skapa cachen enligt beskrivningen i [Skapa en Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Auktorisera Azure Key Vault kryptering från cachen
<!-- header is linked from create article, update if changed -->

Efter några minuter visas den nya Azure HPC Cache i din Azure Portal. Gå till sidan **Översikt för** att ge den åtkomst till Azure Key Vault och aktivera kundstyrd nyckelkryptering.

> [!TIP]
> Cacheminnet kan visas i resurslistan innan "distributionen pågår" rensas. Kontrollera resurslistan efter en minut eller två i stället för att vänta på ett meddelande.

Den här tvåstegsprocessen är nödvändig eftersom Azure HPC Cache-instansen behöver en identitet för att skicka till Azure Key Vault för auktorisering. Cacheidentiteten finns inte förrän dess första steg har skapats.

> [!NOTE]
> Du måste auktorisera krypteringen inom 90 minuter efter att du har skapat cachen. Om du inte slutför det här steget kommer cacheminnet att få en time out och misslyckas. En misslyckad cache måste skapas på nytt, den kan inte åtgärdas.

Cachen visar statusen **Väntar på nyckeln**. Klicka på **knappen Aktivera** kryptering överst på sidan för att ge cachen åtkomst till det angivna nyckelvalvet.

![skärmbild av översiktssidan för cache i portalen med markering på knappen Aktivera kryptering (översta raden) och Status: Väntar på nyckel](media/waiting-for-key.png)

Klicka **på Aktivera kryptering** och klicka sedan på knappen **Ja** för att auktorisera cachen att använda krypteringsnyckeln. Den här åtgärden aktiverar även mjuk borttagning och rensningsskydd (om det inte redan är aktiverat) i nyckelvalvet.

![skärmbild av översiktssidan för cache i portalen med ett banderollmeddelande längst upp där användaren uppmanas att aktivera kryptering genom att klicka på Ja](media/enable-keyvault.png)

När cachen begär åtkomst till nyckelvalvet kan den skapa och kryptera de diskar som lagrar cachelagrade data.

När du har auktoriserat Azure HPC Cache flera minuter av installationen för att skapa krypterade diskar och relaterad infrastruktur.

## <a name="update-key-settings"></a>Uppdatera nyckelinställningar

Du kan ändra nyckelvalv, nyckel eller nyckelversion för ditt cacheminne från Azure Portal. Klicka på länken **Krypteringsinställningar** för cachen för att öppna **sidan Kundnyckelinställningar.**

Du kan inte ändra ett cacheminne mellan kund-hanterade nycklar och system-hanterade nycklar.

![skärmbild av sidan "Inställningar för kundnycklar" som nås genom att klicka på Inställningar > kryptering från cachesidan i Azure Portal](media/change-key-click.png)

Klicka på **länken Ändra** nyckel och klicka sedan på **Ändra nyckelvalv, nyckel eller version för** att öppna nyckelväljaren.

![skärmbild av sidan "select key from Azure Key Vault" (Välj nyckel från en Azure Key Vault) med tre listr listringsväljare för att välja nyckelvalv, nyckel och version](media/select-new-key.png)

Nyckelvalv i samma prenumeration och i samma region som cachen visas i listan.

När du har valt de nya krypteringsnyckelvärdena klickar du **på Välj**. En bekräftelsesida visas med de nya värdena. Klicka **på** Spara för att slutföra valet.

![skärmbild av bekräftelsesidan med knappen Spara längst upp till vänster](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Läs mer om kund hanterade nycklar i Azure

De här artiklarna förklarar mer om Azure Key Vault och kundbaserade nycklar för att kryptera data i Azure:

* [Översikt över Azure Storage-kryptering](../storage/common/storage-service-encryption.md)
* [Diskkryptering med kund hanterade nycklar](../virtual-machines/disk-encryption.md#customer-managed-keys) – Dokumentation för att använda Azure Key Vault med hanterade diskar, vilket är ett liknande scenario som Azure HPC Cache

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure HPC Cache och Key Vault-baserad kryptering fortsätter du att konfigurera cachen genom att ge den åtkomst till dina datakällor.

* [Lägga till lagringsmål](hpc-cache-add-storage.md)
