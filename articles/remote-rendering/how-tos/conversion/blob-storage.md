---
title: Använda Azure-Blob Storage för modellkonvertering
description: Beskriver vanliga steg för att konfigurera och använda Blob Storage för modell konvertering.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507525"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Använda Azure-Blob Storage för modellkonvertering

[Modell konverterings](model-conversion.md) tjänsten kräver åtkomst till Azure-Blob Storage så att den kan hämta indata och lagra utdata. I den här artikeln beskrivs hur du utför de vanligaste stegen.

## <a name="prepare-azure-storage-accounts"></a>Förbereda Azure Storage-konton

- Skapa ett lagrings konto (StorageV2)
- Skapa en BLOB-behållare för indata i lagrings kontot (till exempel med namnet "arrinput")
- Skapa en utgående BLOB-behållare i lagrings kontot (till exempel med namnet "arroutput")

> [!TIP]
> Stegvisa instruktioner för hur du konfigurerar ditt lagrings konto finns i [snabb start: konvertera en modell för åter givning](../../quickstarts/convert-model.md)

Att skapa lagrings kontot och blob-behållarna kan göras med något av följande verktyg:

- [Azure-portalen](https://portal.azure.com)
- [AZ kommando rad](/cli/azure/install-azure-cli)
- [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)
- SDK: er (C#, python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Se till att Azure Remote rendering kan komma åt ditt lagrings konto

Azure-fjärrRending måste hämta modell data från ditt lagrings konto och skriva tillbaka data till den.

Du kan bevilja åtkomst till Azure Remote rendering till ditt lagrings konto på följande två sätt:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Anslut ditt Azure Storage-konto med ditt Azure-konto för fjärrrendering

Följ stegen i avsnittet [skapa ett konto](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>Hämta SAS för lagrings behållare

Lagrade Access-signaturer (SAS) används för att bevilja Läs åtkomst för indata och skriv åtkomst för utdata. Vi rekommenderar att du genererar nya URI: er varje gången en modell konverteras. Eftersom URI: er upphör att gälla efter en viss tid kan det vara risk att programmet slutar svara för en längre varaktighet.

Information om SAS finns i [SAS-dokumentationen](../../../storage/common/storage-sas-overview.md).

En SAS-URI kan genereras med hjälp av en av:

- AZ PowerShell-modul
  - Se [PowerShell-skripten](../../samples/powershell-example-scripts.md)
- [AZ kommando rad](/cli/azure/install-azure-cli)
- [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)
  - Högerklicka på container "Get Shared Access Signature" (Läs, lista åtkomst för inmatnings behållare, skriv åtkomst för behållaren utdata)
- SDK: er (C#, python...)

Ett exempel på hur du använder signaturer för delad åtkomst i till gångs konvertering visas i Conversion.ps1 av [PowerShell-exempel skripten](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Ladda upp en ingångs modell

Om du vill börja konvertera en modell måste du ladda upp den med något av följande alternativ:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) – ett bekvämt gränssnitt för att ladda upp/ladda ned/hantera filer på Azure Blob Storage
- [Kommando rad i Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell-modul](/powershell/azure/install-az-ps)
  - Se [PowerShell-skripten](../../samples/powershell-example-scripts.md)
- [Använda en Storage SDK (python, C#...)](../../../storage/index.yml)
- [Använda Azure Storage REST-API: er](/rest/api/storageservices/blob-service-rest-api)

Ett exempel på hur du överför data för konvertering hittar du i Conversion.ps1 av [PowerShell-exempel skripten](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Hämta en SAS-URI för den konverterade modellen

Det här steget liknar [hämtning av SAS för lagrings behållarna](#retrieve-sas-for-the-storage-containers). Men den här gången måste du hämta en SAS-URI för modell filen som skrevs till behållaren utdata.

Om du till exempel vill hämta en SAS-URI via [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)högerklickar du på modell filen och väljer Hämta signatur för delad åtkomst.

Du behöver en signatur för delad åtkomst (SAS) för att läsa in modeller om du inte har anslutit ditt lagrings konto till ditt Azure Remote rendering-konto. Du kan lära dig hur du ansluter ditt konto i [skapa ett konto](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera modell konverteringen](configure-model-conversion.md)
- [Modell konverterings REST API](conversion-rest-api.md)
