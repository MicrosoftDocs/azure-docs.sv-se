---
title: Konfigurera en anslutnings sträng
titleSuffix: Azure Storage
description: Konfigurera en anslutnings sträng för ett Azure Storage-konto. En anslutnings sträng innehåller den information som krävs för att ge åtkomst till ett lagrings konto från ditt program vid körning med hjälp av autentisering med delad nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d7ca1707c89f03683960822591065143d3f8aa4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783886"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfigurera Azure Storage-anslutningssträngar

En anslutnings sträng innehåller den autentiseringsinformation som krävs för att ditt program ska kunna komma åt data i ett Azure Storage konto vid körning med hjälp av autentisering med delad nyckel. Du kan konfigurera anslutnings strängar för att:

* Anslut till Azurite Storage-emulatorn.
* Få åtkomst till ett lagrings konto i Azure.
* Få åtkomst till angivna resurser i Azure via en signatur för delad åtkomst (SAS).

Information om hur du visar åtkomst nycklar för ditt konto och kopierar en anslutnings sträng finns i [Hantera lagrings kontots åtkomst nycklar](storage-account-keys-manage.md).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>Lagra en anslutnings sträng

Ditt program måste ha åtkomst till anslutnings strängen vid körning för att auktorisera begär Anden som görs till Azure Storage. Du har flera alternativ för att lagra anslutnings strängen:

* Du kan lagra anslutnings strängen i en miljö variabel.
* Ett program som körs på Skriv bordet eller på en enhet kan lagra anslutnings strängen i en **app.config** eller **web.config** fil. Lägg till anslutnings strängen i avsnittet **appSettings** i de här filerna.
* Ett program som körs i en Azure-molnbaserad tjänst kan lagra anslutnings strängen i [Azure Service Configuration schema-filen (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)). Lägg till anslutnings strängen i **ConfigurationSettings** -avsnittet i tjänst konfigurations filen.

Genom att lagra anslutnings strängen i en konfigurations fil kan du enkelt uppdatera anslutnings strängen för att växla mellan [Azurite](../common/storage-use-azurite.md) och ett Azure Storage-konto i molnet. Du behöver bara redigera anslutnings strängen för att peka på mål miljön.

Du kan använda [Microsoft Azures Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) för att få åtkomst till anslutnings strängen vid körning oavsett var programmet körs.

## <a name="configure-a-connection-string-for-azurite"></a>Konfigurera en anslutnings sträng för Azurite

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om Azurite finns i [använda Azurite-emulatorn för lokal Azure Storage utveckling](../common/storage-use-azurite.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurera en anslutnings sträng för ett Azure Storage-konto

Använd följande format för att skapa en anslutnings sträng för ditt Azure Storage-konto. Ange om du vill ansluta till lagrings kontot via HTTPS (rekommenderas) eller HTTP, Ersätt `myAccountName` med namnet på ditt lagrings konto och Ersätt `myAccountKey` med din konto åtkomst nyckel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Din anslutnings sträng kan till exempel se ut ungefär så här:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Även om Azure Storage stöder både HTTP och HTTPS i en anslutnings sträng, *rekommenderas https starkt*.

> [!TIP]
> Du kan hitta lagrings kontots anslutnings strängar i [Azure Portal](https://portal.azure.com). Gå till **Inställningar**  >  **åtkomst nycklar** på ditt lagrings kontos meny blad om du vill se anslutnings strängar för både primära och sekundära åtkomst nycklar.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Skapa en anslutnings sträng med hjälp av en signatur för delad åtkomst

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Skapa en anslutnings sträng för en explicit lagrings slut punkt

Du kan ange explicita tjänst slut punkter i anslutnings strängen i stället för att använda standard slut punkterna. Om du vill skapa en anslutnings sträng som anger en explicit slut punkt anger du den fullständiga tjänst slut punkten för varje tjänst, inklusive protokoll specifikation (HTTPS (rekommenderas) eller HTTP) i följande format:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Ett scenario där du kanske vill ange en explicit slut punkt är när du har mappat din Blob Storage-slutpunkt till en [anpassad domän](../blobs/storage-custom-domain-name.md). I så fall kan du ange din anpassade slut punkt för Blob Storage i anslutnings strängen. Du kan också ange standard slut punkter för de andra tjänsterna om programmet använder dem.

Här är ett exempel på en anslutnings sträng som anger en explicit slut punkt för Blob Service:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

I det här exemplet anges explicita slut punkter för alla tjänster, inklusive en anpassad domän för Blob Service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Slut punkts värden i en anslutnings sträng används för att konstruera begär ande-URI: er till lagrings tjänsterna och diktera formen för alla URI: er som returneras till din kod.

Om du har mappat en lagrings slut punkt till en anpassad domän och utelämnar slut punkten från en anslutnings sträng, kommer du inte att kunna använda anslutnings strängen för att komma åt data i den tjänsten från din kod.

Mer information om hur du konfigurerar en anpassad domän för Azure Storage finns i [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](../blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Värdena för tjänst slut punkten i anslutnings strängarna måste vara välformulerade URI: er, inklusive `https://` (rekommenderas) eller `http://` .

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Skapa en anslutnings sträng med ett slut punkts suffix

Om du vill skapa en anslutnings sträng för en lagrings tjänst i regioner eller instanser med olika slut punkts suffix, till exempel för Azure Kina 21Vianet eller Azure Government, använder du följande anslutnings sträng format. Ange om du vill ansluta till lagrings kontot via HTTPS (rekommenderas) eller HTTP, Ersätt `myAccountName` med namnet på ditt lagrings konto, Ersätt `myAccountKey` med kontots åtkomst nyckel och Ersätt `mySuffix` med URI-suffixet:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Här är ett exempel på en anslutnings sträng för lagrings tjänster i Azure Kina 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Parsar en anslutnings sträng

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Använd Azurite-emulatorn för lokal Azure Storage utveckling](../common/storage-use-azurite.md)
* [Azure Storage Explorer](storage-explorers.md)
* [Använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md)