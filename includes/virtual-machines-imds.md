---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 554730919d4226c07e099d5e457cd0fd20dbad30
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510707"
---
Azure-Instance Metadata Service (IMDS) innehåller information om de virtuella dator instanser som körs. Du kan använda den för att hantera och konfigurera dina virtuella datorer.
Den här informationen omfattar SKU, lagring, nätverkskonfigurationer och kommande underhålls händelser. En fullständig lista över tillgängliga data finns i [Sammanfattning av slut punkts kategorier](#endpoint-categories).

IMDS är tillgängligt för att köra instanser av virtuella datorer (VM) och virtuella datorers skalnings uppsättnings instanser. Alla slut punkter har stöd för virtuella datorer som skapats och hanterats med hjälp av [Azure Resource Manager](/rest/api/resources/). Endast den attesterings bara kategorin och nätverks delen av instans kategorin stöder virtuella datorer som skapats med hjälp av den klassiska distributions modellen. Den attesterings bara slut punkten gör det bara till en begränsad omfattning.

IMDS är en REST API som är tillgänglig på en välkänd, icke-dirigerbart IP-adress ( `169.254.169.254` ). Du kan bara komma åt det inifrån den virtuella datorn. Kommunikationen mellan VM-och IMDS lämnar aldrig värden.
Låt dina HTTP-klienter kringgå Webbproxyservrar i den virtuella datorn vid frågor till IMDS och behandla `169.254.169.254` samma som [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Användning

### <a name="access-azure-instance-metadata-service"></a>Få åtkomst till Azure Instance Metadata Service

Skapa en virtuell dator från [Azure Resource Manager](/rest/api/resources/) eller [Azure Portal](https://portal.azure.com)och Använd följande exempel för att få åtkomst till IMDS.
Fler exempel finns i [exempel på Azure instance metadata](https://github.com/microsoft/azureimds).

Här är exempel kod för att hämta alla metadata för en instans. För att få åtkomst till en speciell data källa, se [slut punkts kategorier](#endpoint-categories) för en översikt över alla tillgängliga funktioner.

**Förfrågan**

> [!IMPORTANT]
> I det här exemplet kringgås proxyservrar. Du **måste** kringgå proxyservrar när du frågar efter IMDS. Se [proxyservrar](#proxies) för ytterligare information.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Säkerhet och autentisering

Instance Metadata Service kan bara nås från en virtuell dator som körs på en icke-flyttbar IP-adress. Virtuella datorer är begränsade för att interagera med metadata/funktioner som är kopplade till sig själva. API: et är endast HTTP och lämnar aldrig värden.

För att säkerställa att begär Anden direkt är avsedda för IMDS och förhindra oavsiktlig eller oönskad omdirigering av begär Anden:
- **Måste** innehålla rubriken `Metadata: true`
- Får **inte** innehålla ett `X-Forwarded-For` sidhuvud

En begäran som inte uppfyller **båda** dessa krav kommer att avvisas av tjänsten.

> [!IMPORTANT]
> IMDS är **inte** en kanal för känsliga data. API: et är oautentiserad och öppen för alla processer på den virtuella datorn. Information som exponeras genom den här tjänsten bör betraktas som delad information till alla program som körs i den virtuella datorn.

## <a name="proxies"></a>Proxy

IMDS är **inte** avsett att användas bakom en proxyserver och detta stöds inte. De flesta HTTP-klienter tillhandahåller ett alternativ som du kan använda för att inaktivera proxyservrar på dina begär Anden och den här funktionen måste användas vid kommunikation med IMDS. Mer information finns i klientens dokumentation.

> [!IMPORTANT]
> Även om du inte känner till någon proxykonfiguration i din miljö **måste du fortfarande åsidosätta eventuella standardinställningar för klientens proxyserver**. Proxykonfigurationen kan identifieras automatiskt och det går inte att kringgå de här konfigurationerna som visar att du kan avbrotts risker om datorns konfiguration ändras i framtiden.

## <a name="rate-limiting"></a>Frekvensbegränsning

I allmänhet är begär anden till IMDS begränsade till 5 begär Anden per sekund. Begär Anden som överskrider detta tröskelvärde avvisas med 429 svar. Begär anden till den [hanterade identitets](#managed-identity) kategorin är begränsade till 20 begär Anden per sekund och 5 samtidiga begär Anden.

## <a name="http-verbs"></a>HTTP-verb

Följande HTTP-verb stöds för närvarande:

| Verb | Beskrivning |
|------|-------------|
| `GET` | Hämta den begärda resursen

## <a name="parameters"></a>Parametrar

Slut punkter kan ha stöd för obligatoriska och/eller valfria parametrar. Mer information finns i [schema](#schema) och dokumentation för den aktuella slut punkten.

### <a name="query-parameters"></a>Frågeparametrar

IMDS-slutpunkter stöder HTTP-frågeparametrar. Exempel: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Anger parametrarna:

| Name | Värde |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Begär Anden med dubbla frågeparametrar kommer att avvisas.

### <a name="route-parameters"></a>Flödes parametrar

För vissa slut punkter som returnerar större JSON-blobbar stöder vi tillägg av väg parametrar till begär ande slut punkten för att filtrera ned till en delmängd av svaret: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Parametrarna motsvarar de index/nycklar som används för att gå nedåt i JSON-objektet. du interagerar med en parsad representation.

Returnerar till exempel `/metatadata/instance` JSON-objektet:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Om vi vill filtrera svaret nedåt till bara Compute-egenskapen skickar vi begäran: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

På samma sätt gäller att om vi vill filtrera till en kapslad egenskap eller ett enskilt mat ris element behåller vi tillägg av nycklar: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtrerar till det första elementet från `Network.interface` egenskapen och returnerar:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> När du filtrerar till en lövnod `format=json` fungerar det inte. För dessa frågor `format=text` måste anges explicit eftersom standardformatet är JSON.

## <a name="schema"></a>Schema

### <a name="data-format"></a>Dataformat

Som standard returnerar IMDS data i JSON-format ( `Content-Type: application/json` ). Slut punkter som stöder svars filtrering (se [Route-parametrar](#route-parameters)) stöder dock också formatet `text` .

Om du vill komma åt ett svar som inte är standardformat anger du det begärda formatet som en frågesträngparametern i begäran. Exempel:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

I JSON-svar kommer alla primitiver att vara av typen `string` och saknade eller inaktuella värden är alltid inkluderade, men anges som en tom sträng.

### <a name="versioning"></a>Versionshantering

IMDS är versions hantering och anger att API-versionen i HTTP-begäran är obligatorisk. Det enda undantaget till det här kravet är [versions](#versions) slut punkten, som kan användas för att dynamiskt hämta tillgängliga API-versioner.

När nya versioner läggs till kan äldre versioner fortfarande nås för kompatibilitet om skripten har beroenden för vissa data format.

När du inte anger en version får du ett fel meddelande med en lista över de senaste versionerna som stöds:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>API-versioner som stöds
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

En fullständig Swagger-definition för IMDS finns på: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regional tillgänglighet

Tjänsten är **allmänt tillgänglig** i alla Azure-moln.

## <a name="root-endpoint"></a>Rot slut punkt

Rot slut punkten är `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Slut punkts kategorier

IMDS-API: et innehåller flera slut punkts kategorier som representerar olika data källor, som var och en innehåller en eller flera slut punkter. Se varje kategori för information.

| Kategori rot | Beskrivning | Version introducerad |
|---------------|-------------|--------------------|
| `/metadata/attested` | Se [attesterade data](#attested-data) | 2018-10-01
| `/metadata/identity` | Se [hanterad identitet via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Se [instansens metadata](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Se [hämta Load Balancer metadata via IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Se [schemalagda händelser via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Se [versioner](#versions) | Ej tillämpligt

## <a name="versions"></a>Versioner

> [!NOTE]
> Den här funktionen släpptes tillsammans med version 2020-10-01, som för närvarande är distribuerad och kanske ännu inte är tillgänglig i varje region.

### <a name="list-api-versions"></a>Visa lista över API-versioner

Returnerar en uppsättning API-versioner som stöds.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametrar

Ingen (den här slut punkten har inte versions hantering).

#### <a name="response"></a>Svarsåtgärder

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadata för instans

### <a name="get-vm-metadata"></a>Hämta metadata för virtuell dator

Exponerar viktiga metadata för den virtuella dator instansen, inklusive beräkning, nätverk och lagring. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametrar

| Name | Obligatorisk/valfri | Beskrivning |
|------|-------------------|-------------|
| `api-version` | Krävs | Den version som används för att betjäna begäran.
| `format` | Valfritt | `json`Svarets format (eller `text` ). * Obs: kan krävas när parametrarna för begäran används

Den här slut punkten stöder svars filtrering via [väg parametrar](#route-parameters).

#### <a name="response"></a>Svarsåtgärder

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Schema analys:

**Beräkning**

| Data | Beskrivning | Version introducerad |
|------|-------------|--------------------|
| `azEnvironment` | Azure-miljö där den virtuella datorn körs i | 2018-10-01
| `customData` | Den här funktionen är för närvarande inaktive rad. Vi kommer att uppdatera den här dokumentationen när den blir tillgänglig | 2019-02-01
| `evictionPolicy` | Anger hur en [virtuell dator](../articles/virtual-machines/spot-vms.md) ska avlägsnas. | 2020-12-01
| `isHostCompatibilityLayerVm` | Identifierar om den virtuella datorn körs på värdens kompatibilitetsnivå | 2020-06-01
| `licenseType` | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Detta finns endast för AHB-aktiverade virtuella datorer | 2020-09-01
| `location` | Azure-regionen som den virtuella datorn körs i | 2017-04-02
| `name` | Namn på den virtuella datorn | 2017-04-02
| `offer` | Erbjudande information för den virtuella dator avbildningen och finns bara för avbildningar som distribuerats från Azures avbildnings Galleri | 2017-04-02
| `osProfile.adminUsername` | Anger namnet på administratörs kontot | 2020-07-15
| `osProfile.computerName` | Anger namnet på datorn | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Anger om lösenordsautentisering är inaktiverat. Detta finns bara för virtuella Linux-datorer | 2020-10-01
| `osType` | Linux eller Windows | 2017-04-02
| `placementGroupId` | [Placerings grupp](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för den virtuella datorns skalnings uppsättning | 2017-08-01
| `plan` | [Planera](/rest/api/compute/virtualmachines/createorupdate#plan) som innehåller namn, produkt och utgivare för en virtuell dator om det är en Azure Marketplace-avbildning | 2018-04-02
| `platformUpdateDomain` |  [Uppdatera den domän](../articles/virtual-machines/availability.md) som den virtuella datorn körs i | 2017-04-02
| `platformFaultDomain` | [Feldomän](../articles/virtual-machines/availability.md) som den virtuella datorn körs i | 2017-04-02
| `priority` | Prioritet för den virtuella datorn. Se [virtuella datorer för virtuella datorer](../articles/virtual-machines/spot-vms.md) för mer information | 2020-12-01
| `provider` | Provider för den virtuella datorn | 2018-10-01
| `publicKeys` | [Samling offentliga nycklar](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) som har tilldelats den virtuella datorn och sökvägar | 2018-04-02
| `publisher` | Utgivare av VM-avbildningen | 2017-04-02
| `resourceGroupName` | [Resurs grupp](../articles/azure-resource-manager/management/overview.md) för den virtuella datorn | 2017-08-01
| `resourceId` | Resursens [fullständigt kvalificerade](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | En speciell SKU för VM-avbildningen | 2017-04-02
| `securityProfile.secureBootEnabled` | Identifierar om UEFI säker start är aktiverat på den virtuella datorn | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Identifierar om den virtuella Trusted Platform Module (TPM) är aktive rad på den virtuella datorn | 2020-06-01
| `storageProfile` | Se lagrings profil nedan | 2019-06-01
| `subscriptionId` | Azure-prenumeration för den virtuella datorn | 2017-08-01
| `tags` | [Taggar](../articles/azure-resource-manager/management/tag-resources.md) för den virtuella datorn  | 2017-08-01
| `tagsList` | Taggar formaterade som en JSON-matris för enklare programmerings parsning  | 2019-06-04
| `version` | Version av VM-avbildningen | 2017-04-02
| `vmId` | [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
| `vmScaleSetName` | [Skal uppsättnings namn för virtuell](../articles/virtual-machine-scale-sets/overview.md) dator för den virtuella datorns skalnings uppsättning | 2017-12-01
| `vmSize` | [VM-storlek](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Tillgänglighets zon](../articles/availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01

**Lagringsprofil**

Lagrings profilen för en virtuell dator är uppdelad i tre kategorier: avbildnings referens, OS-disk och data diskar.

Objektet bild referens innehåller följande information om operativ system avbildningen:

| Data | Beskrivning |
|------|-------------|
| `id` | Resurs-ID
| `offer` | Erbjudande för plattformen eller Marketplace-avbildningen
| `publisher` | Avbildnings utgivare
| `sku` | Bild-SKU
| `version` | Version av plattformen eller Marketplace-avbildningen

Objektet OS-disk innehåller följande information om den OS-disk som används av den virtuella datorn:

| Data | Beskrivning |
|------|-------------|
| `caching` | Krav för cachelagring
| `createOption` | Information om hur den virtuella datorn skapades
| `diffDiskSettings` | Inställningar för tillfälliga diskar
| `diskSizeGB` | Disk storlek i GB
| `image`   | Virtuell hård disk för käll användar avbildning
| `lun`     | Diskens logiska enhets nummer
| `managedDisk` | Parametrar för hanterade diskar
| `name`    | Disknamn
| `vhd`     | Virtuell hårddisk
| `writeAcceleratorEnabled` | Huruvida writeAccelerator har Aktiver ATS på disken

Data disks-matrisen innehåller en lista över data diskar som är anslutna till den virtuella datorn. Varje data disk objekt innehåller följande information:

Data | Beskrivning |
-----|-------------|
| `caching` | Krav för cachelagring
| `createOption` | Information om hur den virtuella datorn skapades
| `diffDiskSettings` | Inställningar för tillfälliga diskar
| `diskSizeGB` | Disk storlek i GB
| `encryptionSettings` | Krypterings inställningar för disken
| `image` | Virtuell hård disk för käll användar avbildning
| `managedDisk` | Parametrar för hanterade diskar
| `name` | Disknamn
| `osType` | Typ av operativ system som ingår i disken
| `vhd` | Virtuell hårddisk
| `writeAcceleratorEnabled` | Huruvida writeAccelerator har Aktiver ATS på disken

**Nätverk**

| Data | Beskrivning | Version introducerad |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokal IPv4-adress för den virtuella datorn | 2017-04-02
| `ipv4.publicIpAddress` | Offentlig IPv4-adress för den virtuella datorn | 2017-04-02
| `subnet.address` | Den virtuella datorns under näts adress | 2017-04-02
| `subnet.prefix` | Undernätsprefixet, exempel 24 | 2017-04-02
| `ipv6.ipAddress` | Lokal IPv6-adress för den virtuella datorn | 2017-04-02
| `macAddress` | Mac-adress för virtuell dator | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Exempel 1: spåra virtuell dator som körs på Azure

Som tjänst leverantör kan du behöva spåra antalet virtuella datorer som kör program varan eller ha agenter som behöver spåra unika virtuella datorer. Om du vill kunna hämta ett unikt ID för en virtuell dator använder du `vmId` fältet från instance metadata service.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Exempel 2: placering av olika data repliker

För vissa scenarier är placeringen av olika data repliker av primär betydelse. Till exempel kan [HDFS-replik placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller container placering via en [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kräva att du känner till `platformFaultDomain` och `platformUpdateDomain` den virtuella datorn körs på.
Du kan också använda [Tillgänglighetszoner](../articles/availability-zones/az-overview.md) för instanserna för att fatta beslut.
Du kan fråga dessa data direkt via IMDS.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Exempel 3: Hämta VM-Taggar

VM-Taggar ingår i instans-API: t under slut punkt för instans/beräkning/taggar.
Taggar kan ha tillämpats på den virtuella Azure-datorn för att logiskt organisera dem i en taxonomi. Taggarna som tilldelas till en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags`Fältet är en sträng med taggarna avgränsade med semikolon. Dessa utdata kan vara ett problem om semikolon används i själva taggarna. Om en parser skrivs för att program mässigt extrahera taggarna bör du förlita dig på `tagsList` fältet. `tagsList`Fältet är en JSON-matris utan avgränsare och därmed lättare att parsa. TagsList som är tilldelad en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Exempel 4: Hämta mer information om den virtuella datorn under support ärendet

Som tjänst leverantör kan du få ett support samtal där du vill ha mer information om den virtuella datorn. Att be kunden att dela dina beräknings-metadata kan ge grundläggande information om support teknikern för att veta om vilken typ av virtuell dator som finns i Azure.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Exempel 5: Hämta Azure-miljön där den virtuella datorn körs

Azure har olika suveräna moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland behöver du Azure-miljön för att kunna göra vissa körnings beslut. I följande exempel visas hur du kan få det här beteendet.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

Molnet och värdena i Azure-miljön visas här.

| Moln | Azure-miljö |
|-------|-------------------|
| [Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Exempel 6: Hämta nätverks information

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Exempel 7: Hämta offentlig IP-adress

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Bestyrkade data

### <a name="get-attested-data"></a>Hämta bestyrkade data

IMDS hjälper till att tillhandahålla garantier för att de data som tillhandahålls kommer från Azure. Microsoft loggar in en del av den här informationen, så du kan bekräfta att en avbildning i Azure Marketplace är den som du kör på Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametrar

| Name | Obligatorisk/valfri | Beskrivning |
|------|-------------------|-------------|
| `api-version` | Krävs | Den version som används för att betjäna begäran.
| `nonce` | Valfritt | En 10-siffrig sträng som fungerar som en kryptografisk nonce. Om inget värde anges använder IMDS den aktuella UTC-tidsstämpeln.

#### <a name="response"></a>Svarsåtgärder

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> På grund av IMDS-mekanismen kan ett tidigare cachelagrat nonce-värde returneras.

Signatur-bloben är en [PKCS7](https://aka.ms/pkcs7)-signerad version av dokumentet. Den innehåller certifikatet som används för signering tillsammans med viss VM-specifik information.

För virtuella datorer som skapats med hjälp av Azure Resource Manager innehåller dokumentet,,,, `vmId` `sku` för att `nonce` `subscriptionId` `timeStamp` skapa och upphör ande av dokumentet, samt information om hur du planerar informationen om avbildningen. Plan informationen är bara ifylld för Azure Marketplace-avbildningar.

För virtuella datorer som skapats med den klassiska distributions modellen är det bara `vmId` garanterat att de är ifyllda. Du kan extrahera certifikatet från svaret och använda det för att bekräfta att svaret är giltigt och kommer från Azure.

Det avkodade dokumentet innehåller följande fält:

| Data | Beskrivning | Version introducerad |
|------|-------------|--------------------|
| `licenseType` | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Detta är endast tillgängligt för AHB-aktiverade virtuella datorer. | 2020-09-01
| `nonce` | En sträng som kan anges med begäran. Om inget `nonce` har angetts används den aktuella UTC-tidsstämpeln för universell tid. | 2018-10-01
| `plan` | [Avbildnings planen för Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Innehåller plan-ID (namn), produkt avbildning eller erbjudande (produkt) och utgivar-ID (utgivare). | 2018-10-01
| `timestamp.createdOn` | UTC-tidsstämpeln för när det signerade dokumentet skapades | 2018-20-01
| `timestamp.expiresOn` | UTC-tidsstämpeln för när det signerade dokumentet upphör att gälla | 2018-10-01
| `vmId` | [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2018-10-01
| `subscriptionId` | Azure-prenumeration för den virtuella datorn | 2019-04-30
| `sku` | En speciell SKU för VM-avbildningen | 2019-11-01

> [!NOTE]
> För klassiska virtuella datorer (icke-Azure Resource Manager) är det bara vmId som är garanterat ifyllt.

Exempel dokument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Exempel 1: kontrol lera att den virtuella datorn körs i Azure

Leverantörer i Azure Marketplace vill se till att deras program vara licensieras att köras endast i Azure. Om någon kopierar den virtuella hård disken till en lokal miljö måste leverantören kunna identifiera den. Med IMDS kan dessa leverantörer Hämta signerade data som endast garanterar svar från Azure.

> [!NOTE]
> Det här exemplet kräver att JQ-verktyget installeras.

**Signaturverifiering**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Kontrol lera att signaturen kommer från Microsoft Azure och kontrol lera om det finns fel i certifikat kedjan.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Resultat**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Kontrol lera att signaturen är från Microsoft Azure och kontrol lera om det finns fel i certifikat kedjan.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> På grund av IMDS-mekanismen kan ett tidigare cachelagrat `nonce` värde returneras.

`nonce`I det signerade dokumentet kan jämföras om du har angett en `nonce` parameter i den första begäran.

> [!NOTE]
> Certifikatet för det offentliga molnet och varje suveräna moln är annorlunda.

| Moln | Certifikat |
|-------|-------------|
| [Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> Certifikaten kanske inte har en exakt matchning av `metadata.azure.com` för det offentliga molnet. Därför bör certifierings valideringen tillåta ett eget namn från valfri `.metadata.azure.com` under domän.

I de fall där det mellanliggande certifikatet inte kan hämtas på grund av nätverks begränsningar under verifieringen kan du fästa det mellanliggande certifikatet. Azure rullar över certifikaten, som är standard-PKI-praxis. Du måste uppdatera de fästa certifikaten när förnyelsen sker. När en ändring av uppdateringen av mellanliggande certifikat planeras, uppdateras Azure-bloggen och Azure-kunder meddelas. 

Du kan hitta mellanliggande certifikat i [PKI-lagringsplatsen](https://www.microsoft.com/pki/mscorp/cps/default.htm). De mellanliggande certifikaten för varje region kan vara olika.

> [!NOTE]
> Mellanliggande certifikat för Azure Kina 21Vianet kommer från DigiCert global rot certifikat utfärdare, i stället för Baltimore.
Om du har fäst mellanliggande certifikat för Azure Kina som en del av en rot kedjas auktoritets ändring, måste de mellanliggande certifikaten uppdateras.


## <a name="managed-identity"></a>Hanterad identitet

En hanterad identitet som tilldelas av systemet kan aktive ras på den virtuella datorn. Du kan också tilldela en eller flera användare tilldelade hanterade identiteter till den virtuella datorn.
Du kan sedan begära token för hanterade identiteter från IMDS. Använd dessa tokens för att autentisera med andra Azure-tjänster, t. ex. Azure Key Vault.

Detaljerade anvisningar om hur du aktiverar den här funktionen finns i [Hämta en](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)åtkomsttoken.

## <a name="load-balancer-metadata"></a>Load Balancer metadata
När du placerar instanser av virtuella datorer eller virtuella datorer bakom en Azure-Standard Load Balancer kan du använda IMDS för att hämta metadata relaterade till belastningsutjämnaren och instanserna. Mer information finns i [Hämta information om belastnings utjämning](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Schemalagda händelser
Du kan hämta status för de schemalagda händelserna med hjälp av IMDS. Sedan kan användaren ange en uppsättning åtgärder som ska köras vid de här händelserna. Mer information finns i avsnittet [schemalagda händelser för Linux](../articles/virtual-machines/linux/scheduled-events.md) eller [schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Exempel kod på olika språk

I följande tabell visas exempel på hur du anropar IMDS med olika språk i den virtuella datorn:

| Språk | Exempel |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Fel och fel sökning

Om det inte går att hitta ett data element eller en felaktig begäran, returnerar Instance Metadata Service vanliga HTTP-fel. Exempel:

| HTTP-statuskod | Anledning |
|------------------|--------|
| `200 OK` | Begäran lyckades.
| `400 Bad Request` | Saknad `Metadata: true` rubrik eller saknad parameter `format=json` vid fråga till en lövnod
| `404 Not Found` | Det begärda elementet finns inte
| `405 Method Not Allowed` | HTTP-metoden (verb) stöds inte på slut punkten.
| `410 Gone` | Försök igen om en stund i högst 70 sekunder
| `429 Too Many Requests` | Gränsen för API- [frekvens](#rate-limiting) har överskridits
| `500 Service Error` | Försök igen om en stund

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Jag får felet `400 Bad Request, Required metadata header not specified` . Vad betyder detta?**

IMDS kräver att rubriken `Metadata: true` skickas i begäran. Att skicka den här rubriken i REST-anropet ger åtkomst till IMDS.

**Varför får jag inte beräknings information för min virtuella dator?**

För närvarande stöder IMDS endast instanser som skapats med Azure Resource Manager.

**Jag skapade den virtuella datorn via Azure Resource Manager en tid sedan. Varför ser jag inte information om att beräkna metadata?**

Om du har skapat den virtuella datorn efter september 2016 lägger du till en [tagg](../articles/azure-resource-manager/management/tag-resources.md) för att börja se Compute metadata. Om du har skapat den virtuella datorn före september 2016 lägger du till eller tar bort tillägg eller data diskar till VM-instansen för att uppdatera metadata.

**Varför ser jag inte alla data som är fyllda för en ny version?**

Om du har skapat den virtuella datorn efter september 2016 lägger du till en [tagg](../articles/azure-resource-manager/management/tag-resources.md) för att börja se Compute metadata. Om du har skapat den virtuella datorn före september 2016 lägger du till eller tar bort tillägg eller data diskar till VM-instansen för att uppdatera metadata.

**Varför får jag fel meddelandet `500 Internal Server Error` `410 Resource Gone` ?**

Gör om begäran. Mer information finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults). Om problemet kvarstår kan du skapa ett support ärende i Azure Portal för den virtuella datorn.

**Kommer detta att fungera för instanser av skalnings uppsättningar för virtuella datorer?**

Ja, IMDS är tillgängligt för instanser av skalnings uppsättningar för virtuella datorer.

**Jag uppdaterade mina taggar i skalnings uppsättningar för virtuella datorer, men de visas inte i instanserna (till skillnad från virtuella datorer med en instans). Är jag något fel?**

För närvarande används taggar för skalnings uppsättningar för virtuella datorer endast på den virtuella datorn vid omstart, avbildning eller disk ändring till instansen.

**Varför har min begäran nått sin tids gräns för mitt samtal till tjänsten?**

Metadata-anrop måste göras från den primära IP-adress som tilldelats till det primära nätverkskortet på den virtuella datorn. Om du har ändrat dina vägar måste du dessutom ha en väg för 169.254.169.254-/32-adressen i den virtuella datorns lokala routningstabell.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Dumpa din lokala routningstabell och leta efter IMDS-posten. Exempel:
    ```console
    > route print
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
      169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
    ... (continues) ...
    ```
1. Kontrol lera att det finns en väg för `169.254.169.254` och anteckna motsvarande nätverks gränssnitt (till exempel `172.16.69.7` ).
1. Dumpa gränssnitts konfigurationen och hitta det gränssnitt som motsvarar den som refereras i routningstabellen, som anger MAC-adressen (fysisk).
    ```console
    > ipconfig /all
    ... (continues) ...
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
       Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
       Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
       IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
    ... (continues) ...
    ```
1. Bekräfta att gränssnittet motsvarar det primära NÄTVERKSKORTet för den virtuella datorn och den primära IP-adressen. Du kan hitta det primära NÄTVERKSKORTet och IP-adressen genom att titta på nätverks konfigurationen i Azure Portal eller genom att titta på den med Azure CLI. Anteckna de privata IP-adresserna (och MAC-adressen om du använder CLI). Här är ett PowerShell CLI-exempel:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. Om de inte matchar, uppdaterar du routningstabellen så att det primära NÄTVERKSKORTet och IP-adressen är riktade.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Dumpa din lokala routningstabell med ett kommando som `netstat -r` och leta efter posten IMDS (t. ex.):
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Kontrol lera att det finns en väg för `169.254.169.254` och notera motsvarande nätverks gränssnitt (t. ex. `eth0` ).
1. Dumpa gränssnitts konfigurationen för motsvarande gränssnitt i routningstabellen (Observera att det exakta namnet på konfigurations filen kan variera)
    ```console
    ~$ cat /etc/netplan/50-cloud-init.yaml
    network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 00:0d:3a:e4:c7:2e
            set-name: eth0
    version: 2
    ```
1. Om du använder en dynamisk IP-adress noterar du MAC-adressen. Om du använder en statisk IP-adress kan du anteckna IP-adresser och/eller MAC-adressen i listan.
1. Bekräfta att gränssnittet motsvarar det primära NÄTVERKSKORTet för den virtuella datorn och den primära IP-adressen. Du kan hitta det primära NÄTVERKSKORTet och IP-adressen genom att titta på nätverks konfigurationen i Azure Portal eller genom att titta på den med Azure CLI. Anteckna de privata IP-adresserna (och MAC-adressen om du använder CLI). Här är ett PowerShell CLI-exempel:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: ipexample606 True 00-0D-3A-E4-C7-2E
    ```
1. Om de inte matchar uppdaterar du routningstabellen så att det primära NÄTVERKSKORTet/IP-adressen är mål.

---

**Kluster för växling vid fel i Windows Server**

När du frågar efter IMDS med redundanskluster, är det ibland nödvändigt att lägga till en väg i routningstabellen. Gör så här:

1. Öppna en kommandotolk med administratörsbehörighet.

1. Kör följande kommando och anteckna adressen till gränssnittet för nätverks målet ( `0.0.0.0` ) i IPv4-routningstabellen.

```bat
route print
```

> [!NOTE]
> Följande exempel på utdata är från en virtuell Windows Server-dator med redundanskluster aktiverat. För enkelhetens skull innehåller utdata endast IPv4-routningstabellen.

```
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Kör följande kommando och Använd adressen för gränssnittet för nätverks mål ( `0.0.0.0` ), som är ( `10.0.1.10` ) i det här exemplet.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Support

Om du inte kan få svar på metadata efter flera försök, kan du skapa ett support ärende i Azure Portal.

## <a name="product-feedback"></a>Produktfeedback

Du kan ge feedback om produkter och idéer till vår kanal för feedback från användare under Virtual Machines > Instance Metadata Service här: https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>Nästa steg

[Hämta en åtkomsttoken för den virtuella datorn](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Schemalagda händelser för Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md)
