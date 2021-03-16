---
title: Använd kund-hanterade nycklar för att kryptera data i Azure HPC-cache
description: Använda Azure Key Vault med Azure HPC cache för att kontrol lera åtkomsten till krypterings nycklar i stället för att använda Microsoft-hanterade standard krypterings nycklar
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: e8f1b3fffefcdf1d2ec8bd3e9b1aaea93697ca8a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471976"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Använd kund hanterade krypterings nycklar för Azure HPC cache

Du kan använda Azure Key Vault för att kontrol lera ägarskapet för de nycklar som används för att kryptera dina data i Azure HPC-cache. Den här artikeln förklarar hur du använder Kundhanterade nycklar för kryptering av cache-data.

> [!NOTE]
> Alla data som lagras i Azure, inklusive på cache-diskarna, krypteras i vila med hjälp av Microsoft-hanterade nycklar som standard. Du behöver bara följa stegen i den här artikeln om du vill hantera nycklarna som används för att kryptera dina data.

Azure HPC cache skyddas också av [värd kryptering för virtuella datorer](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) på de hanterade diskar som innehåller cachelagrade data, även om du lägger till en kund nyckel för cache-diskarna. Genom att lägga till en kundhanterad nyckel för Double Encryption får du en extra säkerhets nivå för kunder med höga säkerhets krav. Mer information finns [i kryptering på Server sidan av Azure disk Storage](../virtual-machines/disk-encryption.md) .

