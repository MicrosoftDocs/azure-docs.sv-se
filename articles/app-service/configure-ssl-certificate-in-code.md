---
title: Använda ett TLS/SSL-certifikat i kod
description: Lär dig hur du använder klientcertifikat i din kod. Autentisera med fjärrresurser med ett klientcertifikat eller kör kryptografiska uppgifter med dem.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829412"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Använda ett TLS-/SSL-certifikat i koden i Azure App Service

I programkoden kan du komma åt de offentliga [eller privata certifikat som du lägger till i App Service](configure-ssl-certificate.md). Din appkod kan fungera som en klient och få åtkomst till en extern tjänst som kräver certifikatautentisering, eller så kan den behöva utföra kryptografiska uppgifter. Den här guiden visar hur du använder offentliga eller privata certifikat i programkoden.

Den här metoden för att använda certifikat i din kod använder TLS-funktionen i App Service, vilket kräver att din app är på **Basic-nivån** eller högre. Om din app är på **den kostnadsfria** **eller delade** nivån kan du [inkludera certifikatfilen på appdatabasen](#load-certificate-from-file).

När du App Service hantera dina TLS-/SSL-certifikat kan du underhålla certifikaten och programkoden separat och skydda känsliga data.

## <a name="prerequisites"></a>Förutsättningar

Så här följer du den här guiden:

- [Skapa en App Service-app](./index.yml)
- [Lägga till ett certifikat i din app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Hitta tumavtrycket

Välj <a href="https://portal.azure.com" target="_blank">Azure Portal</a>på menyn till vänster i **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält väljer du **TLS/SSL-inställningar** och sedan Certifikat för privat nyckel **(.pfx)** eller Certifikat för offentlig nyckel **(.cer).**

Leta upp det certifikat som du vill använda och kopiera tumavtrycket.

![Kopiera certifikatets tumavtryck](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Gör certifikatet tillgängligt

Om du vill komma åt ett certifikat i din appkod lägger du till dess tumavtryck i appinställningen genom att köra följande `WEBSITE_LOAD_CERTIFICATES` kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Om du vill göra alla dina certifikat tillgängliga anger du värdet till `*` .

## <a name="load-certificate-in-windows-apps"></a>Läsa in certifikat i Windows-appar

Med appinställningen blir de angivna certifikaten tillgängliga för din `WEBSITE_LOAD_CERTIFICATES` Windows-värdapp i Windows-certifikatarkivet i [Aktuell användare\Min.](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

I C#-kod får du åtkomst till certifikatet via certifikatets tumavtryck. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

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

I Java-kod får du åtkomst till certifikatet från Windows-MY-arkivet med hjälp av fältet Ämnesnamn (se [Certifikat för offentlig nyckel).](https://en.wikipedia.org/wiki/Public_key_certificate) Följande kod visar hur du läser in ett certifikat för privat nyckel:

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

För språk som inte stöder eller inte erbjuder otillräckligt stöd för Windows-certifikatarkivet, se [Läs in certifikat från fil](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Läsa in certifikat från fil

Om du behöver läsa in en certifikatfil som du överför manuellt är det bättre att ladda upp certifikatet med [FTPS](deploy-ftp.md) i stället för [Git,](deploy-local-git.md)till exempel. Du bör hålla känsliga data som ett privat certifikat utanför källkontrollen.

> [!NOTE]
> ASP.NET och ASP.NET Core i Windows måste ha åtkomst till certifikatarkivet även om du läser in ett certifikat från en fil. Om du vill läsa in en certifikatfil i en Windows .NET-app läser du in den aktuella användarprofilen med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Den här metoden för att använda certifikat i din kod använder TLS-funktionen i App Service, vilket kräver att din app är på **Basic-nivå** eller högre.

I följande C#-exempel läses ett offentligt certifikat in från en relativ sökväg i din app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett TLS/SSL-certifikat från en fil i Node.js, PHP, Python, Java eller Ruby finns i dokumentationen för respektive språk eller webbplattform.

## <a name="load-certificate-in-linuxwindows-containers"></a>Läsa in certifikat i Linux/Windows-containrar

Appinställningarna `WEBSITE_LOAD_CERTIFICATES` gör de angivna certifikaten tillgängliga för dina Windows- eller Linux-containerappar (inklusive inbyggda Linux-containrar) som filer. Filerna finns under följande kataloger:

| Containerplattform | Offentliga certifikat | Privata certifikat |
| - | - | - |
| Windows-container | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-container | `/var/ssl/certs` | `/var/ssl/private` |

Certifikatfilnamnen är tumavtrycken för certifikatet. 

> [!NOTE]
> App Service in certifikatsökvägarna i Windows-containrar som följande `WEBSITE_PRIVATE_CERTS_PATH` miljövariabler `WEBSITE_INTERMEDIATE_CERTS_PATH` , , `WEBSITE_PUBLIC_CERTS_PATH` och `WEBSITE_ROOT_CERTS_PATH` . Det är bättre att referera till certifikatsökvägen med miljövariablerna i stället för att hårdkoda certifikatsökvägen, om certifikatsökvägarna ändras i framtiden.
>

Dessutom läser [Windows Server Core-containrar](configure-custom-container.md#supported-parent-images) in certifikaten i certifikatarkivet automatiskt i **LocalMachine\My**. Om du vill läsa in certifikaten följer du samma mönster som [Läs in certifikat i Windows-appar.](#load-certificate-in-windows-apps) För Windows Nano-baserade containrar använder du filsökvägarna ovan för [att läsa in certifikatet direkt från filen](#load-certificate-from-file).

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

Följande C#-kod visar hur du läser in ett privat certifikat i en Linux-app.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett TLS/SSL-certifikat från en fil i Node.js, PHP, Python, Java eller Ruby finns i dokumentationen för respektive språk eller webbplattform.

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Använda TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Vanliga frågor och svar: App Service certifikat](./faq-configuration-and-management.md)
