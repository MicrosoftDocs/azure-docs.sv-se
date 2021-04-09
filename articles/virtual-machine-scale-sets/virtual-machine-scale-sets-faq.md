---
title: Vanliga frågor och svar om Virtual Machine Scale Sets
description: Få svar på vanliga frågor om skalnings uppsättningar för virtuella datorer i Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 2decbf6f65e6d5112f62bff3a00fe1187730e98c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933482"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Vanliga frågor och svar om Virtual Machine Scale Sets

Få svar på vanliga frågor om skalnings uppsättningar för virtuella datorer i Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Vanliga Vanliga frågor och svar om skalnings uppsättningar

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hur många virtuella datorer kan man ha i en skalningsuppsättning?

En skalnings uppsättning kan ha 0 till 1 000 virtuella datorer baserade på plattforms avbildningar, eller 0 till 600 virtuella datorer baserade på anpassade avbildningar.

### <a name="are-data-disks-supported-within-scale-sets"></a>Kan datadiskar användas i skalningsuppsättningar?

Ja. En skalningsuppsättning kan definiera en konfiguration för anslutna datadiskar som gäller för alla virtuella datorer i uppsättningen. Mer information finns i [Azure scale sets and attached data disks (Azure-skalningsuppsättningar och anslutna datadiskar)](virtual-machine-scale-sets-attached-disks.md). Andra alternativ för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temporär enhet (lokal, backas inte upp av Azure Storage)
* Azure-datatjänst (t.ex. Azure-tabeller, Azure-blobbar)
* Extern datatjänst (t.ex. fjärrdatabas)

### <a name="which-azure-regions-support-scale-sets"></a>Vilka Azure-regioner har stöd för skalningsuppsättningar?

Alla regioner stöder skalningsuppsättningar.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hur skapar jag en skalningsuppsättning med en anpassad avbildning?

