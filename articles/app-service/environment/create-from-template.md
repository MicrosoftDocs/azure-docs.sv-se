---
title: Skapa en ASE med ARM
description: Lär dig hur du skapar en extern eller ILB App Service miljö genom att använda en Azure Resource Manager mall.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148167"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Skapa en ASE med hjälp av en Azure Resource Manager mall

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service miljöer (ASE) kan skapas med en tillgänglig slut punkt på Internet eller en slut punkt på en intern adress i ett virtuellt Azure-nätverk (VNet). När den skapas med en intern slut punkt tillhandahålls den slut punkten av en Azure-komponent som kallas intern belastningsutjämnare (ILB). ASE på en intern IP-adress kallas för en ILB-ASE. ASE med en offentlig slut punkt kallas för en extern ASE. 

Du kan skapa en ASE med hjälp av Azure Portal eller en Azure Resource Manager mall. Den här artikeln beskriver de steg och den syntax du behöver för att skapa en extern ASE-eller ILB-ASE med Resource Manager-mallar. Information om hur du skapar en ASE i Azure Portal finns i [skapa en extern ASE][MakeExternalASE] eller [göra en ILB ASE][MakeILBASE].

När du skapar en ASE i Azure Portal kan du skapa ditt VNet på samma gång eller välja ett befintligt VNet att distribuera till. När du skapar en ASE från en mall måste du börja med: 

* Ett virtuellt Resource Manager-nätverk.
* Ett undernät i det virtuella nätverket. Vi rekommenderar en ASE-undernätets storlek på `/24` med 256 adresser för framtida tillväxt-och skalnings behov. När ASE har skapats kan du inte ändra storleken.
* Resurs-ID från ditt VNet. Du kan hämta den här informationen från Azure Portal under egenskaperna för ditt virtuella nätverk.
* Den prenumeration som du vill distribuera till.
* Den plats som du vill distribuera till.

Så här automatiserar du skapandet av ASE:

1. Skapa ASE från en mall. Om du skapar en extern ASE är du klar efter det här steget. Om du skapar en ILB-ASE finns det några fler saker att göra.

2. När din ILB-ASE har skapats laddas ett TLS/SSL-certifikat som matchar din ILB ASE-domän.

