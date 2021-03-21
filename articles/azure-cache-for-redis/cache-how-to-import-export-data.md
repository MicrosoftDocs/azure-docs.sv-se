---
title: Importera och exportera data i Azure cache för Redis
description: Lär dig hur du importerar och exporterar data till och från Blob Storage med ditt Premium Azure-cache för Redis-instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96327516"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importera och exportera data i Azure cache för Redis
Import/export är en Azure-cache för Redis data hanterings åtgärd, som gör att du kan importera data till Azure cache för Redis eller exportera data från Azure cache för Redis genom att importera och exportera en Azure cache för Redis Database (RDB) ögonblicks bild från en Premium-cache till en BLOB i ett Azure Storage-konto.

- **Export** – du kan exportera Azure-cachen för Redis RDB-ögonblicksbilder till en sid-blob.
- **Importera** – du kan importera Azure-cachen för Redis RDB-ögonblicksbilder från antingen en Page BLOB eller en Block-Blob.

Genom att importera/exportera kan du migrera mellan olika Azure cache för Redis-instanser eller fylla i cachen med data innan du använder.

Den här artikeln innehåller en guide för att importera och exportera data med Azure cache för Redis och ger svar på vanliga frågor.

> [!IMPORTANT]
> Import/export är endast tillgängligt för cacheminnen på [Premium-nivån](cache-overview.md#service-tiers) .
>
>

## <a name="import"></a>Importera
Import kan användas för att hämta Redis-kompatibla RDB-filer från en Redis-server som körs i molnet eller i miljön, inklusive Redis som körs på Linux, Windows eller någon annan moln leverantör, till exempel Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läser Azure cache för Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cachen.

> [!NOTE]
> Innan du påbörjar importen kontrollerar du att din Redis-databas (RDB) eller filer laddas upp till Page-eller block-blobar i Azure Storage, i samma region och prenumeration som Azure-cachen för Redis-instans. Mer information finns i [Kom igång med Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md). Om du har exporterat din RDB-fil med hjälp av funktionen [Azure cache för Redis-export](#export) , lagras RDB-filen redan i en sid-blob och är redo att importeras.
>
>

1. Om du vill importera en eller flera exporterade cache-blobbar [bläddrar du till din cache](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure Portal och klickar på **Importera data** från **resurs-menyn**.

    ![Importera data](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klicka på **Välj BLOB (er)** och välj det lagrings konto som innehåller de data som ska importeras.

    ![Välj lagrings konto](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klicka på den behållare som innehåller de data som ska importeras.

    ![Välj behållare](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Välj en eller flera blobbar som ska importeras genom att klicka på ytan till vänster om BLOB-namnet och klicka sedan på **Välj**.

    ![Välj blobbar](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Klicka på **Importera** för att starta import processen.

   > [!IMPORTANT]
   > Cachen är inte tillgänglig för cache-klienter under importen och alla befintliga data i cachen tas bort.
   >
   >

    ![Importera](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Du kan övervaka förloppet för import åtgärden genom att följa meddelanden från Azure Portal eller genom att granska händelserna i [gransknings loggen](../azure-resource-manager/management/view-activity-logs.md).

    ![Import förlopp](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportera
Med export kan du exportera data som lagras i Azure cache för Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure-cache för Redis-instans till en annan eller till en annan redis-server. Under exporten skapas en temporär fil på den virtuella datorn som är värd för Azure cache för Redis-Server-instansen och filen laddas upp till det angivna lagrings kontot. När export åtgärden har slutförts med statusen lyckad eller misslyckad tas den temporära filen bort.

1. Om du vill exportera det aktuella innehållet i cachen till lagring [bläddrar du till din cache](cache-configure.md#configure-azure-cache-for-redis-settings) i Azure Portal och klickar på **Exportera data** på **resurs-menyn**.

    ![I navigerings fönstret för contoso5premium markeras alternativet Exportera data i listan administration.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klicka på **Välj lagrings behållare** och välj önskat lagrings konto. Lagrings kontot måste finnas i samma prenumeration och region som din cache.

   > [!IMPORTANT]
   > Export fungerar med Page blobbar som stöds av både klassiska och Resource Manager-lagringsenheter, men stöds inte av Blob Storage-konton just nu. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).
   >

    ![Lagringskonto](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Välj önskad BLOB-behållare och klicka på **Välj**. Om du vill använda en ny behållare klickar du på **Lägg till behållare** för att lägga till den först och väljer den sedan i listan.

    ![På behållare för contoso55 markeras alternativet + container. Det finns en behållare i listan, cachesaves och den markeras och markeras. Markerings alternativet är markerat och markerat.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Ange ett **BLOB Name-prefix** och klicka på **Exportera** för att starta export processen. BLOB Name-prefixet används för att ge prefixet namnen på de filer som genereras av den här export åtgärden.

    ![Exportera](./media/cache-how-to-import-export-data/cache-export-data.png)

    Du kan övervaka förloppet för export åtgärden genom att följa meddelanden från Azure Portal eller genom att granska händelserna i [gransknings loggen](../azure-resource-manager/management/view-activity-logs.md).

    ![Export av data slutförd](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Cacheminnen är tillgängliga för användning under export processen.

## <a name="importexport-faq"></a>Vanliga frågor och svar om import/export
Det här avsnittet innehåller vanliga frågor och svar om import/export-funktionen.

* [Vilka pris nivåer kan användas för att importera/exportera?](#what-pricing-tiers-can-use-importexport)
* [Kan jag importera data från en Redis-Server?](#can-i-import-data-from-any-redis-server)
* [Vilka RDB-versioner kan jag importera?](#what-rdb-versions-can-i-import)
* [Är mitt cacheminne tillgängligt under en import/export-åtgärd?](#is-my-cache-available-during-an-importexport-operation)
* [Kan jag använda import/export med Redis-kluster?](#can-i-use-importexport-with-redis-cluster)
* [Hur fungerar import/export med en anpassad databas inställning?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Hur skiljer sig import/export från Redis beständighet?](#how-is-importexport-different-from-redis-persistence)
* [Kan jag automatisera import/export med PowerShell, CLI eller andra hanterings klienter?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Jag har fått ett tids gräns fel under import/export-åtgärden. Vad innebär det?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Jag fick ett fel när jag exporterade data till Azure Blob Storage. Vad hände?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Vilka pris nivåer kan användas för att importera/exportera?
Import/export är endast tillgängligt på Premium pris nivån.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan jag importera data från en Redis-Server?
Ja, förutom att importera data som exporter ATS från Azure cache för Redis-instanser, kan du importera RDB-filer från alla Redis-servrar som körs i molnet eller miljön, till exempel Linux-, Windows-eller moln leverantörer som Amazon Web Services. Det gör du genom att ladda upp RDB-filen från den önskade Redis-servern till en sida eller block-BLOB i ett Azure Storage konto och sedan importera den till din Premium Azure-cache för Redis-instans. Du kanske till exempel vill exportera data från produktions-cachen och importera dem till ett cacheminne som används som en del av en utvecklings miljö för testning eller migrering.

> [!IMPORTANT]
> Om du vill importera data som exporter ATS från andra Redis-servrar än Azure cache för Redis när du använder en Page BLOB måste sidans BLOB-storlek justeras på en 512 byte-gränser. Exempel kod för att utföra en obligatorisk byte-utfyllnad finns i [exempel på sidan BLOB-uppladdning](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Vilka RDB-versioner kan jag importera?

Azure cache för Redis stöder RDB-import via RDB version 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Är mitt cacheminne tillgängligt under en import/export-åtgärd?
* **Export** -cache-minnet är tillgängligt och du kan fortsätta att använda cachen under en export åtgärd.
* **Importera** – cacheminnen blir otillgängliga när en import åtgärd startar och blir tillgänglig för användning när importen har slutförts.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan jag använda import/export med Redis-kluster?
Ja, och du kan importera/exportera mellan en klustrad cache och en icke-klustrad cache. Eftersom Redis-kluster [endast stöder databas 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), importeras inga data i andra databaser än 0. När klustrade cachelagrade data importeras omdistribueras nycklarna mellan Shards i klustret.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hur fungerar import/export med en anpassad databas inställning?
Vissa pris nivåer har olika [begränsningar för databaser](cache-configure.md#databases), så det finns några saker att tänka på när du importerar om du har konfigurerat ett anpassat värde för `databases` inställningen under cache-skapandet.

* När du importerar till en pris nivå med en lägre `databases` gräns än den nivå som du exporterade från:
  * Om du använder standard antalet `databases` , som är 16 för alla pris nivåer, går inga data förlorade.
  * Om du använder ett anpassat antal `databases` som ligger inom gränserna för nivån som du importerar till går inga data förlorade.
  * Om dina exporterade data innehåller data i en databas som överskrider gränserna för den nya nivån, importeras inte data från de högre databaserna.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hur skiljer sig import/export från Redis beständighet?
Med Azure cache för Redis beständighet kan du spara data som lagras i Redis till Azure Storage. När persistence har kon figurer ATS sparar Azure cache för Redis en ögonblicks bild av Azure-cachen för Redis i ett Redis-binärformat till disk baserat på en konfigurerbar säkerhets kopierings frekvens. Om en oåterkallelig händelse inträffar som inaktiverar både den primära cachen och replik cachen återställs cache-data automatiskt med den senaste ögonblicks bilden. Mer information finns i [så här konfigurerar du data beständighet för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).

Med import/export kan du hämta data till eller exportera från Azure cache för Redis. Den konfigurerar inte säkerhets kopiering och återställning med Redis persistence.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan jag automatisera import/export med PowerShell, CLI eller andra hanterings klienter?
Ja, för PowerShell-instruktioner, se [så här importerar du en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) och [exporterar en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Jag har fått ett tids gräns fel under import/export-åtgärden. Vad betyder det?
Om du är kvar på bladet **Importera data** eller **Exportera data** i mer än 15 minuter innan du påbörjar åtgärden, visas ett fel meddelande som liknar följande exempel:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Lös problemet genom att starta import-eller export åtgärden innan 15 minuter har gått ut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Jag fick ett fel när jag exporterade data till Azure Blob Storage. Vad hände?
Export fungerar endast med RDB-filer som lagras som Page blobbar. Andra BLOB-typer stöds inte för närvarande, inklusive Blob Storage-konton med frekventa och låg frekventa nivåer. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis-tjänst nivåer](cache-overview.md#service-tiers)