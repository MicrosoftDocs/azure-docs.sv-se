---
title: Uppdatera ett kluster så att det använder certifikatets nätverks namn
description: Lär dig hur du konverterar ett Azure Service Fabric Cluster-certifikat från tumavtryck-baserade deklarationer till vanliga namn.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900804"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Konvertera kluster certifikat från tumavtryck-baserade deklarationer till vanliga namn

Signaturen för ett certifikat (kallas ofta för ett tumavtryck) är unik. Ett kluster certifikat som deklareras av tumavtryck refererar till en angiven instans av ett certifikat. Den här specificiteten gör certifikat förnyelse och hantering i allmänhet, svårt och explicit. Varje ändring kräver samordning av uppgraderingar av klustret och de underliggande dator värdarna.

Att konvertera ett Azure Service Fabric-klusters certifikat deklarationer från tumavtryck till deklarationer som baseras på certifikatets allmänna ämnes namn (CN) fören klar hanteringen avsevärt. I synnerhet krävs det inte längre en kluster uppgradering för att rulla över ett certifikat. Den här artikeln beskriver hur du konverterar ett befintligt kluster till CN-baserade deklarationer utan drift avbrott.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Flytta till certifikat utfärdare-signerade certifikat

Säkerheten för ett kluster vars certifikat deklareras av tumavtryck rest på det faktum att det är omöjligt eller inte är möjligt att falska ett certifikat med samma signatur som ett annat. I det här fallet är uthärkomsten för certifikatet mindre viktig, så att självsignerade certifikat är tillräckliga.

Som kontrast är säkerheten för ett kluster vars certifikat deklareras av CN-flöden från det implicita förtroendet som kluster ägaren har i sin certifikat leverantör. Providern är den PKI-tjänst (Public Key Infrastructure) som utfärdade certifikatet. Förtroendet baseras bland annat på PKI: s certifierings metoder, oavsett om deras operativa säkerhet granskas och godkänns av andra betrodda parter, och så vidare.

Kluster ägaren måste också ha detaljerad information om vilka certifikat utfärdare som utfärdar sina certifikat, eftersom detta är en grundläggande aspekt av att verifiera certifikat efter ämne. Detta innebär också att självsignerade certifikat är helt olämpliga för detta ändamål. Bokstavligen vem som helst kan generera ett certifikat med ett specifikt ämne.

Ett certifikat som deklareras av CN anses vanligt vis vara giltigt om:

* Dess kedja kan skapas utan problem.
* Ämnet har det förväntade CN-elementet.
* Utfärdaren (omedelbar eller högre i kedjan) är betrodd av agenten som utför verifieringen.

Service Fabric stöder deklaration av certifikat med CN på två sätt:

* Med *implicita* utfärdare, vilket innebär att kedjan måste sluta med ett förtroende ankare.
* Med utfärdare som deklarerats av tumavtryck, som kallas utfärdande av utfärdare.