Skapa och avbilda en VM-avbildning och Använd den som källa för din skalnings uppsättning. En själv studie kurs om hur du skapar och använder en anpassad VM-avbildning kan du använda [Azure CLI](tutorial-use-custom-image-cli.md) eller [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Vilka virtuella datorer tas bort om jag minskar skalningsuppsättningens kapacitet från 20 till 15?

Som standard tas virtuella datorer bort från skalnings uppsättningen jämnt över tillgänglighets zoner (om skalnings uppsättningen distribueras i zonindelade-konfigurationen) och fel domäner för att maximera tillgängligheten. Virtuella datorer med högst ID tas bort först.

Du kan ändra ordningen för borttagning av virtuella datorer genom att ange en [skalnings princip](virtual-machine-scale-sets-scale-in-policy.md) för skalnings uppsättningen.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens ID från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer är balanserade över fel domäner.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan jag framtvinga en körning av sekvensen när jag använder flera tillägg i en skalningsuppsättning?

Ja, du kan använda [tillägg](virtual-machine-scale-sets-extension-sequencing.md)för skalnings uppsättning.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Fungerar skalningsuppsättningar med Azures tillgänglighetsuppsättningar?

En regional (icke-zonindelade) skalnings uppsättning använder *placerings grupper* som fungerar som en implicit tillgänglighets uppsättning med fem fel domäner och fem uppdaterings domäner. Skalnings uppsättningar på fler än 100 virtuella datorer sträcker sig över flera placerings grupper. Mer information om placeringsgrupper finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma virtuella nätverk som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer (som ofta kräver unika konfigurationer) i en tillgänglighetsuppsättning och placera datanoder i skalningsuppsättningen.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Fungerar skalnings uppsättningar med Azures tillgänglighets zoner?

Ja! Mer information finns i avsnittet om [skalnings uppsättnings zon](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatisk skalning

### <a name="what-are-best-practices-for-azure-autoscale"></a>Vad är bästa praxis för Azure autoskalning?

För bästa praxis för automatisk skalning, se [metod tips för automatisk skalning av virtuella datorer](../azure-monitor/autoscale/autoscale-best-practices.md).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Var hittar jag mått namn för automatisk skalning som använder värdbaserade mått?

För mått namn för automatisk skalning som använder värdbaserade mått, se [mått som stöds med Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Finns det några exempel på autoskalning baserat på ett Azure Service Bus ämne och Kölängd?

Ja. Exempel på automatisk skalning baserat på ett Azure Service Bus ämne och Kölängd finns i [Azure Monitor autoskalning av vanliga mått](../azure-monitor/autoscale/autoscale-common-metrics.md).

För en Service Bus kö använder du följande JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

För en lagrings kö använder du följande JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Ersätt exempel värden med dina resurs-URI: er (Uniform Resource Identifier).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Ska jag Autoskala med hjälp av värdbaserade mått eller ett diagnostik-tillägg?

Du kan skapa en inställning för autoskalning på en virtuell dator för att använda mått på värdnivå eller gästbaserade mått.

En lista över mått som stöds finns i [Azure Monitor autoskalning av vanliga mått](../azure-monitor/autoscale/autoscale-common-metrics.md).

Ett fullständigt exempel för skalnings uppsättningar för virtuella datorer finns i [Avancerad konfiguration av automatisk skalning med hjälp av Resource Manager-mallar för skalnings uppsättningar för virtuella datorer](../azure-monitor/autoscale/autoscale-virtual-machine-scale-sets.md).

Exemplet använder CPU-mått på värdnivå och måttet för antalet meddelanden.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hur gör jag för att ange varnings regler för en skalnings uppsättning för virtuella datorer?

Du kan skapa aviseringar för mått för skalnings uppsättningar för virtuella datorer via PowerShell eller Azure CLI. Mer information finns i [Azure Monitor PowerShell snabb starts exempel](../azure-monitor/powershell-samples.md#create-metric-alerts) och [Azure Monitor cross-platform CLI snabb starts exempel](../azure-monitor/cli-samples.md#work-with-alerts).

TargetResourceId för den virtuella datorns skalnings uppsättning ser ut så här:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Du kan välja en prestanda räknare för virtuella datorer som måttet för att ange en avisering för. Mer information finns i [gäst operativ systemets mått för Resource Manager-baserade virtuella Windows-datorer](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) och [gäst operativ system mått för virtuella Linux-datorer](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-linux-vms) i artikeln [Azure Monitor autoskalning av vanliga mått](../azure-monitor/autoscale/autoscale-common-metrics.md) .

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hur gör jag för att konfigurera autoskalning på en skalnings uppsättning för virtuella datorer med hjälp av PowerShell?

Om du vill konfigurera automatisk skalning på en skalnings uppsättning för virtuella datorer med hjälp av PowerShell läser du skala [automatiskt en skalnings uppsättning för virtuella datorer](tutorial-autoscale-powershell.md). Du kan också konfigurera autoskalning med [Azure CLI](tutorial-autoscale-cli.md) och [Azure-mallar](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Om jag har stoppat (Frigjord) en virtuell dator, är den virtuella datorn som en del av en åtgärd för autoskalning?

Nej. Om regler för autoskalning kräver ytterligare VM-instanser som en del i en skalnings uppsättning, skapas en ny VM-instans. Virtuella dator instanser som är stoppade (friallokerade) har inte startats som en del av en autoskalning-händelse. De stoppade (fribelagda) virtuella datorerna kan dock tas bort som en del av en autoskalning-händelse som skalas i antalet instanser, på samma sätt som alla VM-instanser kan tas bort baserat på ordningen för VM-instansens ID.



## <a name="certificates"></a>Certifikat

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hur gör jag för att du säkert leverera ett certifikat till den virtuella datorn?

För att på ett säkert sätt skicka ett certifikat till den virtuella datorn kan du installera ett kund certifikat direkt i ett Windows-certifikat arkiv från kundens nyckel valv.

Använd följande JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Koden stöder Windows och Linux.

Mer information finns i [skapa eller uppdatera en skalnings uppsättning för virtuella datorer](/rest/api/compute/virtualmachinescalesets/createorupdate).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hur gör jag för att använda självsignerade certifikat för Azure Service Fabric-kluster?
I det senaste exemplet använder du följande Azure CLI-instruktion i Azure-gränssnittet: läsa Service Fabric CLI module exempel dokumentation, som skrivs ut till stdout:

```azurecli
az sf cluster create -h
```

Självsignerade certifikat kan inte användas för distribuerat förtroende från en certifikat utfärdare och ska inte användas för Service Fabric kluster som är värdar för företags produktions lösningar. Mer Service Fabric säkerhets rikt linjer finns i [metod tips för Azure Service Fabric säkerhet](../security/fundamentals/service-fabric-best-practices.md) och [Service Fabric kluster säkerhets scenarier](../service-fabric/service-fabric-cluster-security.md).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan jag ange ett SSH-nyckelpar som ska användas för SSH-autentisering med en skalnings uppsättning för virtuella Linux-datorer från en Resource Manager-mall?

Ja. REST API för **osProfile** liknar standard REST API för virtuella datorer.

Inkludera **osProfile** i mallen:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Detta JSON-block används i [den här snabb starts mal len för Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Mer information finns i [skapa eller uppdatera en skalnings uppsättning för virtuella datorer](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Hur gör jag för att ta bort föråldrade certifikat?

Ta bort inaktuella certifikat genom att ta bort det gamla certifikatet från listan med valv certifikat. Lämna alla certifikat som du vill ska finnas kvar på datorn i listan. Detta tar inte bort certifikatet från alla dina virtuella datorer. Det lägger inte heller till certifikatet till nya virtuella datorer som skapas i skalnings uppsättningen för den virtuella datorn.

Ta bort certifikatet från befintliga virtuella datorer genom att använda ett anpassat skript tillägg för att manuellt ta bort certifikaten från certifikat arkivet.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hur gör jag för att du mata in en befintlig Offentlig SSH-nyckel i SSH-skiktet för skalnings uppsättningen för virtuella datorer under etableringen?

Om du bara tillhandahåller de virtuella datorerna med en offentlig SSH-nyckel behöver du inte ange de offentliga nycklarna i Key Vault. Offentliga nycklar är inte hemliga.

Du kan tillhandahålla offentliga SSH-nycklar i oformaterad text när du skapar en virtuell Linux-dator:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration element namn | Krävs | Typ | Beskrivning
--- | --- | --- | ---
SSH | Inga | Samling | Anger SSH-nyckeln för ett Linux-operativsystem
path | Ja | Sträng | Anger sökvägen till Linux-filen där SSH-nycklar eller certifikatet ska placeras
Data | Ja | Sträng | Anger en Base64-kodad Offentlig SSH-nyckel

Ett exempel finns i [snabb starts mal len 101-VM-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>När jag kör `Update-AzVmss` efter att ha lagt till mer än ett certifikat från samma nyckel valv visas följande meddelande:

>Update-AzVmss: lista hemlighet innehåller upprepade instanser av/Subscriptions/ \<my-subscription-id> /resourceGroups/Internal-RG-dev/providers/Microsoft.KeyVault/Vaults/Internal-keyvault-dev, vilket inte är tillåtet.

Detta kan inträffa om du försöker lägga till samma valv i stället för att använda ett nytt valv certifikat för det befintliga käll valvet. `Add-AzVmssSecret`Kommandot fungerar inte korrekt om du lägger till ytterligare hemligheter.

Om du vill lägga till fler hemligheter från samma nyckel valv uppdaterar du listan $vmss. Properties. osProfile. hemligheter [0]. vaultCertificates.

En förväntad ingående struktur finns i [skapa eller uppdatera en virtuell dator uppsättning](/rest/api/compute/virtualmachinescalesets/createorupdate).

Hitta hemligheten i det objekt i skalnings uppsättningen för virtuella datorer som finns i nyckel valvet. Lägg sedan till certifikat referensen (URL: en och namnet på den hemliga butiken) i listan som är associerad med valvet.

> [!NOTE]
> För närvarande kan du inte ta bort certifikat från virtuella datorer med hjälp av API: et för skalnings uppsättningen för virtuella datorer.
>

Nya virtuella datorer kommer inte att ha det gamla certifikatet. Virtuella datorer som har certifikatet och som redan har distribuerats kommer dock att ha det gamla certifikatet.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan jag push-överföra certifikat till den virtuella datorns skalnings uppsättning utan att ange lösen ordet när certifikatet finns i den hemliga lagringen?

Du behöver inte hårdkoda lösen ord i skript. Du kan hämta lösen ord dynamiskt med de behörigheter som du använder för att köra distributions skriptet. Om du har ett skript som flyttar ett certifikat från nyckel valvet för den hemliga lagrings platsen, kommer det hemliga lager `get certificate` kommandot också att spara lösen ordet för. pfx-filen.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hur fungerar egenskapen hemligheter för virtualMachineProfile. osProfile för en skalnings uppsättning för virtuella datorer? Varför behöver jag sourceVault-värdet när jag måste ange den absoluta URI: n för ett certifikat med hjälp av egenskapen certificateUrl?

En referens för Windows Remote Management (WinRM)-certifikat måste finnas i egenskapen hemligheter i OS-profilen.

Syftet med att ange käll valvet är att genomdriva principer för åtkomst kontrol lista (ACL) som finns i en användares Azure Cloud Service-modell. Om käll valvet inte har angetts kan användare som inte har behörighet att distribuera eller komma åt hemligheter i ett nyckel valv kunna via en CRP (Compute Resource Provider). ACL: er finns även för resurser som inte finns.

Om du anger ett felaktigt käll valv-ID men en giltig URL för Key Vault rapporteras ett fel när du avsöker åtgärden.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Om jag lägger till hemligheter till en befintlig skalnings uppsättning för virtuella datorer, är hemligheterna inmatade i befintliga virtuella datorer eller bara i nya?

Certifikat läggs till i alla virtuella datorer, även för befintliga. Om den virtuella datorns skalnings uppsättning upgradePolicy-egenskap är inställd på **manuell**, läggs certifikatet till den virtuella datorn när du utför en manuell uppdatering på den virtuella datorn.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Var ska jag lagra certifikat för virtuella Linux-datorer?

Information om hur du distribuerar certifikat för virtuella Linux-datorer finns i [distribuera certifikat till virtuella datorer från ett kundhanterat nyckel valv](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hur gör jag för att lägga till ett nytt valv certifikat i ett nytt certifikat objekt?

Information om hur du lägger till ett valv certifikat till en befintlig hemlighet finns i följande PowerShell-exempel. Använd endast ett hemligt objekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Vad händer med certifikat om du återavbildar en virtuell dator?

Om du återavbildar en virtuell dator tas certifikaten bort. Avbildningen tas bort hela OS-disken.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Vad händer om du tar bort ett certifikat från nyckel valvet?

Om hemligheten tas bort från nyckel valvet och du sedan kör `stop deallocate` för alla virtuella datorer och sedan startar dem igen, uppstår ett problem. Felen beror på att CRP måste hämta hemligheterna från nyckel valvet, men det kan inte. I det här scenariot kan du ta bort certifikaten från den virtuella datorns skalnings uppsättnings modell.

CRP-komponenten sparar inte kund hemligheter. Om du kör `stop deallocate` för alla virtuella datorer i den virtuella datorns skalnings uppsättning tas cachen bort. I det här scenariot hämtas hemligheter från nyckel valvet.

Det här problemet uppstår inte vid utskalning eftersom det finns en cachelagrad kopia av hemligheten i Azure Service Fabric (i en klient modell med en enda infrastruktur).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Varför måste jag ange certifikat versionen när jag använder Key Vault?

Syftet med Key Vault kravet att ange certifikatets version är att göra den tydlig till användaren vilket certifikat som distribueras på deras virtuella datorer.

Om du skapar en virtuell dator och sedan uppdaterar din hemlighet i nyckel valvet, laddas inte det nya certifikatet ned till dina virtuella datorer. Men de virtuella datorerna verkar referera till den och nya virtuella datorer får den nya hemligheten. För att undvika detta måste du referera till en hemlig version.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mitt team fungerar med flera certifikat som distribueras till som. cer-offentliga nycklar. Vad är den rekommenderade metoden för att distribuera dessa certifikat till en skalnings uppsättning för virtuella datorer?

Om du vill distribuera offentliga. cer-offentliga nycklar till en skalnings uppsättning för virtuella datorer kan du generera en. pfx-fil som bara innehåller CER-filer. Använd för att göra detta `X509ContentType = Pfx` . Läs till exempel in. CER-filen som ett x509Certificate2-objekt i C# eller PowerShell och anropa sedan metoden.

Mer information finns i [X509Certificate. export-metoden (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Hur gör jag för att skicka in certifikat som base64-strängar?

För att emulera överföring i ett certifikat som en Base64-sträng kan du extrahera den senaste versions-URL: en i en Resource Manager-mall. Ta med följande JSON-egenskap i Resource Manager-mallen:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Måste jag figursätta certifikat i JSON-objekt i nyckel valv?

I virtuella datorers skalnings uppsättningar och virtuella datorer måste certifikat omslutas i JSON-objekt.

Vi stöder även innehålls typen application/x-PKCS12.

Vi stöder för närvarande inte. CER-filer. Om du vill använda CER-filer exporterar du dem till. pfx-behållare.



## <a name="compliance-and-security"></a>Efterlevnad och säkerhet

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Är skalnings uppsättningar för virtuella datorer PCI-kompatibla?

VM-skalningsuppsättningar är ett tunt API-läger ovanpå CRP. Båda komponenterna är en del av beräkningsplattformen i Azure-tjänsteträdet.

Från ett kompatibilitetsperspektiv är VM-skalningsuppsättningar en grundläggande del av Azure Compute-plattformen. De delar ett team, verktyg, processer, distributionsmetodik, säkerhetskontroller, JIT-kompilering (just-in-time), övervakning, aviseringar och så vidare med själva CRP:n. VM-skalningsuppsättningar är PCI-kompatibla (Payment Card Industry) eftersom CRP:n är en del av den aktuella attesteringen för PCI Data Security Standard (DSS).

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Fungerar [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) med skalnings uppsättningar för virtuella datorer?

Ja. Du kan se några exempel på MSI-mallar i Azure snabb starts mallar för [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) och [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Tas bort

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Kommer de Lås jag ställer in på plats på instanser av skalnings uppsättning för virtuella datorer att respekteras när instanser tas bort?

I Azure-portalen har du möjlighet att ta bort en enskild instans eller Mass borttagning genom att välja flera instanser. Om du försöker ta bort en enskild instans som har ett lås på plats, respekteras låset och du kan inte ta bort instansen. Men om du väljer flera instanser och någon av dessa instanser har ett lås på plats, respekteras inte låset och alla markerade instanser tas bort.

I Azure CLI har du bara möjlighet att ta bort en enskild instans. Om du försöker ta bort en enskild instans som har ett lås på plats, respekteras låset och du kan inte ta bort den instansen.

## <a name="extensions"></a>Tillägg

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hur gör jag för att bort tillägget för skalnings uppsättning för virtuell dator?

Om du vill ta bort ett tillägg för skalnings uppsättning för virtuell dator använder du följande PowerShell-exempel:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Du hittar tillägg-värdet i `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Finns det ett exempel på en mall för skalnings uppsättning för virtuell dator som kan integreras med Azure Monitor loggar?

Ett exempel på en mall för skalnings uppsättning för virtuell dator som integreras med Azure Monitor loggar finns i det andra exemplet i [distribuera ett Azure Service Fabric-kluster och aktivera övervakning med hjälp av Azure Monitor loggar](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hur gör jag för att lägga till ett tillägg till alla virtuella datorer i skalnings uppsättningen för den virtuella datorn?

Om uppdaterings principen är inställd på **Automatisk** och distribuerar om mallen med de nya tilläggs egenskaperna uppdaterar alla virtuella datorer.

Om uppdaterings principen är inställd på **manuell** måste du först uppdatera tillägget och sedan manuellt uppdatera alla instanser i de virtuella datorerna.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Om tilläggen som är associerade med en befintlig virtuell dators skalnings uppsättning uppdateras, påverkas befintliga virtuella datorer?

Om tilläggs definitionen i den virtuella datorns skal uppsättnings modell uppdateras och upgradePolicy-egenskapen är inställd på **Automatisk**, uppdateras de virtuella datorerna. Om egenskapen upgradePolicy är inställd på **manuell** flaggas tillägg som inte matchar modellen.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Körs tilläggen igen när en befintlig dator är servad eller avbildningad?

Om en befintlig virtuell dator är servad visas den som en omstart och tilläggen körs inte igen. Om en virtuell dator har återställts på avbildningen, liknar processen samma som att ersätta operativ system enheten med käll avbildningen. Alla specialiseringar från den senaste modellen, till exempel tillägg, körs igen.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hur gör jag för att ansluta en skalnings uppsättning för virtuell dator till en Active Directory domän?

Om du vill ansluta en skalnings uppsättning för virtuella datorer till en Active Directory-domän (AD) kan du definiera ett tillägg.

Använd egenskapen JsonADDomainExtension för att definiera ett tillägg:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Tillägget för skalnings uppsättningen för den virtuella datorn försöker installera något som kräver en omstart.

Om tillägget för skalnings uppsättningen för virtuell dator försöker installera något som kräver en omstart, kan du använda tillägget Azure Automation Desired State Configuration (Automation DSC). Om operativ systemet är Windows Server 2012 R2, hämtar Azure i Windows Management Framework (WMF) 5,0-installationen, startar om och fortsätter sedan med konfigurationen.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hur gör jag för att aktiverar du program mot skadlig kod i skalnings uppsättningen för den virtuella datorn?

Använd följande PowerShell-exempel för att aktivera program mot skadlig kod på din skalnings uppsättning för virtuella datorer:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hur gör jag för att köra ett anpassat skript som finns i ett privat lagrings konto?

Om du vill köra ett anpassat skript som finns på ett privat lagrings konto konfigurerar du skyddade inställningar med lagrings kontots nyckel och namn. Mer information finns i [tillägg för anpassat skript](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity).

## <a name="passwords"></a>Lösenord

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hur gör jag för att återställa lösen ordet för virtuella datorer i skalnings uppsättningen för virtuella datorer?

Det finns två huvudsakliga sätt att ändra lösen ordet för virtuella datorer i skalnings uppsättningar.

- Ändra modellen för skalnings uppsättningen för den virtuella datorn direkt. Tillgängligt med API 2017-12-01 och senare.

    Uppdatera admin-autentiseringsuppgifterna direkt i skalnings uppsättnings modellen (till exempel med hjälp av Azure Resource Explorer, PowerShell eller CLI). När skalnings uppsättningen har uppdaterats har alla nya virtuella datorer de nya autentiseringsuppgifterna. Befintliga virtuella datorer har bara nya autentiseringsuppgifter om de är avbildade.

- Återställ lösen ordet med hjälp av tillägg för VM-åtkomst. Se till att följa lösen ords kraven som beskrivs [här](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Använd följande PowerShell-exempel:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Nätverk

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Är det möjligt att tilldela en nätverks säkerhets grupp (NSG) en skalnings uppsättning så att den gäller för alla virtuella dator nätverkskort i uppsättningen?

Ja. En nätverks säkerhets grupp kan tillämpas direkt på en skalnings uppsättning genom att referera till den i avsnittet networkInterfaceConfigurations i nätverks profilen. Exempel:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hur gör jag för att gör en VIP-växling för skalnings uppsättningar för virtuella datorer i samma prenumeration och samma region?

Om du har två skalnings uppsättningar för virtuella datorer med Azure Load Balancer klient delar och de är i samma prenumeration och region kan du frigöra de offentliga IP-adresserna från var och en, och tilldela den andra. Se [VIP-växling: blå-grön distribution i Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) till exempel. Detta innebär en fördröjning trots att resurserna frigörs/allokeras på nätverks nivå. Ett snabbare alternativ är att använda Azure Application Gateway med två backend-pooler och en regel för routning. Alternativt kan du vara värd för ditt program med [Azure App tjänst](https://azure.microsoft.com/services/app-service/) som ger stöd för snabb växling mellan mellanlagrings-och produktions platser.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hur gör jag för att ange ett intervall med privata IP-adresser som ska användas för statisk privat IP-adressallokering?

IP-adresser väljs från ett undernät som du anger.

Tilldelnings metoden för IP-adresser för skalnings uppsättningar för virtuella datorer är alltid "dynamisk", men det betyder inte att dessa IP-adresser kan ändras. I det här fallet innebär "dynamisk" bara att du inte anger IP-adressen i en begäran. Ange den statiska uppsättningen med hjälp av under nätet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hur gör jag för att distribuerar du en skalnings uppsättning för en virtuell dator till ett befintligt virtuellt Azure-nätverk?

Information om hur du distribuerar en skalnings uppsättning för en virtuell dator till ett befintligt virtuellt Azure-nätverk finns i [distribuera en skalnings uppsättning för virtuella datorer till ett befintligt virtuellt nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan jag använda skalnings uppsättningar med accelererat nätverk?

Ja. Om du vill använda accelererat nätverk anger du enableAcceleratedNetworking till sant i skalnings uppsättningens networkInterfaceConfigurations-inställningar. Till exempel

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hur kan jag konfigurera DNS-servrarna som används av en skalnings uppsättning?

Om du vill skapa en skalnings uppsättning för virtuell dator med en anpassad DNS-konfiguration lägger du till ett dnsSettings JSON-paket i avsnittet skalnings uppsättning networkInterfaceConfigurations. Exempel:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hur kan jag konfigurera en skalnings uppsättning för att tilldela en offentlig IP-adress till varje virtuell dator?

Om du vill skapa en virtuell dators skalnings uppsättning som tilldelar en offentlig IP-adress till varje virtuell dator kontrollerar du att API-versionen av Microsoft. Compute/virtualMachineScaleSets-resursen är 2017-03-30 och lägger till ett _publicipaddressconfiguration_ JSON-paket i avsnittet för skalnings uppsättning ipConfigurations. Exempel:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan jag konfigurera en skalnings uppsättning så att den fungerar med flera programgatewayer?

Ja. Du kan lägga till resurs-ID: n för flera Application Gateway backend-adresspooler i listan _applicationGatewayBackendAddressPools_ i avsnittet _ipConfigurations_ i nätverks profilen för skalnings uppsättningen.

## <a name="scale"></a>Skala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>I vilket fall skulle jag skapa en skalnings uppsättning för virtuella datorer med färre än två virtuella datorer?

En orsak till att skapa en skalnings uppsättning för virtuella datorer med färre än två virtuella datorer är att använda de elastiska egenskaperna för en skalnings uppsättning för virtuella datorer. Du kan till exempel distribuera en skalnings uppsättning för virtuella datorer med noll virtuella datorer för att definiera infrastrukturen utan att betala kostnader för virtuella datorer som körs. När du är redo att distribuera virtuella datorer ökar du kapaciteten för den virtuella datorns skalnings uppsättning till antalet produktions instanser.

Ett annat skäl till att du kan skapa en skalnings uppsättning för virtuella datorer med färre än två virtuella datorer är om du är mindre med tillgänglighet än att använda en tillgänglighets uppsättning med diskreta virtuella datorer. Med skalnings uppsättningar för virtuella datorer kan du arbeta med undifferentiated Compute-enheter som är utbytbara. Denna enhetlighet är en nyckel differentiering för skalnings uppsättningar för virtuella datorer jämfört med tillgänglighets uppsättningar. Många tillstånds lösa arbets belastningar spårar inte enskilda enheter. Om arbets belastningen sjunker kan du skala ned till en beräknings enhet och sedan skala upp till många när arbets belastningen ökar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hur gör jag för att ändra antalet virtuella datorer i en skalnings uppsättning för virtuella datorer?

Om du vill ändra antalet virtuella datorer i en skalnings uppsättning för virtuella datorer i Azure Portal, i avsnittet Egenskaper för virtuell dator skalnings uppsättning, klickar du på bladet "skalning" och använder skjutreglaget.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hur gör jag för att definiera anpassade aviseringar för när vissa tröskelvärden uppnås?

Du har viss flexibilitet i hur du hanterar aviseringar för angivna tröskelvärden. Du kan till exempel definiera anpassade webhookar. Följande webhook-exempel är från en Resource Manager-mall:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Korrigeringar och åtgärder

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan jag skapa en skalnings uppsättning i en befintlig resurs grupp?

Ja, du kan skapa en skalnings uppsättning i en befintlig resurs grupp.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan jag flytta en skalnings uppsättning till en annan resurs grupp?

Ja, du kan flytta skalnings uppsättnings resurser till en ny prenumeration eller resurs grupp.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hur uppdaterar jag skalnings uppsättningen för virtuella datorer till en ny avbildning? Hur gör jag för att hanterar du korrigering?

Om du vill uppdatera skalnings uppsättningen för den virtuella datorn till en ny avbildning och hantera korrigering, se [uppgradera en skalnings uppsättning för virtuella datorer](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan jag använda åtgärden för avbildning för att återställa en virtuell dator utan att ändra avbildningen? (Det vill säga jag vill återställa en virtuell dator till fabriks inställningarna i stället för till en ny avbildning.)

Ja, du kan använda åtgärden för avbildning för att återställa en virtuell dator utan att ändra avbildningen. Men om den virtuella datorns skalnings uppsättning refererar till en plattforms avbildning med `version = latest` , kan den virtuella datorn uppdatera till en senare OS-avbildning när du anropar `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Är det möjligt att integrera skalnings uppsättningar med Azure Monitor loggar?

Ja, du kan installera tillägget Azure Monitor på de virtuella datorerna i skalnings uppsättningen. Här är ett Azure CLI-exempel:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Du hittar de nödvändiga workspaceId och workspaceKey på arbets ytan Log Analytics Azure Portal. På sidan Översikt klickar du på panelen Inställningar. Klicka på fliken anslutna källor överst.

> [!NOTE]
> Om din skalnings uppsättnings _upgradePolicy_ är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta _AZ VMSS Update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hur gör jag för att aktiverar du startdiagnostik?

Du aktiverar startdiagnostik genom att först skapa ett lagrings konto. Lägg sedan till det här JSON-blocket i den virtuella datorns skalnings uppsättning **virtualMachineProfile** och uppdatera skalnings uppsättningen för den virtuella datorn:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

När en ny virtuell dator skapas visar egenskapen InstanceView för den virtuella datorn information om skärm bilden och så vidare. Här är ett exempel:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hur gör jag för att hämta egenskaps information för varje virtuell dator utan att göra flera anrop? Hur får jag till exempel fel domänen för var och en av de 100 virtuella datorerna i skalnings uppsättningen för den virtuella datorn?

Om du vill hämta egenskaps information för varje virtuell dator utan att skapa flera anrop kan du anropa `ListVMInstanceViews` genom att göra en REST API `GET` på följande resurs-URI:

/Subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines? $expand = instanceView&$select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan jag skicka olika tilläggs argument till olika virtuella datorer i en skalnings uppsättning för virtuella datorer?

Nej, du kan inte skicka olika tilläggs argument till olika virtuella datorer i en skalnings uppsättning för virtuella datorer. Tillägg kan dock fungera baserat på de unika egenskaperna för den virtuella dator som de körs på, t. ex. på dator namnet. Tillägg kan också fråga efter instansen metadata på http://169.254.169.254 för att få mer information om den virtuella datorn.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Varför finns det luckor mellan den virtuella datorns skalnings uppsättnings dator namn och VM-ID? Till exempel: 0, 1, 3...

Det finns luckor mellan den virtuella datorns skalnings uppsättnings dator namn och VM-ID: t eftersom **egenskapen för** skalnings uppsättningen för den virtuella datorn har angetts till standardvärdet **True**. Om överetablering har angetts till **Sant** skapas fler virtuella datorer än vad som begärs. Ytterligare virtuella datorer tas sedan bort. I det här fallet får du ökad distributions tillförlitlighet, men vid kostnad av sammanhängande namn och sammanhängande regler för NAT (Network Address Translation).

Du kan ställa in den här egenskapen på **false**. För små skalnings uppsättningar för virtuella datorer påverkar detta inte distributionens tillförlitlighet.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Vad är skillnaden mellan att ta bort en virtuell dator i en skalnings uppsättning för virtuella datorer och att ta bort den virtuella datorn? När ska jag välja ett över det andra?

Den största skillnaden mellan att ta bort en virtuell dator i en skalnings uppsättning för virtuella datorer och att ta bort den virtuella datorn är att `deallocate` inte ta bort de virtuella hård diskarna (VHD). Det finns lagrings kostnader som är kopplade till att köra `stop deallocate` . Du kan använda någon av följande orsaker:

- Du vill sluta betala beräknings kostnaderna, men du vill behålla disk statusen för de virtuella datorerna.
- Du vill starta en uppsättning virtuella datorer snabbare än du kan skala ut en skalnings uppsättning för virtuella datorer.
  - I samband med det här scenariot kanske du har skapat en egen motor för autoskalning och vill ha en snabbare skala från slut punkt till slut punkt.
- Du har en skalnings uppsättning för virtuella datorer som är ojämnt distribuerad mellan fel domäner eller uppdaterings domäner. Detta kan bero på att du har tagit bort virtuella datorer som du har tagit bort eller att virtuella datorer har tagits bort efter överetablering. Körs `stop deallocate` följt av `start` på den virtuella datorns skalnings uppsättning och distribuerar de virtuella datorerna mellan fel domäner eller uppdaterings domäner.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hur gör jag för att ta en ögonblicks bild av en instans av en skalnings uppsättning för virtuella datorer?
Skapa en ögonblicks bild från en instans av en skalnings uppsättning för virtuella datorer.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Skapa en hanterad disk från ögonblicks bilden.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
