---
title: Konfigurera certifikat för program i Linux
description: Konfigurera certifikat för din app med Service Fabric runtime på ett Linux-kluster
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 70f9cc38d84681f68c10882889214648a4dd2624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785574"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikat och säkerhet på Linux-kluster

Den här artikeln innehåller information om hur du konfigurerar X. 509-certifikat på Linux-kluster.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Plats och format för X. 509-certifikat på Linux-noder

Service Fabric vanligt vis förväntar sig att X. 509-certifikat finns i */var/lib/sfcerts* -katalogen på Linux-klusternoder. Detta gäller för kluster certifikat, klient certifikat osv. I vissa fall kan du ange en annan plats än mappen *var/lib/sfcerts* för certifikat. Med Reliable Services som skapats med Service Fabric Java SDK kan du till exempel ange en annan plats genom konfigurations paketet (Settings.xml) för vissa programspecifika certifikat. Mer information finns i [certifikat som refereras i konfigurations paketet (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

För Linux-kluster förväntar Service Fabric att certifikaten finns som en. PEM-fil som innehåller både det certifikat och den privata nyckeln eller som en CRT-fil som innehåller certifikatet och en nyckel fil som innehåller den privata nyckeln. Alla filer ska vara i PEM-format. 

Om du installerar certifikatet från Azure Key Vault med hjälp av en [Resource Manager-mall](./service-fabric-cluster-creation-create-template.md) eller [PowerShell](/powershell/module/az.servicefabric/) -kommandon installeras certifikatet i rätt format i */var/lib/sfcerts* -katalogen på varje nod. Om du installerar ett certifikat via en annan metod måste du kontrol lera att certifikatet är korrekt installerat på klusternoder.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikat som refereras i applikations manifestet

Certifikat som anges i applikations manifestet, till exempel genom [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) -eller [**EndpointCertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) -element, måste finnas i */var/lib/sfcerts* -katalogen. De element som används för att ange certifikat i applikations manifestet tar inte ett Path-attribut, så certifikaten måste finnas i standard katalogen. De här elementen tar ett valfritt **X509StoreName** -attribut. Standardvärdet är "My", som pekar på */var/lib/sfcerts* -katalogen på Linux-noder. Andra värden är inte definierade i ett Linux-kluster. Vi rekommenderar att du utelämnar attributet **X509StoreName** för appar som körs i Linux-kluster. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikat som refereras i konfigurations paketet (Settings.xml)

För vissa tjänster kan du konfigurera X. 509-certifikat i [ConfigPackage](./service-fabric-application-and-service-manifests.md) (som standard Settings.xml). Detta är exempelvis fallet när du deklarerar certifikat som används för att skydda RPC-kanaler för Reliable Services tjänster som skapats med Service Fabric .NET Core eller Java SDK: er. Det finns två sätt att referera till certifikat i konfigurations paketet. Support varierar mellan .NET Core och Java SDK: er.

### <a name="using-x509-securitycredentialstype"></a>Använda X509-SecurityCredentialsType

Med .NET-eller Java-SDK: er kan du ange **X509** som **SecurityCredentialsType**. Detta motsvarar `X509Credentials` ([.net](/previous-versions/azure/reference/mt124925(v=azure.100)) / [Java](/java/api/system.fabric.x509credentials))-typen `SecurityCredentials` ([.net](/previous-versions/azure/reference/mt124894(v=azure.100)) / [Java](/java/api/system.fabric.securitycredentials)).

**X509** -referensen söker upp certifikatet i ett certifikat arkiv. Följande XML visar de parametrar som används för att ange platsen för certifikatet:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

För en tjänst som körs på Linux, **LocalMachine** / **Mina** platser till standard platsen för certifikat, */var/lib/sfcerts* -katalogen. För Linux är alla andra kombinationer av **CertificateStoreLocation** och **certifikat Arkiv** odefinierade. 

Ange alltid **LocalMachine** för parametern **CertificateStoreLocation** . Du behöver inte ange parametern **certifikat Arkiv** eftersom den använder "My" som standard. Med en **X509** -referens måste certifikatmallarna finnas i katalogen */var/lib/sfcerts* på klusternoden.  

Följande XML visar ett **TransportSettings** -avsnitt baserat på det här formatet:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>Använda X509_2 SecurityCredentialsType

Med Java SDK kan du ange **X509_2** för **SecurityCredentialsType**. Detta motsvarar `X509Credentials2` ([Java](/java/api/system.fabric.x509credentials2)) typen `SecurityCredentials` (Java[](/java/api/system.fabric.securitycredentials)). 

Med en **X509_2** referens kan du ange en Sök vägs parameter, så att du kan hitta certifikatet i en annan katalog än */var/lib/sfcerts*.  Följande XML visar de parametrar som används för att ange platsen för certifikatet: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Följande XML visar ett **TransportSettings** -avsnitt baserat på det här formatet.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Certifikatet anges som en. CRT-fil i föregående XML. Detta innebär också att det finns en. nyckel fil som innehåller den privata nyckeln på samma plats.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurera en Reliable Services app som ska köras på Linux-kluster

Med Service Fabric SDK: er kan du kommunicera med Service Fabric runtime-API: er för att utnyttja plattformen. När du kör ett program som använder den här funktionen på säkra Linux-kluster måste du konfigurera ditt program med ett certifikat som kan användas för att verifiera med Service Fabric Runtime. Program som innehåller Service Fabric Reliable service Services som skrivits med .NET Core eller Java SDK: er kräver den här konfigurationen. 

Om du vill konfigurera ett program lägger du till ett [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) -element under taggen **certifikat** , som finns under taggen **ApplicationManifest** i *ApplicationManifest.xml* -filen. Följande XML visar ett certifikat som refereras till av sitt tumavtryck: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Du kan referera till antingen kluster certifikatet eller ett certifikat som du installerar på varje klusternod. På Linux måste certifikatfiler finnas i */var/lib/sfcerts* -katalogen. Mer information finns i [plats och format för X. 509-certifikat på Linux-noder](#location-and-format-of-x509-certificates-on-linux-nodes).