Mer information finns i [vanliga deklarationer för certifikat validering i vanliga namn](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Om du vill konvertera ett kluster med hjälp av ett självsignerat certifikat som deklarerats av tumavtryck till CN måste målet, CA-signerat certifikat först införas i klustret med tumavtrycket. Det är bara konverteringen från tumavtryck till CN möjligt.

I test syfte *kan* ett självsignerat certifikat deklareras av CN, men endast om utfärdaren är fäst på sitt eget tumavtryck. Av säkerhets synpunkt är den här åtgärden nästan likvärdig med att deklarera samma certifikat med tumavtrycket. En lyckad konvertering av den här typen garanterar inte en lyckad konvertering från tumavtryck till CN med ett CA-signerat certifikat. Vi rekommenderar att du testar konverteringen med ett korrekt CA-signerat certifikat. Det finns kostnads fria alternativ för det här testet.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Ladda upp certifikatet och installera det i skalnings uppsättningen

I Azure omfattar den rekommenderade mekanismen för hämtning och etablering av certifikat Azure Key Vault och dess verktyg. Ett certifikat som matchar kluster certifikatets deklaration måste vara etablerad till varje nod i de skalnings uppsättningar för virtuella datorer som utgör ditt kluster. Mer information finns i [hemligheter på Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

Det är viktigt att installera både aktuella och mål kluster certifikat på de virtuella datorerna för varje nodtyp i klustret innan du gör ändringar i klustrets certifikat deklarationer. Resan från utfärdande av certifikat till en Service Fabric-nod diskuteras i djupet vid [transporten av ett certifikat](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Ta klustret till ett optimalt start tillstånd

Konvertera en certifikat deklaration från tumavtryck till CN-baserade påverkan:

- Hur varje nod i klustret hittar och visar sina autentiseringsuppgifter för andra noder.
- Hur varje nod validerar autentiseringsuppgifterna för dess motsvarighet vid etablering av en säker anslutning.

Granska [presentations-och validerings reglerna för båda konfigurationerna](cluster-security-certificates.md#certificate-configuration-rules) innan du fortsätter. Den viktigaste beräkningen när du utför en tumavtryck-till-CN-konvertering är att uppgraderade och icke-uppgraderade noder (det vill säga noder som tillhör olika uppgraderings domäner) kan utföra en lyckad ömsesidig autentisering när som helst under uppgraderingen. Det rekommenderade sättet att åstadkomma detta är att deklarera mål-eller mål certifikatet genom tumavtrycket i en inledande uppgradering. Slutför sedan över gången till CN i ett senare namn. Om klustret redan är i ett rekommenderat start läge kan du hoppa över det här avsnittet.

Det finns flera giltiga start tillstånd för en omvandling. Invarianten är att klustret redan använder mål certifikatet (deklarerat av tumavtryck) i början av uppgraderingen till CN. Vi betraktar `GoalCert` , `OldCert1` och `OldCert2` i den här artikeln.

#### <a name="valid-starting-states"></a>Giltiga start tillstånd

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, där `GoalCert` har ett senare `NotBefore` datum än det för `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, där `GoalCert` har ett senare `NotBefore` datum än det för `OldCert1`

> [!NOTE]
> Före version 7.2.445 (7,2 CU4) har Service Fabric valt det certifikat som är längst ut (certifikatet med den längst bort egenskapen NotAfter), så att de tidigare start tillstånden före 7,2 CU4 kräver GoalCert måste ha ett senare `NotAfter` datum än `OldCert1`

Om klustret inte är i ett av de giltiga tillstånd som beskrivs ovan, se information om att uppnå tillståndet i avsnittet i slutet av den här artikeln.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Välj önskat CN-baserade certifikat verifierings schema

Som tidigare beskrivits har Service Fabric stöd för att deklarera certifikat med CN med ett implicit förtroende ankare eller genom att explicit fästa utfärdarens tumavtrycken. Mer information finns i [vanliga deklarationer för certifikat validering i vanliga namn](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Se till att du har en god förståelse för skillnaderna och konsekvenserna av att välja någon av mekanismerna. Den här skillnaden eller valet bestäms av `certificateIssuerThumbprintList` parameterns värde. Tom innebär att förlita sig på en betrodd rot certifikat utfärdare (förtroende ankare), medan en uppsättning tumavtrycken begränsar de tillåtna direkta utfärdarna för kluster certifikat.

   > [!NOTE]
   > I `certificateIssuerThumbprint` fältet kan du ange förväntade direkta utfärdare av certifikat som deklarerats av ämnet CN. Godkända värden är ett eller flera kommaavgränsade SHA1-tumavtrycken. Den här åtgärden förstärker certifikat verifieringen.
   >
   > Om inga utfärdare har angetts eller om listan är tom, godkänns certifikatet för autentisering om dess kedja kan skapas. Certifikatet slutar sedan med en rot som är betrodd av verifieraren. Om en eller flera Issuer-tumavtrycken anges godkänns certifikatet om tumavtrycket för dess direkta utfärdare, som extraheras från kedjan, matchar något av de värden som anges i det här fältet. Certifikatet kommer att godkännas oavsett om roten är betrodd eller inte.
   >
   > En PKI kan använda olika certifikat utfärdare (kallas även *utfärdare*) för att signera certifikat med ett visst ämne. Därför är det viktigt att ange alla förväntade Issuer-tumavtrycken för det ämnet. Med andra ord garanterar inte förnyelsen av ett certifikat att det är signerat av samma utfärdare som det certifikat som förnyas.
   >
   > Att ange utfärdaren anses vara en god praxis. Att utelämna utfärdaren fortsätter att fungera för certifikat som kopplas upp till en betrodd rot, men det här beteendet har begränsningar och kan gå ut i steg om i en snar framtid. Kluster som distribueras i Azure, som skyddas med X509-certifikat som utfärdats av en privat PKI och som deklarerats av ett ämne, kanske inte kan verifieras av Service Fabric (för kluster-till-tjänst-kommunikation). Verifieringen kräver att PKI: s certifikat princip kan identifieras, vara tillgänglig och tillgänglig.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Uppdatera klustrets Azure Resource Manager mall och distribuera

Hantera dina Service Fabric kluster med Azure Resource Manager ARM-mallar (ARM). Ett alternativ, som också använder JSON-artefakter, är [Azure Resource Explorer (för hands version)](https://resources.azure.com). En likvärdig upplevelse är inte tillgänglig i Azure Portal för tillfället.

Om den ursprungliga mallen som motsvarar ett befintligt kluster inte är tillgänglig, kan en motsvarande mall hämtas i Azure Portal. Gå till resurs gruppen som innehåller klustret och välj **Exportera mall** från **Automation** -menyn till vänster. Välj sedan de resurser som du vill använda. Som minst bör den virtuella datorns skalnings uppsättning och kluster resurser exporteras. Den genererade mallen kan också hämtas. Den här mallen kan kräva ändringar innan den kan distribueras fullständigt. Mallen kanske inte överensstämmer med den ursprungliga en exakt. Det är en reflektion av kluster resursens aktuella tillstånd.

De nödvändiga ändringarna är följande:

- Uppdaterar definitionen av Service Fabric Node-tillägget (under den virtuella dator resursen). Om klustret definierar flera nodtyper måste du uppdatera definitionen för varje motsvarande virtuell dators skalnings uppsättning.
- Uppdaterar kluster resurs definitionen.

Detaljerade exempel finns här.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Uppdatera de virtuella datorernas skalnings uppsättnings resurser
Från:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Till:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Uppdatera kluster resursen

I resursen **Microsoft. ServiceFabric/Clusters** lägger du till en **certificateCommonNames** -egenskap med en **commonNames** -inställning och tar bort **certifikat** egenskapen helt (alla dess inställningar).

Från:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Till:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Mer information finns i [distribuera ett Service Fabric kluster som använder certifikatets egna namn i stället för tumavtryck](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen

Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Få ett giltigt start tillstånd för konvertering av ett kluster till CN-baserade certifikat deklarationer

| Start tillstånd | Uppgradering 1 | Uppgradering 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` och `GoalCert` har ett senare `NotBefore` datum än `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` och `OldCert1` har ett senare `NotBefore` datum än `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, där `OldCert1` har ett senare `NotBefore` datum än `GoalCert` | Uppgradera till `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, där `OldCert1` har ett senare `NotBefore` datum än `GoalCert` | Uppgradera till `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Ta bort en av `OldCert1` eller `OldCert2` för att komma till tillstånd `Thumbprint: OldCertx, ThumbprintSecondary: None` | Fortsätt från det nya start läget |

> [!NOTE]
> För ett kluster i en version före version 7.2.445 (7,2 CU4) ersätter `NotBefore` `NotAfter` du med i ovanstående tillstånd.

Instruktioner för hur du utför dessa uppgraderingar finns i [Hantera certifikat i ett Azure Service Fabric-kluster](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [rullar över ett kluster certifikat efter eget namn](service-fabric-cluster-rollover-cert-cn.md).
* Lär dig hur du [konfigurerar ett kluster för touch-autoförnyelse](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
