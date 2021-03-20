---
title: Konfigurera Transport Layer Security (TLS) för ett klient program
titleSuffix: Azure Storage
description: Konfigurera ett klient program för att kommunicera med Azure Storage med en lägsta version av Transport Layer Security (TLS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89001815"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Konfigurera Transport Layer Security (TLS) för ett klient program

Av säkerhets synpunkt kan ett Azure Storage konto kräva att klienterna använder en lägsta version av Transport Layer Security (TLS) för att skicka begär Anden. Anrop till Azure Storage Miss fungerar om klienten använder en version av TLS som är lägre än den lägsta version som krävs. Om t. ex. ett lagrings konto kräver TLS 1,2 kommer en begäran som skickas av en klient som använder TLS 1,1 att Miss sen.

Den här artikeln beskriver hur du konfigurerar ett klient program för att använda en viss version av TLS. Information om hur du konfigurerar en minimi krav version av TLS för ett Azure Storage konto finns i [Konfigurera lägsta nödvändiga version av Transport Layer Security (TLS) för ett lagrings konto](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Konfigurera klientens TLS-version

För att en klient ska kunna skicka en begäran med en viss version av TLS måste operativ systemet ha stöd för den versionen.

I följande exempel visas hur du ställer in klientens TLS-version på 1,2 från PowerShell eller .NET. .NET Framework som används av klienten måste ha stöd för TLS 1,2. Mer information finns i [stöd för TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Följande exempel visar hur du aktiverar TLS 1,2 i en PowerShell-klient:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Följande exempel visar hur du aktiverar TLS 1,2 i en .NET-klient med hjälp av version 12 av Azure Storage-klient biblioteket:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Följande exempel visar hur du aktiverar TLS 1,2 i en .NET-klient med version 11 av Azure Storage klient biblioteket:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Verifiera den TLS-version som används av en klient

För att verifiera att den angivna versionen av TLS användes av klienten för att skicka en begäran kan du använda [Fiddler](https://www.telerik.com/fiddler) eller ett liknande verktyg. Öppna Fiddler för att börja samla in klient nätverks trafik och kör sedan något av exemplen i föregående avsnitt. Titta på Fiddler-spåret för att bekräfta att rätt version av TLS användes för att skicka begäran, som du ser i följande bild.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Skärm bild som visar Fiddler-spårning som visar vilken TLS-version som används på begäran":::

## <a name="next-steps"></a>Nästa steg

- [Konfigurera den tidigaste version av Transport Layer Security (TLS) som krävs för ett lagrings konto](transport-layer-security-configure-minimum-version.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