<!-- This feature is available only in some of the Azure regions where Azure HPC Cache is available. Refer to the [Region availability](hpc-cache-overview.md#region-availability) list for details. -->

Det finns tre steg för att aktivera kundhanterad nyckel kryptering för Azure HPC cache:

1. Konfigurera en Azure Key Vault för att lagra nycklarna.
1. När du skapar Azure HPC-cachen väljer du kund hanterad nyckel kryptering och anger nyckel valvet och nyckeln som ska användas.
1. När cachen har skapats kan du ge den åtkomst till nyckel valvet.

Krypteringen är inte helt konfigurerad förrän du har auktoriserat den från det nyligen skapade cacheminnet (steg 3). Detta beror på att du måste skicka cachens identitet till nyckel valvet för att göra den till en auktoriserad användare. Du kan inte göra detta innan du skapar cachen, eftersom identiteten inte finns förrän cachen har skapats.

När du har skapat cacheminnet kan du inte ändra mellan Kundhanterade nycklar och Microsoft-hanterade nycklar. Om din cache använder Kundhanterade nycklar kan du dock [ändra](#update-key-settings) krypterings nyckeln, nyckel versionen och nyckel valvet efter behov.

## <a name="understand-key-vault-and-key-requirements"></a>Förstå nyckel valv och nyckel krav

Nyckel valvet och nyckeln måste uppfylla dessa krav för att fungera med Azure HPC-cache.

Egenskaper för nyckel valv:

* **Prenumeration** – Använd samma prenumeration som används för cacheminnet.
* **Region** – nyckel valvet måste finnas i samma region som Azure HPC-cachen.
* **Pris nivå** – standard nivån är tillräcklig för användning med Azure HPC cache.
* **Mjuk borttagning** – Azure HPC cache aktiverar mjuk borttagning om det inte redan har kon figurer ATS i nyckel valvet.
* **Rensnings skydd** – rensnings skyddet måste vara aktiverat.
* **Åtkomst princip** – standardinställningarna är tillräckliga.
* **Nätverks anslutning** – Azure HPC-cache måste kunna komma åt nyckel valvet, oavsett vilka slut punkts inställningar du väljer.

Nyckel egenskaper:

* **Nyckel typ** – RSA
* **RSA-nyckelns storlek** – 2048
* **Aktive rad** – Ja

Åtkomst behörigheter för nyckel valv:

* Användaren som skapar Azure HPC-cachen måste ha behörigheter som motsvarar [rollen Key Vault Contributor](../role-based-access-control/built-in-roles.md#key-vault-contributor). Samma behörigheter krävs för att konfigurera och hantera Azure Key Vault.

  Läs [säker åtkomst till ett nyckel valv](../key-vault/general/secure-your-key-vault.md) för mer information.

## <a name="1-set-up-azure-key-vault"></a>1. Konfigurera Azure Key Vault

Du kan ställa in ett nyckel valv och en nyckel innan du skapar cachen eller göra det som en del av att skapa cache. Se till att resurserna uppfyller de krav som beskrivs [ovan](#understand-key-vault-and-key-requirements).

När du skapar en cache måste du ange ett valv, en nyckel och en nyckel version som ska användas för cachens kryptering.

Läs [Azure Key Vault-dokumentationen](../key-vault/general/overview.md) om du vill ha mer information.

> [!NOTE]
> Azure Key Vault måste använda samma prenumeration och vara i samma region som Azure HPC-cachen. Se till att den region du väljer [stöder funktionen Kundhanterade nycklar](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. skapa cachen med Kundhanterade nycklar aktiverade

Du måste ange krypterings nyckel källan när du skapar din Azure HPC-cache. Följ instruktionerna i [skapa en Azure HPC-cache](hpc-cache-create.md)och ange nyckel valvet och nyckeln på sidan **disk krypterings nycklar** . Du kan skapa ett nytt nyckel valv och en nyckel när du skapar cacheminnet.

> [!TIP]
> Om sidan **disk krypterings nycklar** inte visas kontrollerar du att cacheminnet finns i någon av de regioner som stöds.

Den användare som skapar cachen måste ha behörigheter som är lika med [rollen Key Vault deltagare](../role-based-access-control/built-in-roles.md#key-vault-contributor) eller högre.

1. Klicka på knappen för att aktivera privat hanterade nycklar. När du har ändrat den här inställningen visas inställningarna för nyckel valvet.

1. Klicka på **Välj ett nyckel valv** för att öppna sidan Val av nyckel. Välj eller skapa nyckel valvet och nyckeln för att kryptera data på den här cachens diskar.

   Om din Azure Key Vault inte visas i listan, kontrollerar du följande krav:

   * Är cachen i samma prenumeration som nyckel valvet?
   * Är cachen i samma region som nyckel valvet?
   * Finns det någon nätverks anslutning mellan Azure Portal och nyckel valvet?

1. När du har valt ett valv väljer du den enskilda nyckeln från de tillgängliga alternativen eller skapar en ny nyckel. Nyckeln måste vara en 2048-bitars RSA-nyckel.

1. Ange versionen för den valda nyckeln. Läs mer om versions hantering i [Azure Key Vault-dokumentationen](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Fortsätt med resten av specifikationerna och skapa cachen enligt beskrivningen i [skapa en Azure HPC-cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. auktorisera Azure Key Vault kryptering från cachen
<!-- header is linked from create article, update if changed -->

Efter några minuter visas den nya Azure HPC-cachen i din Azure Portal. Gå till **översikts** sidan för att ge den åtkomst till din Azure Key Vault och aktivera kundhanterad nyckel kryptering.

> [!TIP]
> Cachen kan visas i resurs listan innan meddelandena "distributions förfallet" rensas. Kontrol lera resurs listan efter en minut eller två i stället för att vänta på ett lyckat meddelande.

Den här två stegs processen är nödvändig eftersom Azure HPC-instansen behöver en identitet för att skicka till Azure Key Vault för auktorisering. Cache-identiteten finns inte förrän de första stegen för att skapa har slutförts.

> [!NOTE]
> Du måste auktorisera kryptering inom 90 minuter efter att du har skapat cacheminnet. Om du inte slutför det här steget blir cachen timeout och Miss lyckas. En misslyckad cache måste skapas på nytt, den kan inte åtgärdas.

I cachen visas statusen **väntar på nyckel**. Klicka på knappen **Aktivera kryptering** överst på sidan för att ge cachen åtkomst till det angivna nyckel valvet.

![skärm bild av sidan cache-översikt i portalen med markering på knappen Aktivera kryptering (översta raden) och status: väntar på nyckel](media/waiting-for-key.png)

Klicka på **Aktivera kryptering** och klicka sedan på **Ja** om du vill ge cachen behörighet att använda krypterings nyckeln. Den här åtgärden aktiverar även skydd för mjuk borttagning och rensning (om det inte redan är aktiverat) i nyckel valvet.

![skärm bild av sidan cache-översikt i portalen med ett informations meddelande överst som ber användaren att aktivera kryptering genom att klicka på Ja](media/enable-keyvault.png)

När cachen begär åtkomst till nyckel valvet kan det skapa och kryptera diskarna som lagrar cachelagrade data.

När du har auktoriserat krypteringen går Azure HPC-cachen igenom flera minuters installation för att skapa krypterade diskar och relaterad infrastruktur.

## <a name="update-key-settings"></a>Uppdatera nyckel inställningar

Du kan ändra nyckel valvet, nyckeln eller nyckel versionen för din cache från Azure Portal. Klicka på länken cache- **krypterings** inställningar för att öppna sidan **kund nyckel inställningar** .

Du kan inte ändra en cache mellan Kundhanterade nycklar och systemhanterade nycklar.

![skärm bild av sidan Inställningar för kund nycklar som nås genom att klicka på Inställningar > kryptering på sidan cache i Azure Portal](media/change-key-click.png)

Klicka på länken **ändra nyckel** och klicka sedan på **ändra nyckel valvet, nyckeln eller versionen** för att öppna nyckel väljaren.

![skärm bild av sidan "Välj nyckel från Azure Key Vault" med tre List rutor som du väljer Key Vault, nyckel och version](media/select-new-key.png)

Nyckel valv i samma prenumeration och samma region som denna cache visas i listan.

När du har valt de nya krypterings nyckel värden klickar du på **Välj**. En bekräftelse sida visas med de nya värdena. Klicka på **Spara** för att slutföra valet.

![skärm bild av sidan bekräftelse med knappen Spara längst upp till vänster](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Läs mer om Kundhanterade nycklar i Azure

De här artiklarna förklarar mer om hur du använder Azure Key Vault och Kundhanterade nycklar för att kryptera data i Azure:

* [Översikt över Azure Storage-kryptering](../storage/common/storage-service-encryption.md)
* [Disk kryptering med Kundhanterade nycklar](../virtual-machines/disk-encryption.md#customer-managed-keys) – dokumentation för att använda Azure Key Vault med hanterade diskar, vilket är ett liknande scenario för Azure HPC-cache

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure HPC cache och auktoriserad Key Vault-baserad kryptering fortsätter du att konfigurera din cache genom att ge den åtkomst till dina data källor.

* [Lägga till lagringsmål](hpc-cache-add-storage.md)