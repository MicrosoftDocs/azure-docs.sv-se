---
title: Certifikat bindningar
description: Förklara flera ämnen som rör certifikat på en App Service-miljön. Lär dig hur certifikat bindningar fungerar i appar för en enda klient i en ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 306445e26e5b236b49273b9ab8888ecc610bc075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962051"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikat och App Service-miljön 

App Service-miljön (ASE) är en distribution av Azure App Service som körs i Azure-Virtual Network (VNet). Den kan distribueras med en tillgänglig program slut punkt för Internet eller en slut punkt för program som finns i ditt VNet. Om du distribuerar ASE med en tillgänglig slut punkt för Internet kallas distributionen en extern ASE. Om du distribuerar ASE med en slut punkt i ditt virtuella nätverk kallas den distributionen en ILB-ASE. Du kan lära dig mer om ILB-ASE från dokumentet [create och Use a ILB ASE](./create-ilb-ase.md) .

ASE är ett enda klient system. Eftersom det är en enda klient, finns det vissa funktioner som endast är tillgängliga med en ASE som inte är tillgänglig i App Service för flera innehavare. 

## <a name="ilb-ase-certificates"></a>ILB ASE-certifikat 

Om du använder en extern ASE nås dina appar på [APPNAME]. [asename]. p. azurewebsites. net. Som standard skapas alla ASE, även ILB ASE, med certifikat som följer det formatet. När du har en ILB-ASE nås apparna baserat på det domän namn som du anger när du skapar ILB-ASE. För att appar ska kunna stödja TLS måste du ladda upp certifikat. Skaffa ett giltigt TLS/SSL-certifikat genom att använda interna certifikat utfärdare, köpa ett certifikat från en extern utfärdare eller använda ett självsignerat certifikat. 

Det finns två alternativ för att konfigurera certifikat med din ILB-ASE.  Du kan ange ett standard certifikat för jokertecken för ILB-ASE eller ange certifikat för enskilda webbappar i ASE.  Oavsett vilket val du gör måste följande attribut för certifikaten vara korrekt konfigurerade:

- **Ämne:** Det här attributet måste anges till *. [din-Root-Domain-här] för ett ILB ASE-certifikat med jokertecken. Om du skapar certifikatet för din app ska det vara [APPNAME]. [din-Root-Domain-här]
- **Alternativt namn för certifikat mottagare:** Attributet måste innehålla både *. [din-Root-Domain-här] och *. scm. [din-Root-Domain-här] för ASE-certifikatet med jokertecken ILB. Om du skapar certifikatet för din app ska det vara [APPNAME]. [din-Root-Domain-här] och [APPNAME]. scm. [din-Root-Domain-här].

Som tredje variant kan du skapa ett ILB ASE-certifikat som innehåller alla dina egna program namn i certifikatets SAN i stället för att använda en jokertecken referens. Problemet med den här metoden är att du måste känna till namnen på de appar som du placerar i ASE eller så måste du fortsätta uppdatera ILB ASE-certifikatet.

### <a name="upload-certificate-to-ilb-ase"></a>Ladda upp certifikat till ILB ASE 

När en ILB-ASE har skapats i portalen måste certifikatet anges för ILB-ASE. När certifikatet har angetts visas en banderoll i ASE som inte har angetts för certifikatet.  

Det certifikat som du överför måste vara en. pfx-fil. När certifikatet har laddats upp utför ASE en skalnings åtgärd för att ställa in certifikatet. 

Du kan inte skapa ASE och ladda upp certifikatet som en åtgärd i portalen eller till och med i en mall. Som en separat åtgärd kan du ladda upp certifikatet med hjälp av en mall enligt beskrivningen i [skapa en ASE från ett dokument i mallen](./create-from-template.md) .  

Om du snabbt vill skapa ett självsignerat certifikat för testning kan du använda följande PowerShell-version:

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

När du skapar ett självsignerat certifikat måste du se till att ämnes namnet har formatet CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Program certifikat 

Appar som finns i en ASE kan använda app-koncentriska certifikat funktioner som är tillgängliga i App Service för flera innehavare. Dessa funktioner är:  

- SNI-certifikat 
- IP-baserad SSL, som endast stöds med en extern ASE.  En ILB-ASE har inte stöd för IP-baserad SSL.
- Värdbaserade certifikat för nyckel valv 

Instruktioner för att ladda upp och hantera dessa certifikat finns i [Lägg till ett TLS/SSL-certifikat i Azure App Service](../configure-ssl-certificate.md).  Om du helt enkelt konfigurerar certifikat så att de matchar ett anpassat domän namn som du har tilldelat din webbapp räcker dessa instruktioner. Om du laddar upp certifikatet för en ILB ASE-webbapp med standard domän namnet anger du SCM-platsen i SAN-nätverket för certifikatet enligt ovan. 

## <a name="tls-settings"></a>TLS-inställningar 

Du kan konfigurera TLS-inställningen på en app-nivå.  

## <a name="private-client-certificate"></a>Privat klient certifikat 

Ett vanligt användnings fall är att konfigurera din app som en klient i en klient-server-modell. Om du skyddar din server med ett privat CA-certifikat måste du ladda upp klient certifikatet till din app.  Följande instruktioner läser in certifikat till trustStore för de arbetare som appen körs på. Om du läser in certifikatet till en app kan du använda det med dina andra appar i samma App Service plan utan att ladda upp certifikatet igen.

Ladda upp certifikatet till din app i din ASE:

1. Generera en *CER* -fil för ditt certifikat. 
2. Gå till den app som behöver certifikatet i Azure Portal
3. Gå till SSL-inställningar i appen. Klicka på Ladda upp certifikat. Välj Offentlig. Välj lokal dator. Ange ett namn. Bläddra och välj *CER* -filen. Välj överför. 
4. Kopiera tumavtrycket.
5. Gå till program inställningar. Skapa en app-inställning WEBSITE_LOAD_ROOT_CERTIFICATES med tumavtrycket som värde. Om du har flera certifikat kan du lägga dem i samma inställning avgränsade med kommatecken och inget blank steg som 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certifikatet är tillgängligt av alla appar i samma app service-plan som appen, som har konfigurerat inställningen. Om du vill att det ska vara tillgängligt för appar i en annan App Service plan måste du upprepa appens inställnings åtgärd i en app i App Service plan. Om du vill kontrol lera att certifikatet har angetts går du till kudu-konsolen och utfärdar följande kommando i PowerShell-felsöknings konsolen:

```azurepowershell-interactive
dir cert:\localmachine\root
```

Om du vill utföra testningen kan du skapa ett självsignerat certifikat och generera en *CER* -fil med följande PowerShell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```