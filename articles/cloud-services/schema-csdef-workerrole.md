---
title: Azure Cloud Services (klassisk) Def. WorkerRole-schema | Microsoft Docs
description: Azure Worker-rollen används för allmän utveckling och kan utföra bakgrunds bearbetning för en webbroll. Lär dig mer om Azure Worker Role-schemat.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0871527187a3d678cb2b94bd8dc342cf2abde1ba
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743142"
---
# <a name="azure-cloud-services-classic-definition-workerrole-schema"></a>Azure Cloud Services (klassisk) definition WorkerRole-schema

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Azure Worker-rollen är en roll som är användbar för generaliserad utveckling och som kan utföra bakgrunds bearbetning för en webbroll.

Standard tillägget för tjänst definitions filen är. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Grundläggande tjänst definitions schema för en arbets roll.
Det grundläggande formatet för tjänst definitions filen som innehåller en arbets roll är följande.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema element
Tjänst definitions filen innehåller dessa element, som beskrivs i detalj i följande avsnitt i det här avsnittet:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Inställning](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Slutpunkter](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikat](#Certificates)

[Certifikat](#Certificate)

[Kina](#Imports)

[Importera](#Import)

[Körning](#Runtime)

[Miljö](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabel](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Start](#Startup)

[Uppgift](#Task)

[Innehåll](#Contents)

[Innehåll](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a> WorkerRole
`WorkerRole`-Elementet beskriver en roll som är användbar för generaliserad utveckling och kan utföra bakgrunds bearbetning för en webbroll. En tjänst kan innehålla noll eller flera arbets roller.

I följande tabell beskrivs attributen för- `WorkerRole` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Namnet på arbets rollen. Rollens namn måste vara unikt.|
|enableNativeCodeExecution|boolean|Valfritt. Standardvärdet är `true` , intern kod körning och fullständigt förtroende är aktiverat som standard. Ange det här attributet till `false` om du vill inaktivera intern kod körning för arbets rollen och använda Azure partiellt förtroende i stället.|
|vmsize|sträng|Valfritt. Ange det här värdet om du vill ändra storleken på den virtuella dator som har tilldelats den här rollen. Standardvärdet är `Small`. En lista över möjliga storlekar för virtuella datorer och deras attribut finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings
- `ConfigurationSettings` Elementet beskriver samlingen av konfigurations inställningar för en arbets roll. Det här elementet är överordnat `Setting` elementet.

##  <a name="setting"></a><a name="Setting"></a> Inställningen
`Setting`Elementet beskriver ett namn och ett värde-par som anger en konfigurations inställning för en instans av en roll.

I följande tabell beskrivs attributen för- `Setting` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett unikt namn för konfigurations inställningen.|

Konfigurations inställningarna för en roll är namn-och värdepar som deklareras i tjänst definitions filen och anges i tjänst konfigurations filen.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources
- `LocalResources` Elementet beskriver samlingen av lokala lagrings resurser för en arbets roll. Det här elementet är överordnat `LocalStorage` elementet.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage
`LocalStorage`Elementet identifierar en lokal lagrings resurs som tillhandahåller fil system utrymme för tjänsten vid körning. En roll kan definiera noll eller flera lokala lagrings resurser.

> [!NOTE]
>  `LocalStorage`Elementet kan visas som underordnat `WorkerRole` elementet för att stödja kompatibilitet med tidigare versioner av Azure SDK.

I följande tabell beskrivs attributen för- `LocalStorage` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett unikt namn för det lokala arkivet.|
|cleanOnRoleRecycle|boolean|Valfritt. Anger om det lokala arkivet ska rensas när rollen startas om. Standardvärdet är `true`.|
|Att sizeinmb|int|Valfritt. Den önskade mängden lagrings utrymme som ska allokeras för det lokala arkivet, i MB. Om inget värde anges är det allokerade standard lagrings utrymmet 100 MB. Den minsta mängd lagrings utrymme som kan allokeras är 1 MB.<br /><br /> Den maximala storleken på de lokala resurserna är beroende av storleken på den virtuella datorn. Mer information finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

Namnet på den katalog som allokeras till den lokala lagrings resursen motsvarar det värde som anges för attributet name.

##  <a name="endpoints"></a><a name="Endpoints"></a> Slut punkter
`Endpoints`-Elementet beskriver insamlingen av indatamängds slut punkter (externt), interna och instans-ingångar för en roll. Det här elementet är överordnat `InputEndpoint` `InternalEndpoint` `InstanceInputEndpoint` elementen, och.

Inmatade och interna slut punkter tilldelas separat. En tjänst kan ha totalt 25 slut punkter för indatatyper, interna och instans ingångar som kan allokeras mellan de 25 roller som tillåts i en tjänst. Om du till exempel har 5 roller kan du allokera 5 ingångs slut punkter per roll, eller så kan du allokera 25 ingångs slut punkter till en enda roll, eller så kan du allokera 1-ingångs slut punkt till 25 roller.

> [!NOTE]
>  Varje roll som distribueras kräver en instans per roll. Standard etableringen för en prenumeration är begränsad till 20 kärnor och är därför begränsad till 20 instanser av en roll. Om ditt program kräver fler instanser än vad som tillhandahålls av standard etableringen [, se fakturering, prenumerations hantering och kvot stöd](https://azure.microsoft.com/support/options/) för mer information om hur du ökar din kvot.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint
`InputEndpoint`Elementet beskriver en extern slut punkt för en arbets roll.

Du kan definiera flera slut punkter som är en kombination av HTTP-, HTTPS-, UDP-och TCP-slutpunkter. Du kan ange vilket port nummer du väljer för en ingångs slut punkt, men port numren som anges för varje roll i tjänsten måste vara unika. Om du till exempel anger att en roll använder port 80 för HTTP och port 443 för HTTPS, kan du ange att en andra roll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs attributen för- `InputEndpoint` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett unikt namn för den externa slut punkten.|
|protokollhanterare|sträng|Krävs. Transport protokollet för den externa slut punkten. För en arbets roll är möjliga värden `HTTP` ,, `HTTPS` `UDP` eller `TCP` .|
|port|int|Krävs. Porten för den externa slut punkten. Du kan ange vilket port nummer du vill, men port numren som anges för varje roll i tjänsten måste vara unika.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|
|certifikat|sträng|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat som definieras av ett- `Certificate` element.|
|localPort|int|Valfritt. Anger en port som används för interna anslutningar på slut punkten. `localPort`Attributet mappar den externa porten på slut punkten till en intern port för en roll. Detta är användbart i scenarier där en roll måste kommunicera med en intern komponent på en annan port än den som exponeras externt.<br /><br /> Om detta inte anges är värdet för `localPort` samma som `port` attributet. Ange värdet `localPort` till "*" för att automatiskt tilldela en ej tilldelad port som kan upptäckas med körnings-API: et.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> `localPort`Attributet är bara tillgängligt med Azure SDK version 1,3 eller senare.|
|ignoreRoleInstanceStatus|boolean|Valfritt. När värdet för det här attributet är inställt på `true` , ignoreras statusen för en tjänst och slut punkten tas inte bort av belastningsutjämnaren. Ange det här värdet som `true` användbart för fel sökning av upptagna instanser av en tjänst. Standardvärdet är `false`. **Obs:** En slut punkt kan fortfarande ta emot trafik även när rollen inte är i ett klart tillstånd.|
|loadBalancerProbe|sträng|Valfritt. Namnet på belastnings Utjämnings avsökningen som är associerad med slut punkten för indatakälla. Mer information finns i [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint
`InternalEndpoint`Elementet beskriver en intern slut punkt för en arbets roll. En intern slut punkt är endast tillgänglig för andra roll instanser som körs i tjänsten. den är inte tillgänglig för klienter utanför tjänsten. En arbets roll kan ha upp till fem interna HTTP-, UDP-eller TCP-slutpunkter.

I följande tabell beskrivs attributen för- `InternalEndpoint` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett unikt namn för den interna slut punkten.|
|protokollhanterare|sträng|Krävs. Transport protokollet för den interna slut punkten. Möjliga värden är `HTTP` , `TCP` , `UDP` eller `ANY` .<br /><br /> Värdet `ANY` anger att alla protokoll, vilken port som helst tillåts.|
|port|int|Valfritt. Den port som används för interna belastningsutjämnade anslutningar på slut punkten. En belastningsutjämnad slut punkt använder två portar. Den port som används för den offentliga IP-adressen och porten som används på den privata IP-adressen. Dessa är vanligt vis inställda på samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> `Port`Attributet är bara tillgängligt med Azure SDK version 1,3 eller senare.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
`InstanceInputEndpoint`Elementet beskriver en slut punkt för instans ineffekt till en arbets roll. En instans av indatamängden är associerad med en speciell roll instans med hjälp av vidarebefordran av port i belastningsutjämnaren. Varje instans av ingångs slut punkt mappas till en speciell port från ett antal möjliga portar. Det här elementet är överordnat `AllocatePublicPortFrom` elementet.

`InstanceInputEndpoint`Elementet är bara tillgängligt med Azure SDK version 1,7 eller senare.

I följande tabell beskrivs attributen för- `InstanceInputEndpoint` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett unikt namn för slut punkten.|
|localPort|int|Krävs. Anger den interna port som alla roll instanser kommer att lyssna på för att ta emot inkommande trafik som vidarebefordras från belastningsutjämnaren. Möjliga värden ligger mellan 1 och 65535.|
|protokollhanterare|sträng|Krävs. Transport protokollet för den interna slut punkten. Möjliga värden är `udp` eller `tcp`. Används `tcp` för http/https-baserad trafik.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
I `AllocatePublicPortFrom` elementet beskrivs det offentliga port intervall som kan användas av externa kunder för att få åtkomst till varje instans slut punkt för indatamängden. Det offentliga (VIP) port numret tilldelas från det här intervallet och tilldelas till varje enskild roll instans slut punkt under klient distribution och uppdatering. Det här elementet är överordnat `FixedPortRange` elementet.

`AllocatePublicPortFrom`Elementet är bara tillgängligt med Azure SDK version 1,7 eller senare.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort
`FixedPort`Elementet anger porten för den interna slut punkten som aktiverar belastningsutjämnade anslutningar på slut punkten.

`FixedPort`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `FixedPort` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|port|int|Krävs. Porten för den interna slut punkten. Detta har samma resultat som när du ställer in `FixedPortRange` min och Max på samma port.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange
`FixedPortRange`Elementet anger det port intervall som är kopplat till den interna slut punkten eller instansens ingångs slut punkt och anger den port som används för belastningsutjämnade anslutningar på slut punkten.

> [!NOTE]
>  `FixedPortRange`Elementet fungerar på olika sätt beroende på vilket element det finns i. När `FixedPortRange` elementet är i `InternalEndpoint` elementet öppnas alla portar i belastningsutjämnaren inom intervallet för de minsta och högsta attributen för alla virtuella datorer som rollen körs på. När `FixedPortRange` elementet är i `InstanceInputEndpoint` elementet, öppnar det bara en port inom intervallet för de minsta och högsta attributen på varje virtuell dator som kör rollen.

`FixedPortRange`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `FixedPortRange` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|min|int|Krävs. Den minsta porten i intervallet. Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|
|max|sträng|Krävs. Den maximala porten i intervallet. Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|

##  <a name="certificates"></a><a name="Certificates"></a> Intyg
- `Certificates` Elementet beskriver samlingen av certifikat för en arbets roll. Det här elementet är överordnat `Certificate` elementet. En roll kan ha valfritt antal associerade certifikat. Mer information om hur du använder certifikat elementet finns i [ändra tjänst definitions filen med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Certifikatmallens
`Certificate`Elementet beskriver ett certifikat som är associerat med en arbets roll.

I följande tabell beskrivs attributen för- `Certificate` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Ett namn för det här certifikatet, som används för att referera till det när det är associerat med ett HTTPS- `InputEndpoint` element.|
|storeLocation|sträng|Krävs. Platsen för det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är `CurrentUser` och `LocalMachine` .|
|storeName|sträng|Krävs. Namnet på det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är de inbyggda lagrings namnen,,,,,,,, `My` `Root` `CA` `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` eller namnet på ett anpassat arkiv. Om du anger ett namn på en anpassad butik skapas butiken automatiskt.|
|permissionLevel|sträng|Valfritt. Anger de åtkomst behörigheter som tilldelas roll processerna. Om du bara vill att utökade processer ska kunna komma åt den privata nyckeln anger du `elevated` behörighet. `limitedOrElevated` behörighet tillåter alla roll processer att komma åt den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a> Kina
`Imports`Elementet beskriver en samling importera moduler för en arbets roll som lägger till komponenter i gäst operativ systemet. Det här elementet är överordnat `Import` elementet. Det här elementet är valfritt och en roll kan bara ha ett körnings block.

`Imports`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

##  <a name="import"></a><a name="Import"></a> Export
`Import`Elementet anger en modul som ska läggas till i gäst operativ systemet.

`Import`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `Import` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|moduleName|sträng|Krävs. Namnet på den modul som ska importeras. Giltiga import moduler är:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />– Diagnostik<br /><br /> Med RemoteAccess-och RemoteForwarder-modulerna kan du konfigurera roll instansen för fjärr skrivbords anslutningar. Mer information finns i [aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Med modulen diagnostik kan du samla in diagnostikdata för en roll instans|

##  <a name="runtime"></a><a name="Runtime"></a> Flöde
`Runtime`Elementet beskriver en samling miljö variabel inställningar för en arbets roll som styr körnings miljön för Azure-värd processen. Det här elementet är överordnat `Environment` elementet. Det här elementet är valfritt och en roll kan bara ha ett körnings block.

`Runtime`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `Runtime` elementet:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|executionContext|sträng|Valfritt. Anger kontexten där roll processen startas. Standard kontexten är `limited` .<br /><br /> -   `limited` – Processen startas utan administratörs behörighet.<br />-   `elevated` – Processen startas med administratörs behörighet.|

##  <a name="environment"></a><a name="Environment"></a> Miljö
`Environment`Elementet beskriver en samling miljö variabel inställningar för en arbets roll. Det här elementet är överordnat `Variable` elementet. En roll kan ha valfritt antal miljövariabler inställda.

##  <a name="variable"></a><a name="Variable"></a> Variabel
`Variable`Elementet anger en miljö variabel som ska anges i gäst operativ systemet.

`Variable`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `Variable` elementet:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|name|sträng|Krävs. Namnet på den miljö variabel som ska anges.|
|värde|sträng|Valfritt. Värdet som ska anges för miljö variabeln. Du måste inkludera antingen ett Value-attribut eller ett- `RoleInstanceValue` element.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue
`RoleInstanceValue`Elementet anger den xPath som värdet för variabeln hämtas från.

I följande tabell beskrivs attributen för- `RoleInstanceValue` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|XPath|sträng|Valfritt. Sökväg för distributions inställningar för instansen. Mer information finns i [konfigurations variabler med XPath](cloud-services-role-config-xpath.md).<br /><br /> Du måste inkludera antingen ett Value-attribut eller ett- `RoleInstanceValue` element.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a> EntryPoint
`EntryPoint`Elementet anger en Rolls start punkt. Det här elementet är överordnat `NetFxEntryPoint` elementen. Med de här elementen kan du ange ett annat program än standard WaWorkerHost.exe att fungera som roll start punkt.

`EntryPoint`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint`Elementet anger vilket program som ska köras för en roll.

> [!NOTE]
>  `NetFxEntryPoint`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för- `NetFxEntryPoint` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|assemblyName|sträng|Krävs. Sökväg och fil namn för sammansättningen som innehåller start punkten. Sökvägen är relativ i mappen **\\ %ROLEROOT%\Approot** (ange inte **\\ %ROLEROOT%\Approot** i `commandLine` , den antas). **% ROLEROOT%** är en miljö variabel som underhålls av Azure och den representerar rot katalogen för din roll. Mappen **\\ %ROLEROOT%\Approot** representerar programmappen för din roll.|
|targetFrameworkVersion|sträng|Krävs. Den version av .NET Framework som sammansättningen skapats på. Ett exempel är `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a> ProgramEntryPoint
`ProgramEntryPoint`Elementet anger vilket program som ska köras för en roll. Med- `ProgramEntryPoint` elementet kan du ange en program start punkt som inte baseras på en .net-sammansättning.

> [!NOTE]
>  `ProgramEntryPoint`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för- `ProgramEntryPoint` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Raden|sträng|Krävs. Sökvägen, fil namnet och alla kommando rads argument för programmet som ska köras. Sökvägen är relativ till mappen **%ROLEROOT%\Approot** (ange inte **%ROLEROOT%\Approot** i kommando raden, men den antas). **% ROLEROOT%** är en miljö variabel som underhålls av Azure och den representerar rot katalogen för din roll. Mappen **%ROLEROOT%\Approot** representerar programmappen för din roll.<br /><br /> Om programmet avslutas återvinns rollen, så normalt anger du att programmet ska fortsätta att köras, i stället för ett program som bara startar och kör en begränsad uppgift.|
|setReadyOnProcessStart|boolean|Krävs. Anger om roll instansen ska vänta på kommando rads programmet för att signalera att den har startats. Värdet måste anges till för tillfället `true` . Att ställa in värdet på `false` är reserverat för framtida användning.|

##  <a name="startup"></a><a name="Startup"></a> Startade
`Startup`Elementet beskriver en samling uppgifter som körs när rollen startas. Det här elementet kan vara överordnat `Variable` elementet. Mer information om hur du använder roll start åtgärder finns i [så här konfigurerar du Start åtgärder](cloud-services-startup-tasks.md). Det här elementet är valfritt och en roll kan bara ha ett start block.

I följande tabell beskrivs attribut för- `Startup` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|prioritet|int|Endast för internt bruk.|

##  <a name="task"></a><a name="Task"></a> Uppgift
`Task`Elementet anger start aktivitet som äger rum när rollen startas. Start uppgifter kan användas för att utföra uppgifter som förbereder rollen för att köra sådana installations program komponenter eller köra andra program. Aktiviteter körs i den ordning som de visas i `Startup` element blocket.

`Task`Elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för- `Task` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Raden|sträng|Krävs. Ett skript, till exempel en CMD-fil, som innehåller de kommandon som ska köras. Start kommando och kommandofiler måste sparas i ANSI-format. Fil format som anger en byte-ordning markör vid början av filen kommer inte att bearbetas korrekt.|
|executionContext|sträng|Anger kontexten som skriptet körs i.<br /><br /> -   `limited` [Standard] – kör med samma behörigheter som den roll som är värd för processen.<br />-   `elevated` – Kör med administratörs behörighet.|
|taskType|sträng|Anger kommandots körnings beteende.<br /><br /> -   `simple` [Standard] – systemet väntar på att aktiviteten avslutas innan andra aktiviteter startas.<br />-   `background` – Systemet väntar inte på att aktiviteten ska avslutas.<br />-   `foreground` – Påminner om bakgrunden, förutom att rollen inte startas om förrän alla förgrunds uppgifter avslutas.|

##  <a name="contents"></a><a name="Contents"></a> Innehåller
`Contents`Elementet beskriver samlingen av innehåll för en arbets roll. Det här elementet är överordnat `Content` elementet.

`Contents`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

##  <a name="content"></a><a name="Content"></a> Innehåll
`Content`Elementet definierar käll platsen för det innehåll som ska kopieras till den virtuella Azure-datorn och mål Sök vägen som den kopieras till.

`Content`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för- `Content` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|mål|sträng|Krävs. Plats på den virtuella Azure-dator som innehållet placeras på. Den här platsen är relativ till mappen **%ROLEROOT%\Approot**.|

Det här elementet är elementets överordnade element `SourceDirectory` .

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory
`SourceDirectory`Elementet definierar den lokala katalogen som innehållet kopieras från. Använd det här elementet för att ange det lokala innehåll som ska kopieras till den virtuella Azure-datorn.

`SourceDirectory`Elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för- `SourceDirectory` elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|path|sträng|Krävs. Relativ eller absolut sökväg till en lokal katalog vars innehåll ska kopieras till den virtuella Azure-datorn. Det finns stöd för att utöka miljövariabler i katalog Sök vägen.|

## <a name="see-also"></a>Se även
[Definitions schema för moln tjänst (klassisk)](schema-csdef-file.md)