3. Det uppladdade TLS/SSL-certifikatet tilldelas till ILB-ASE som "standard" TLS/SSL-certifikat.  Det här certifikatet används för TLS/SSL-trafik till appar på ILB-ASE när de använder den gemensamma rot domänen som är tilldelad till ASE (till exempel `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Skapa ASE
En Resource Manager-mall som skapar en ASE och dess tillhör ande parameter fil är tillgänglig [i ett exempel][quickstartasev2create] på GitHub.

Om du vill skapa en ILB-ASE använder du dessa [exempel][quickstartilbasecreate]för Resource Manager-mallar. De är i användnings fall. De flesta av parametrarna i *azuredeploy.parameters.jsi* filen är vanliga för att skapa ILB-ASE och externa ASE. I följande lista anropas parametrar för särskild anteckning eller som är unika när du skapar en ILB-ASE:

* *internalLoadBalancingMode*: i de flesta fall ska du ange detta till 3, vilket innebär både http/https-trafik på portarna 80/443 och kontroll-/data kanals portarna som har avlyssnats av FTP-tjänsten på ASE, binds till en ILB-allokerad virtuell nätverks adress. Om den här egenskapen anges till 2, är bara FTP-tjänstens relaterade portar (både kontroll-och data kanaler) bundna till en ILB-adress. HTTP/HTTPS-trafiken finns kvar på den offentliga VIP: en.
* *dnsSuffix*: den här parametern definierar den standard rot domän som är kopplad till ASE. I den offentliga variationen av Azure App Service, är standard rot domänen för alla webbappar *azurewebsites.net*. Eftersom en ILB-ASE är intern för kundens virtuella nätverk är det inte klokt att använda den offentliga tjänstens standard rot domän. I stället bör en ILB-ASE ha en standard rot domän som är bra att använda i företagets interna virtuella nätverk. Contoso Corporation kan till exempel använda en standard rot domän för *Internal-contoso.com* för appar som är avsedda att matchas och endast nås i Contosos virtuella nätverk. 
* *ipSslAddressCount*: den här parametern använder automatiskt värdet 0 i *azuredeploy.jspå* filen eftersom ILB ASE bara har en enda ILB-adress. Det finns inga explicita IP-SSL-adresser för en ILB-ASE. Därför måste IP-SSL-adresspoolen för en ILB-ASE anges till noll. Annars inträffar ett etablerings fel. 

När *azuredeploy.parameters.js* filen har fyllts i skapar du ASE med hjälp av PowerShell-kodfragmentet. Ändra fil Sök vägarna så att de matchar Resource Manager-mallens fil platser på din dator. Kom ihåg att ange dina egna värden för Resource Manager-distributionens namn och resurs gruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär en timme innan ASE skapas. Sedan visas ASE i portalen i listan över ASE för den prenumeration som utlöste distributionen.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Ladda upp och konfigurera "standard" TLS/SSL-certifikatet
Ett TLS/SSL-certifikat måste vara associerat med ASE som standard TLS/SSL-certifikat som används för att upprätta TLS-anslutningar till appar. Om ASE standard-DNS-suffixet är *Internal-contoso.com*, så kräver en anslutning till `https://some-random-app.internal-contoso.com` ett TLS/SSL-certifikat som är giltigt för **. Internal-contoso.com*. 

Skaffa ett giltigt TLS/SSL-certifikat genom att använda interna certifikat utfärdare, köpa ett certifikat från en extern utfärdare eller använda ett självsignerat certifikat. Oberoende av källan till TLS/SSL-certifikatet måste följande attribut för certifikaten vara korrekt konfigurerade:

* **Subject**: det här attributet måste anges till **. your-Root-Domain-here.com*.
* **Alternativt namn för certifikat mottagare**: det här attributet måste innehålla både **. your-Root-Domain-here.com* och **. scm.your-Root-Domain-here.com*. TLS-anslutningar till den SCM/kudu-plats som är associerad med varje app använder en adress i formatet *Your-App-Name.scm.your-Root-Domain-here.com*.

Med ett giltigt TLS/SSL-certifikat i handen krävs ytterligare två förberedande steg. Konvertera/Spara TLS/SSL-certifikatet som en PFX-fil. Kom ihåg att. pfx-filen måste innehålla alla mellanliggande och rot certifikat. Skydda den med ett lösenord.

. Pfx-filen måste konverteras till en Base64-sträng eftersom TLS/SSL-certifikatet laddas upp med hjälp av en Resource Manager-mall. Eftersom Resource Manager-mallar är Textfiler måste PFX-filen konverteras till en Base64-sträng. På så sätt kan den inkluderas som en parameter för mallen.

Använd följande PowerShell-kodfragment för att:

* Generera ett självsignerat certifikat.
* Exportera certifikatet som en PFX-fil.
* Konvertera. pfx-filen till en Base64-kodad sträng.
* Spara den base64-kodade strängen i en separat fil. 

Den här PowerShell-koden för base64-kodning har anpassats från [PowerShell-skriptets blogg][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com&quot;,&quot;*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

När TLS/SSL-certifikatet har genererats och konverterats till en Base64-kodad sträng använder du exempel Resource Manager-mallen [Konfigurera SSL-standardcertifikatet][quickstartconfiguressl] på GitHub. 

Parametrarna i *azuredeploy.parameters.jsi* filen visas här:

* *appServiceEnvironmentName*: namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: text sträng som innehåller den Azure-region där ILB-ASE har distribuerats.  Till exempel: "södra centrala USA".
* *pfxBlobString*: den based64-kodade sträng representationen för. pfx-filen. Använd kodfragmentet som visas tidigare och kopiera strängen som finns i "exportedcert. pfx. B64". Klistra in det som värdet för attributet *pfxBlobString* .
* *lösen ord*: lösen ordet som används för att skydda. pfx-filen.
* *certificateThumbprint*: certifikatets tumavtryck. Om du hämtar det här värdet från PowerShell (till exempel *$Certificate. Tumavtryck* från det tidigare kodfragmentet) kan du använda värdet som är. Om du kopierar värdet från dialog rutan Windows-certifikat, kom ihåg att ta bort de extra blank stegen. *CertificateThumbprint* bör se ut ungefär som AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: ett eget sträng-ID som du väljer används för att identifiera certifikatet. Namnet används som en del av den unika Resource Manager-identifieraren för *Microsoft. Web/certificates-* enheten som representerar TLS/SSL-certifikatet. Namnet *måste* sluta med följande suffix: \_ yourASENameHere_InternalLoadBalancingASE. Azure Portal använder det här suffixet som en indikator att certifikatet används för att skydda ett ILB-aktiverat ASE.

Ett förkortat exempel på *azuredeploy.parameters.jspå* visas här:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

När *azuredeploy.parameters.js* filen har fyllts i konfigurerar du standard TLS/SSL-certifikatet med hjälp av PowerShell-kodfragmentet. Ändra fil Sök vägarna så att de matchar där Resource Manager-mallfilerna finns på datorn. Kom ihåg att ange dina egna värden för Resource Manager-distributionens namn och resurs gruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär 40 minuter per ASE-klient att tillämpa ändringen. Till exempel, för en ASE som använder två klient delar, tar mallen runt en timme och 20 minuter att slutföra. När mallen körs kan ASE inte skalas.  

När mallen har slutförts kan appar på ILB-ASE nås via HTTPS. Anslutningarna skyddas med hjälp av standard TLS/SSL-certifikatet. TLS/SSL-standardcertifikatet används när appar på ILB-ASE adresseras med hjälp av en kombination av program namnet plus standard värd namnet. Använder till exempel `https://mycustomapp.internal-contoso.com` standard TLS/SSL-certifikat för **. Internal-contoso.com*.

Men precis som appar som körs på den offentliga tjänsten för flera innehavare kan utvecklare konfigurera anpassade värdnamn för enskilda appar. De kan också konfigurera unika SNI TLS/SSL-certifikat bindningar för enskilda appar.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2.

I ASEv1 hanterar du alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut App Service plan måste du skala ut den arbets grupp som du vill använda som värd för den.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Innehåller virtuella processorer som används för klient delar eller arbetare som inte är värdar för några arbets belastningar. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

Om du vill skapa en ASEv1 med hjälp av en Resource Manager-mall, se [skapa en ILB ASE v1 med en Resource Manager-mall][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md