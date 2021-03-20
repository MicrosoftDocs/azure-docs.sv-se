---
title: Säker kommunikation med Azure Service Fabric omvänd proxy
description: Konfigurera omvänd proxy för att aktivera säker kommunikation från slut punkt till slut punkt i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256143"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ansluta till en säker tjänst med omvänd proxy

Den här artikeln förklarar hur du upprättar en säker anslutning mellan omvänd proxy och tjänster, vilket innebär att en säker kanal från slut punkt till slut punkt skapas. Läs mer om omvänd proxy [i omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> Det går bara att ansluta till säkra tjänster när omvänd proxy har kon figurer ATS för att lyssna på HTTPS. I den här artikeln förutsätter vi att detta är fallet. Läs konfigurera [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy-setup.md) för att konfigurera omvänd proxy i Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Upprätta säker anslutning mellan omvänd proxy och tjänster 

### <a name="reverse-proxy-authenticating-to-services"></a>Omvänd proxy-autentisering till tjänster:
Den omvända proxyn identifierar sig själv för tjänster med hjälp av certifikatet. För Azure-kluster anges certifikatet med ***reverseProxyCertificate** _ egenskap i [resurs typ avsnittet](../azure-resource-manager/templates/template-syntax.md) [_ *Microsoft. ServiceFabric* * /Clusters](/azure/templates/microsoft.servicefabric/clusters) i Resource Manager-mallen. För fristående kluster anges certifikatet med **egenskapen _ReverseProxyCertificate_*_ eller _*_ReverseProxyCertificateCommonNames_*_ _ i avsnittet _* säkerhet** i ClusterConfig.jspå. Läs mer i [Aktivera omvänd proxy i fristående kluster](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Tjänster kan implementera logiken för att verifiera certifikatet som presenteras av den omvända proxyn. Tjänsterna kan ange information om godkända klient certifikat som konfigurations inställningar i konfigurations paketet. Detta kan läsas vid körning och används för att validera certifikatet som presenteras av den omvända proxyn. Se [hantera program parametrar](service-fabric-manage-multiple-environment-app-configuration.md) för att lägga till konfigurations inställningarna. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Omvänd proxy verifierar tjänstens identitet via certifikatet som presenteras av tjänsten:
Omvänd proxy stöder följande principer för att utföra Server certifikat validering av de certifikat som presenteras av tjänsterna: none, ServiceCommonNameAndIssuer och ServiceCertificateThumbprints.
Om du vill välja princip för omvänd proxy som ska användas anger du **ApplicationCertificateValidationPolicy** i avsnittet **ApplicationGateway/http** under [fabricSettings](service-fabric-cluster-fabric-settings.md).

I nästa avsnitt visas konfigurations information för var och en av dessa alternativ.

### <a name="service-certificate-validation-options"></a>Verifierings alternativ för tjänst certifikat 

- **Ingen**: omvänd proxy hoppar över verifiering av tjänst certifikatet för proxy och upprättar en säker anslutning. Det här är standardbeteendet.
Ange **ApplicationCertificateValidationPolicy** med värdet **ingen** i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: omvänd proxy verifierar certifikatet som presenteras av tjänsten baserat på certifikatets egna namn och omedelbara utfärdarens tumavtryck: ange **ApplicationCertificateValidationPolicy** med värdet **ServiceCommonNameAndIssuer** i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Om du vill ange en lista över tjänstens nätverks namn och utfärdare tumavtrycken lägger du till ett [**ApplicationGateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) -avsnitt under **fabricSettings**, som du ser nedan. Du kan lägga till flera certifikats egna namn och Issuer-tumavtryck i **Parameters** -matrisen. 

   Om den omslutna återförings-proxyn ansluter till visar ett certifikat som är ett eget namn och utfärdarens tumavtryck matchar något av de värden som anges här, en TLS-kanal upprättas.
   Om det inte gick att matcha certifikat informationen, Miss lyckas reversed proxy av klientens begäran med status koden 502 (Felaktig gateway). HTTP-statuskoden innehåller även frasen "ogiltigt SSL-certifikat". 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: omvänd proxy verifierar proxy-tjänstens certifikat baserat på dess tumavtryck. Du kan välja att använda den här vägen när tjänsterna har kon figurer ATS med självsignerade certifikat: ange **ApplicationCertificateValidationPolicy** med värdet **ServiceCertificateThumbprints** i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Ange även tumavtrycken med en **ServiceCertificateThumbprints** -post i avsnittet **ApplicationGateway/http** . Flera tumavtrycken kan anges som en kommaavgränsad lista i fältet värde, som du ser nedan:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Om Server certifikatets tumavtryck anges i den här konfigurations posten, slutför omvänd proxy TLS-anslutningen. Annars avbryts anslutningen och klientens begäran Miss lyckas med 502 (Felaktig gateway). HTTP-statuskoden innehåller även frasen "ogiltigt SSL-certifikat".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Slut punkts val logik när tjänster exponerar säkra såväl som oskyddade slut punkter
Service Fabric har stöd för konfiguration av flera slut punkter för en tjänst. Mer information finns i [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md).

Omvänd proxy väljer en av slut punkterna för att vidarebefordra begäran baserat på **ListenerName** i [tjänst-URI: n](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Om parametern **ListenerName** inte anges kan omvänd proxy välja vilken slut punkt som helst från listan slut punkter. Beroende på vilka slut punkter som har kon figurer ATS för tjänsten kan den valda slut punkten vara en HTTP-eller HTTPS-slutpunkt. Det kan finnas scenarier eller krav där du vill att den omvända proxyn ska arbeta i ett "skyddat läge"; det vill säga att du inte vill att den säkra omvända proxyn vidarebefordrar begär anden till oskyddade slut punkter. Om du vill ställa in omvänd proxy till endast skyddat läge anger du konfigurations posten **SecureOnlyMode** med värdet **True** i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Om en klient har angett en ListenerName som motsvarar en HTTP-slutpunkt (ej skyddad) i **SecureOnlyMode**, och om en klient har angett en  som motsvarar en http-slutpunkt (ej skyddad), Miss lyckas begäran med en http-statuskod på 404

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurera autentisering av klient certifikat via omvänd proxy
TLS-avslutning sker på den omvända proxyn och alla klient certifikat data går förlorade. För tjänsterna för att utföra autentisering av klient certifikat anger du inställningen **ForwardClientCertificate** i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

1. När **ForwardClientCertificate** är inställt på **false** kommer omvänd proxy inte att begära klient certifikatet under dess TLS-handskakning med-klienten.
Det här är standardbeteendet.

2. När **ForwardClientCertificate** är inställt på **True** begär omvänd proxy klientens certifikat under sin TLS-handskakning med-klienten.
Sedan vidarebefordras klient certifikat data i ett anpassat HTTP-huvud med namnet **X-client-Certificate**. Head-värdet är Base64-kodad PEM-format sträng för klientens certifikat. Tjänsten kan lyckas eller Miss lyckas med lämplig status kod efter att ha kontrollerat certifikat data.
Om klienten inte visar ett certifikat vidarebefordrar omvänd proxy en tom rubrik och låter tjänsten hantera ärendet.

> [!NOTE]
> Omvänd proxy fungerar bara som en vidarebefordrings tjänst. Ingen validering av klientens certifikat utförs.


## <a name="next-steps"></a>Nästa steg
* Konfigurera [och konfigurera omvänd proxy i ett kluster](service-fabric-reverseproxy-setup.md).
* Se [Konfigurera omvänd proxy för att ansluta till säkra tjänster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Se ett exempel på HTTP-kommunikation mellan tjänster i ett [exempel projekt på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Fjärran rop med Reliable Services fjärr kommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
