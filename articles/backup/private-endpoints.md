---
title: Privata slutpunkter
description: Förstå processen med att skapa privata slut punkter för Azure Backup och scenarier där privata slut punkter används för att upprätthålla säkerheten för dina resurser.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054880"
---
# <a name="private-endpoints-for-azure-backup"></a>Privata slut punkter för Azure Backup

Med Azure Backup kan du säkerhetskopiera och återställa data på ett säkert sätt från dina Recovery Services-valv med [privata slut punkter](../private-link/private-endpoint-overview.md). Privata slut punkter använder en eller flera privata IP-adresser från ditt VNet, vilket effektivt ansluter tjänsten till ditt VNet.

Den här artikeln hjälper dig att förstå processen med att skapa privata slut punkter för Azure Backup och scenarier där privata slut punkter används för att upprätthålla säkerheten för dina resurser.

## <a name="before-you-start"></a>Innan du börjar

- Privata slut punkter kan bara skapas för nya Recovery Services-valv (som inte har några registrerade objekt i valvet). Därför måste privata slut punkter skapas innan du försöker skydda några objekt i valvet.
- Ett virtuellt nätverk kan innehålla privata slut punkter för flera Recovery Services-valv. Ett Recovery Services valv kan också ha privata slut punkter för det i flera virtuella nätverk. Det maximala antalet privata slut punkter som kan skapas för ett valv är dock 12.
- När en privat slut punkt har skapats för ett valv kommer valvet att låsas upp. Den är inte tillgänglig (för säkerhets kopiering och återställning) från nätverk som skiljer sig från dem som innehåller en privat slut punkt för valvet. Om alla privata slut punkter för valvet tas bort, kommer valvet att vara tillgängligt från alla nätverk.
- En privat slut punkts anslutning för säkerhets kopiering använder totalt 11 privata IP-adresser i ditt undernät, inklusive de som används av Azure Backup för lagring. Det här antalet kan vara högre (upp till 25) för vissa Azure-regioner. Vi föreslår att du har tillräckligt med privata IP-adresser tillgängliga när du försöker skapa privata slut punkter för säkerhets kopiering.
- Även om ett Recovery Services valv används av (både) Azure Backup och Azure Site Recovery, diskuterar den här artikeln användning av privata slut punkter för enbart Azure Backup.
- Azure Active Directory stöder för närvarande inte privata slut punkter. IP-adresser och FQDN: er som krävs för att Azure Active Directory ska fungera i en region måste vara tillåtna utgående åtkomst från det skyddade nätverket när du säkerhetskopierar databaser i virtuella Azure-datorer och säkerhets kopiering med MARS-agenten. Du kan också använda NSG-Taggar och Azure Firewall-taggar för att tillåta åtkomst till Azure AD, efter vad som är tillämpligt.
- Virtuella nätverk med nätverks principer stöds inte för privata slut punkter. Du måste inaktivera nätverks principer innan du fortsätter.
- Du måste registrera om Recovery Services Resource Provider med prenumerationen om du registrerade den innan maj 1 2020. Om du vill registrera providern igen går du till prenumerationen i Azure Portal, navigerar till **resurs leverantören** i det vänstra navigerings fältet och väljer sedan **Microsoft. RecoveryServices** och väljer **Omregistrera**.
- [Återställning mellan regioner](backup-create-rs-vault.md#set-cross-region-restore) för SQL och SAP HANA databas säkerhets kopieringar stöds inte om valvet har aktiverade privata slut punkter.

## <a name="recommended-and-supported-scenarios"></a>Rekommenderade och stödda scenarier

När privata slut punkter är aktiverade för valvet används de för säkerhets kopiering och återställning av SQL och SAP HANA arbets belastningar i en Azure VM-och MARS agent-säkerhetskopiering. Du kan också använda valvet för säkerhets kopiering av andra arbets belastningar även (de kräver inte privata slut punkter). Förutom säkerhets kopiering av SQL och SAP HANA arbets belastningar och säkerhets kopiering med MARS-agenten används även privata slut punkter för att utföra fil återställning för Azure VM-säkerhetskopiering. Mer information finns i följande tabell:

| Säkerhets kopiering av arbets belastningar i Azure VM (SQL, SAP HANA), säkerhets kopiering med MARS-agenten | Användning av privata slut punkter rekommenderas för att tillåta säkerhets kopiering och återställning utan att behöva tillåten IP-adresser/FQDN för Azure Backup eller Azure Storage från dina virtuella nätverk. I det scenariot ser du till att virtuella datorer som är värdar för SQL-databaser kan komma åt Azure AD IP-adresser eller FQDN. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **VIRTUELL Azure-säkerhetskopiering**                                         | Säkerhets kopiering av virtuella datorer kräver inte att du tillåter åtkomst till några IP-adresser eller FQDN. Det kräver därför inte privata slut punkter för säkerhets kopiering och återställning av diskar.  <br><br>   Fil återställning från ett valv som innehåller privata slut punkter skulle dock begränsas till virtuella nätverk som innehåller en privat slut punkt för valvet. <br><br>    När du använder ACL'ed ohanterade diskar bör du se till att lagrings kontot som innehåller diskarna ger till gång till **betrodda Microsoft-tjänster** om det är ACL'ed. |
| **Azure Files säkerhets kopiering**                                      | Azure Files säkerhets kopior lagras i det lokala lagrings kontot. Det kräver därför inte privata slut punkter för säkerhets kopiering och återställning. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Skapa och använda privata slut punkter för säkerhets kopiering

Det här avsnittet beskriver de steg som ingår i att skapa och använda privata slut punkter för Azure Backup i dina virtuella nätverk.

>[!IMPORTANT]
> Vi rekommenderar starkt att du följer stegen i samma ordning som anges i det här dokumentet. Om du inte gör det kan det leda till att valvet återges som inkompatibelt för att använda privata slut punkter och kräver att du startar om processen med ett nytt valv.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

I [det här avsnittet](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) finns information om hur du skapar ett valv med hjälp av Azure Resource Manager-klienten. Detta skapar ett valv med dess hanterade identitet redan aktive rad. Läs mer om Recovery Services-valv [här](./backup-azure-recovery-services-vault-overview.md).

## <a name="enable-managed-identity-for-your-vault"></a>Aktivera hanterad identitet för ditt valv

Hanterade identiteter tillåter valvet att skapa och använda privata slut punkter. Det här avsnittet handlar om hur du aktiverar den hanterade identiteten för ditt valv.

1. Gå till ditt Recovery Services valv – > **identitet**.

    ![Ändra identitetens status till på](./media/private-endpoints/identity-status-on.png)

1. Ändra **statusen** till **på** och välj **Spara**.

1. Ett **objekt-ID** genereras, vilket är valvets hanterade identitet.

    >[!NOTE]
    >Den hanterade identiteten får **inte** inaktive ras (även tillfälligt). Inaktive ring av den hanterade identiteten kan leda till inkonsekvent beteende.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Ge valvet behörighet att skapa nödvändiga privata slut punkter

För att skapa nödvändiga privata slut punkter för Azure Backup måste valvet (den hanterade identiteten för valvet) ha behörighet till följande resurs grupper:

- Resurs gruppen som innehåller det virtuella mål nätverket
- Resurs gruppen där de privata slut punkterna ska skapas
- Resurs gruppen som innehåller Privat DNS zoner, som beskrivs i detalj [här](#creating-private-endpoints-for-backup)

Vi rekommenderar att du ger **deltagar** rollen för dessa tre resurs grupper till valvet (hanterad identitet). Följande steg beskriver hur du gör detta för en viss resurs grupp (detta måste göras för var och en av de tre resurs grupperna):

1. Gå till resurs gruppen och navigera till **Access Control (IAM)** i det vänstra fältet.
1. När **Access Control** går du till **Lägg till en roll tilldelning**.

    ![Lägg till en rolltilldelning](./media/private-endpoints/add-role-assignment.png)

1. I fönstret **Lägg till roll tilldelning** väljer du **deltagare** som **roll** och använder **namnet** på valvet som **huvud konto**. Välj valvet och välj **Spara** när du är färdig.

    ![Välj roll och huvud konto](./media/private-endpoints/choose-role-and-principal.png)

Information om hur du hanterar behörigheter på en mer detaljerad nivå finns i [skapa roller och behörigheter manuellt](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Skapa och godkänna privata slut punkter för Azure Backup

### <a name="creating-private-endpoints-for-backup"></a>Skapar privata slut punkter för säkerhets kopiering

I det här avsnittet beskrivs processen för att skapa en privat slut punkt för ditt valv.

1. I Sök fältet söker du efter och väljer **privat länk**. Det tar dig till det **privata länk centret**.

    ![Sök efter privat länk](./media/private-endpoints/search-for-private-link.png)

1. I det vänstra navigerings fältet väljer du **privata slut punkter**. I fönstret **privata slut punkter** väljer du **+ Lägg** till för att börja skapa en privat slut punkt för valvet.

    ![Lägg till privat slut punkt i privat länk Center](./media/private-endpoints/add-private-endpoint.png)

1. En gång i processen för att **skapa en privat slut punkt** måste du ange information om hur du skapar din privata slut punkts anslutning.

    1. **Grundläggande** information: Fyll i den grundläggande informationen för dina privata slut punkter. Regionen ska vara samma som valvet och resursen.

        ![Fyll i grundläggande information](./media/private-endpoints/basic-details.png)

    1. **Resurs**: den här fliken kräver att du nämner den PaaS-resurs som du vill skapa anslutningen för. Välj **Microsoft. RecoveryServices/valv** från resurs typen för den önskade prenumerationen. När du är färdig väljer du namnet på Recovery Services valvet som **resurs** -och **AzureBackup** som **mål under resurs**.

        ![Fyll i fliken resurs](./media/private-endpoints/resource-tab.png)

    1. **Konfiguration**: i konfiguration anger du det virtuella nätverk och undernät där du vill att den privata slut punkten ska skapas. Detta är det VNet där den virtuella datorn finns. Du kan välja att **integrera din privata slut punkt** med en privat DNS-zon. Alternativt kan du också använda din anpassade DNS-server eller skapa en privat DNS-zon.

        ![Fyll i fliken konfiguration](./media/private-endpoints/configuration-tab.png)

        Se [det här avsnittet](#dns-changes-for-custom-dns-servers) om du vill använda dina anpassade DNS-servrar i stället för att integrera med Azure privat DNS zoner.  

    1. Du kan också lägga till **taggar** för din privata slut punkt.

    1. Fortsätt att **Granska + skapa** när du har skrivit in information. När verifieringen är klar väljer du **skapa** för att skapa den privata slut punkten.

## <a name="approving-private-endpoints"></a>Godkänner privata slut punkter

Om användaren som skapar den privata slut punkten också är ägare av Recovery Services-valvet, godkänns den privata slut punkten som skapades ovan automatiskt. Annars måste ägaren av valvet godkänna den privata slut punkten innan du kan använda den. I det här avsnittet beskrivs manuella godkännanden av privata slut punkter via Azure Portal.

Se [manuellt godkännande av privata slut punkter med hjälp av Azure Resource Manager-klienten](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) för att använda Azure Resource Manager-klienten för att godkänna privata slut punkter.

1. I Recovery Services-valvet navigerar du till **anslutningar för privata slut punkter** i det vänstra fältet.
1. Välj den privata slut punkts anslutning som du vill godkänna.
1. Välj **Godkänn** i det översta fältet. Du kan också välja **avvisa** eller **ta bort** om du vill avvisa eller ta bort slut punkts anslutningen.

    ![Godkänn privata slut punkter](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Använda privata slut punkter för säkerhets kopiering

När de privata slut punkterna som har skapats för valvet i ditt VNet har godkänts, kan du börja använda dem för att utföra säkerhets kopiering och återställning.

>[!IMPORTANT]
>Se till att du har slutfört alla steg som anges ovan i dokumentet innan du fortsätter. För att Sammanfattning måste du ha slutfört stegen i följande check lista:
>
>1. Skapade ett (nytt) Recovery Services valv
>1. Har aktiverat valvet för att använda systemtilldelad hanterad identitet
>1. Tilldelade relevanta behörigheter till valvets hanterade identitet
>1. En privat slut punkt har skapats för valvet
>1. Godkände den privata slut punkten (om inte automatiskt godkänd)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Säkerhets kopiering och återställning av arbets belastningar i virtuell Azure-dator (SQL, SAP HANA)

När den privata slut punkten har skapats och godkänts krävs inga ytterligare ändringar från klient sidan för att använda den privata slut punkten. All kommunikation och data överföring från det skyddade nätverket till valvet utförs via den privata slut punkten.
Men om du tar bort privata slut punkter för valvet efter att en server (SQL/SAP HANA) har registrerats på den, måste du registrera om behållaren med valvet. Du behöver inte sluta skydda dem.

### <a name="backup-and-restore-through-mars-agent"></a>Säkerhetskopiera och Återställ via MARS-agenten

När du använder MARS-agenten för att säkerhetskopiera lokala resurser, se till att ditt lokala nätverk (som innehåller dina resurser som ska säkerhets kopie RAS) är peer-kopplat med det virtuella Azure-nätverket som innehåller en privat slut punkt för valvet, så att du kan använda det. Sedan kan du fortsätta att installera MARS-agenten och konfigurera säkerhets kopieringen enligt beskrivningen här. Du måste dock se till att all kommunikation för säkerhets kopiering sker via endast peer-nätverket.

Men om du tar bort privata slut punkter för valvet efter att en MARS-agent har registrerats på den, måste du registrera om behållaren med valvet. Du behöver inte sluta skydda dem.

## <a name="additional-topics"></a>Ytterligare information

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Skapa ett Recovery Services valv med hjälp av Azure Resource Manager-klienten

Du kan skapa Recovery Services-valvet och aktivera dess hanterade identitet (vilket gör att den hanterade identiteten krävs, som vi senare ser) med hjälp av Azure Resource Manager-klienten. Ett exempel på detta är att delas nedan:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

JSON-filen ovan bör ha följande innehåll:

Begär JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Svars-JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Valvet som skapats i det här exemplet via Azure Resource Manager-klienten har redan skapats med en systemtilldelad hanterad identitet.

### <a name="managing-permissions-on-resource-groups"></a>Hantera behörigheter för resurs grupper

Den hanterade identiteten för valvet måste ha följande behörigheter i resurs gruppen och det virtuella nätverk där de privata slut punkterna ska skapas:

- `Microsoft.Network/privateEndpoints/*` Detta krävs för att utföra CRUD på privata slut punkter i resurs gruppen. Den ska tilldelas till resurs gruppen.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Detta krävs på det virtuella nätverk där privat IP ska anslutas till den privata slut punkten.
- `Microsoft.Network/networkInterfaces/read` Detta krävs på resurs gruppen för att hämta det nätverks gränssnitt som skapats för den privata slut punkten.
- Rollen Privat DNS Zone-deltagare den här rollen finns redan och kan användas för att ge `Microsoft.Network/privateDnsZones/A/*` och ha `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` behörighet.

Du kan använda någon av följande metoder för att skapa roller med nödvändiga behörigheter:

#### <a name="create-roles-and-permissions-manually"></a>Skapa roller och behörigheter manuellt

Skapa följande JSON-filer och Använd PowerShell-kommandot i slutet av avsnittet för att skapa roller:

PrivateEndpointContributorRoleDef.jspå

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jspå

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jspå

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Använd ett skript

1. Starta **Cloud Shell** i Azure Portal och välj **Ladda upp fil** i PowerShell-fönstret.

    ![Välj Ladda upp fil i PowerShell-fönstret](./media/private-endpoints/upload-file-in-powershell.png)

1. Överför följande skript: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Gå till din arbetsmapp (till exempel: `cd /home/user` )

1. Kör följande skript:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Följande är parametrarna:

    - **prenumeration**: * * SubscriptionId som har resurs gruppen där den privata slut punkten för valvet ska skapas och under nätet där valvets privata slut punkt ska kopplas

    - **vaultPEResourceGroup**: resurs grupp där den privata slut punkten för valvet ska skapas

    - **vaultPESubnetResourceGroup**: resurs grupp för det undernät som den privata slut punkten ska kopplas till

    - **vaultMsiName**: namnet på valvets MSI, som är samma som **VaultName**

1. Slutför autentiseringen och skriptet tar kontexten för den angivna prenumerationen som anges ovan. Det skapar lämpliga roller om de saknas från klienten och tilldelar roller till valvets MSI.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Skapa privata slut punkter med Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatiskt godkända privata slut punkter

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Manuellt godkännande av privata slut punkter med hjälp av Azure Resource Manager-klienten

1. Använd **GetVault** för att hämta anslutnings-ID: t för den privata slut punkten.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Då returneras anslutnings-ID för privat slut punkt. Namnet på anslutningen kan hämtas med hjälp av den första delen av anslutnings-ID: t enligt följande:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Hämta **anslutnings-ID för privat slutpunkt** (och **namnet på den privata slut punkten**, där det behövs) från svaret och Ersätt det i följande JSON-och Azure Resource Manager-URI och försök att ändra status till "godkänd/nekad/frånkopplad", som visas i exemplet nedan:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    UTGÖR

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-ändringar för anpassade DNS-servrar

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Skapa DNS-zoner för anpassade DNS-servrar

Du måste skapa tre privata DNS-zoner och länka dem till det virtuella nätverket. Tänk på att i motsats till blob och kö registreras inte offentliga URL: er för säkerhets kopierings tjänsten i Azures offentliga DNS för omdirigering till DNS-zonerna för privata länkar. 

| **Zon**                                                     | **Tjänst** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Kö       |

>[!NOTE]
>I texten ovan refererar *geo* till regions koden. Till exempel *wcus* och *Ne* för USA, västra centrala respektive Nord Europa.

Referera till [den här listan](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) för region koder. Se följande länkar för URL-namn konventioner i nationella regioner:

- [Kina](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Tyskland](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Lägga till DNS-poster för anpassade DNS-servrar

Detta kräver att du skapar poster för varje FQDN i din privata slut punkt i din Privat DNS zon.

Det bör noteras att vi använder de privata slut punkter som skapats för säkerhets kopiering, blob och Kötjänst.

- Den privata slut punkten för valvet använder det namn som angavs när den privata slut punkten skapades
- De privata slut punkterna för blob-och Queue Services har prefixet samma namn som valvet.

Följande bild visar till exempel de tre privata slut punkter som har skapats för en privat slut punkts anslutning med namnet *pee2epe*:

![Tre privata slut punkter för en privat slut punkts anslutning](./media/private-endpoints/three-private-endpoints.png)

DNS-zon för säkerhets kopierings tjänsten ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navigera till din privata slut punkt för säkerhets kopiering i det **privata länk centret**. På sidan Översikt visas FQDN och privata IP-adresser för din privata slut punkt.

1. Lägg till en post för varje FQDN och privat IP som en typ post.

    ![Lägg till post för varje FQDN och privat IP](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

DNS-zon för Blob Service ( `privatelink.blob.core.windows.net` ):

1. Navigera till din privata slut punkt för BLOB i det **privata länk centret**. På sidan Översikt visas FQDN och privata IP-adresser för din privata slut punkt.

1. Lägg till en post för FQDN och privat IP som en typ post.

    ![Lägg till post för FQDN och privat IP som en typ post för Blob Service](./media/private-endpoints/add-type-a-record-for-blob.png)

DNS-zon för Kötjänst ( `privatelink.queue.core.windows.net` ):

1. Navigera till din privata slut punkt för kön i det **privata länk centret**. På sidan Översikt visas FQDN och privata IP-adresser för din privata slut punkt.

1. Lägg till en post för FQDN och privat IP som en typ post.

    ![Lägg till post för FQDN och privat IP som en typ post för Kötjänst](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. Kan jag skapa en privat slut punkt för ett befintligt säkerhets kopierings valv?<br>
A. Nej, privata slut punkter kan bara skapas för nya säkerhets kopierings valv. Därför får valvet inte ha haft några objekt skyddade. I själva verket kan inga försök att skydda objekt i valvet göras innan du skapar privata slut punkter.

F. Jag försökte skydda ett objekt till mitt valv, men det misslyckades och valvet innehåller fortfarande inga objekt som är skyddade för det. Kan jag skapa privata slut punkter för det här valvet?<br>
A. Nej, valvet får inte ha haft några försök att skydda några objekt tidigare.

F. Jag har ett valv som använder privata slut punkter för säkerhets kopiering och återställning. Kan jag senare lägga till eller ta bort privata slut punkter för det här valvet även om jag har säkerhetskopierat objekt som skyddas?<br>
A. Ja. Om du redan har skapat privata slut punkter för ett valv och skyddade säkerhets kopierings objekt till den, kan du senare lägga till eller ta bort privata slut punkter efter behov.

F. Kan den privata slut punkten för Azure Backup också användas för Azure Site Recovery?<br>
A. Nej, den privata slut punkten för säkerhets kopiering kan bara användas för Azure Backup. Du måste skapa en ny privat slut punkt för Azure Site Recovery om den stöds av tjänsten.

F. Jag missade något av stegen i den här artikeln och gick vidare för att skydda min data källa. Kan jag fortfarande använda privata slut punkter?<br>
A. Inte följer stegen i artikeln och fortsätter att skydda objekt kan leda till att valvet inte kan använda privata slut punkter. Vi rekommenderar därför att du läser den här check listan innan du fortsätter att skydda objekt.

F. Kan jag använda min egen DNS-server i stället för att använda Azures privata DNS-zon eller en integrerad privat DNS-zon?<br>
A. Ja, du kan använda dina egna DNS-servrar. Se dock till att alla obligatoriska DNS-poster läggs till enligt rekommendationerna i det här avsnittet.

F. Behöver jag utföra några ytterligare åtgärder på min server när jag har följt processen i den här artikeln?<br>
A. När du har använt processen som beskrivs i den här artikeln behöver du inte göra ytterligare arbete för att använda privata slut punkter för säkerhets kopiering och återställning.

## <a name="next-steps"></a>Nästa steg

- Läs om alla [säkerhetsfunktioner i Azure Backup](security-overview.md)
