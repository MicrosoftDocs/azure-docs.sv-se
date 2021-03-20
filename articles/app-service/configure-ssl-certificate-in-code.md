---
title: Använd ett TLS/SSL-certifikat i kod
description: Lär dig hur du använder klient certifikat i din kod. Autentisera med fjär resurser med ett klient certifikat eller kör kryptografiska uppgifter med dem.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93126047"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Använda ett TLS-/SSL-certifikat i koden i Azure App Service

I program koden kan du komma åt [offentliga eller privata certifikat som du lägger till i App Service](configure-ssl-certificate.md). Din app-kod kan fungera som en klient och komma åt en extern tjänst som kräver certifikatautentisering, eller så kan den behöva utföra kryptografiska uppgifter. Den här instruktions guiden visar hur du använder offentliga eller privata certifikat i program koden.

Den här metoden för att använda certifikat i din kod använder TLS-funktionen i App Service, vilket kräver att din app är på **Basic** -nivå eller högre. Om din app finns på **kostnads fri** eller **delad** nivå kan du [inkludera certifikat filen i din app-lagringsplats](#load-certificate-from-file).

När du låter App Service hantera TLS/SSL-certifikat kan du underhålla certifikaten och program koden separat och skydda känsliga data.

## <a name="prerequisites"></a>Förutsättningar

För att följa den här instruktions guiden:

- [Skapa en App Service-app](./index.yml)
- [Lägg till ett certifikat i appen](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Hitta tumavtrycket

I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer du **app Services** på menyn till vänster  >  **\<app-name>** .

Välj **TLS/SSL-inställningar** i den vänstra navigeringen i din app och välj sedan **privat nyckel certifikat (. pfx)** eller **certifikat för offentlig nyckel (. cer)**.

Hitta det certifikat som du vill använda och kopiera tumavtrycket.

![Kopiera certifikatets tumavtryck](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Gör certifikatet tillgängligt

Om du vill komma åt ett certifikat i din app-kod lägger du till dess tumavtryck i `WEBSITE_LOAD_CERTIFICATES` appens inställning genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Ange värdet till om du vill göra alla dina certifikat tillgängliga `*` .

## <a name="load-certificate-in-windows-apps"></a>Läs in certifikat i Windows-appar

`WEBSITE_LOAD_CERTIFICATES`App-inställningen gör de angivna certifikaten tillgängliga för din Windows-värdbaserade app i Windows certifikat Arkiv, i [aktuell User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

I C#-koden får du åtkomst till certifikatet med tumavtrycket för certifikatet. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

I Java-kod får du åtkomst till certifikatet från "Windows-MY"-butiken med hjälp av fältet eget namn på certifikat mottagare (se [certifikat för offentlig nyckel](https://en.wikipedia.org/wiki/Public_key_certificate)). Följande kod visar hur du läser in ett privat nyckel certifikat:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

För språk som inte stöder eller som inte erbjuder stöd för Windows certifikat Arkiv, se [Läs in certifikat från fil](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Läs in certifikat från fil

Om du behöver läsa in en certifikat fil som du överför manuellt är det bättre att ladda upp certifikatet med [FTPS](deploy-ftp.md) i stället för [git](deploy-local-git.md), till exempel. Du bör behålla känsliga data som ett privat certifikat från käll kontroll.

> [!NOTE]
> ASP.NET och ASP.NET Core i Windows måste komma åt certifikat arkivet även om du läser in ett certifikat från en fil. Om du vill läsa in en certifikat fil i en Windows .NET-app läser du in den aktuella användar profilen med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Den här metoden för att använda certifikat i din kod använder TLS-funktionen i App Service, vilket kräver att din app är på **Basic** -nivå eller högre.

Följande C#-exempel läser in ett offentligt certifikat från en relativ sökväg i appen:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett TLS/SSL-certifikat från en fil i Node.js, PHP, python, Java eller ruby finns i dokumentationen för respektive språk eller webb plattform.

## <a name="load-certificate-in-linuxwindows-containers"></a>Läs in certifikat i Linux/Windows-behållare

`WEBSITE_LOAD_CERTIFICATES`Appinställningar gör de angivna certifikaten tillgängliga för dina Windows-eller Linux container-appar (inklusive inbyggda Linux-behållare) som filer. Filerna finns under följande kataloger:

| Container plattform | Offentliga certifikat | Privata certifikat |
| - | - | - |
| Windows-container | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-container | `/var/ssl/certs` | `/var/ssl/private` |

Certifikat fil namnen är tumavtrycken. 

> [!NOTE]
> App Service mata in certifikat Sök vägar i Windows-behållare som följande miljövariabler `WEBSITE_PRIVATE_CERTS_PATH` , `WEBSITE_INTERMEDIATE_CERTS_PATH` , `WEBSITE_PUBLIC_CERTS_PATH` och `WEBSITE_ROOT_CERTS_PATH` . Det är bättre att referera till certifikat Sök vägen med miljövariablerna i stället för att hårdkoda certifikat Sök vägen, om certifikat Sök vägarna ändras i framtiden.
>

Dessutom laddar [Windows Server Core-behållare](configure-custom-container.md#supported-parent-images) certifikaten till certifikat arkivet automatiskt i **LocalMachine\My**. Om du vill läsa in certifikaten följer du samma mönster som [inläsnings certifikat i Windows-appar](#load-certificate-in-windows-apps). För Windows nano-baserade behållare använder du de fil Sök vägar som anges ovan för att [läsa in certifikatet direkt från filen](#load-certificate-from-file).

Följande C#-kod visar hur du läser in ett offentligt certifikat i en Linux-app.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett TLS/SSL-certifikat från en fil i Node.js, PHP, python, Java eller ruby finns i dokumentationen för respektive språk eller webb plattform.

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Använda TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Vanliga frågor och svar: App Service certifikat](./faq-configuration-and-management.md)