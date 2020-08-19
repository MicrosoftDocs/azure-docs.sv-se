---
title: Azure Key Vault – använda mjuk borttagning med PowerShell
description: Lär dig hur du använder Azure PowerShell för att använda funktionen mjuk borttagning i Azure Key Vault som tillåter återställning av nyckel valv och Key Vault-objekt.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.openlocfilehash: 6ec784048d2c30d215fe046ccda39ef6f8a3daa5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585550"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Så här använder du Key Vault mjuk borttagning med PowerShell

Azure Key Vault funktionen för mjuk borttagning tillåter återställning av borttagna valv och valv objekt. Mer specifikt tar borttagning av adresser i följande scenarier:

- Stöd för rekonstruerbar borttagning av ett nyckel valv
- Stöd för rekonstruerbar borttagning av Key Vault-objekt; nycklar, hemligheter och certifikat

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 eller senare – om du inte har den här installationen, installerar Azure PowerShell och associerar den med din Azure-prenumeration, se [så här installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/). 

>[!NOTE]
> Det finns en inaktuell version av vår Key Vault PowerShell-fil för utdatafiler som **kan** läsas in i din miljö i stället för till rätt version. Vi förväntar sig en uppdaterad version av PowerShell som innehåller den korrigering som behövs för formatering av utdata och kommer att uppdatera det här avsnittet vid den tidpunkten. Den aktuella lösningen, om du stöter på det här formaterings problemet, är:
> - Använd följande fråga om du ser att du inte ser den aktiverade egenskapen mjuk borttagning som beskrivs i det här avsnittet: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete` .


Key Vault detaljerad referensinformation för PowerShell finns i [Azure Key Vault PowerShell-referens](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Behörigheter som krävs

Key Vault åtgärder hanteras separat via RBAC-behörigheter (rollbaserad åtkomst kontroll) enligt följande:

| Åtgärd | Description | Användar behörighet |
|:--|:--|:--|
|Lista|Visar en lista över borttagna nyckel valv.|Microsoft. nyckel valv/deletedVaults/läsa|
|Återställ|Återställer ett borttaget nyckel valv.|Microsoft. nyckel valv/valv/Skriv|
|Rensa|Tar permanent bort ett borttaget nyckel valv och allt dess innehåll.|Microsoft. nyckel-valv/platser/deletedVaults/rensning/åtgärd|

Mer information om behörigheter och åtkomst kontroll finns i [skydda nyckel valvet](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Aktivera mjuk borttagning

Du aktiverar "mjuk borttagning" för att tillåta återställning av ett borttaget nyckel valv, eller objekt som lagras i ett nyckel valv.

> [!IMPORTANT]
> Att aktivera "mjuk borttagning" i ett nyckel valv är en åtgärd som inte kan ångras. När egenskapen Soft-Delete har angetts till "true" går det inte att ändra eller ta bort den.  

### <a name="existing-key-vault"></a>Befintligt nyckel valv

För ett befintligt nyckel valv med namnet ContosoVault aktiverar du mjuk borttagning på följande sätt. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nytt nyckel valv

Mjuk borttagning är automatiskt aktiverat som standard för alla nya nyckel valv. Den 31 december 2020 är det inte längre möjligt att inaktivera mjuk borttagning i alla nyckel valv. 

### <a name="verify-soft-delete-enablement"></a>Verifiera mjuk borttagnings aktivering

Kontrol lera att ett nyckel valv har mjuk borttagning aktiverat genom att köra kommandot *show* och leta efter funktionen "mjuk borttagning aktive rad"? basattributet

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Ta bort ett skyddat nyckel valv med mjuk borttagning

Kommandot för att ta bort ett nyckel valvs ändringar i beteende, beroende på om mjuk borttagning är aktiverat.

> [!IMPORTANT]
>Om du kör följande kommando för ett nyckel valv som inte har mjuk borttagning aktiverat, kommer du att ta bort det här nyckel valvet och allt dess innehåll utan alternativ för återställning!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Hur mjuk borttagning skyddar dina nyckel valv

Med mjuk borttagning aktiverat:

- Ett borttaget nyckel valv tas bort från resurs gruppen och placeras i ett reserverat namn område som är kopplat till den plats där det skapades. 
- Borttagna objekt som nycklar, hemligheter och certifikat är inte tillgängliga så länge de innehåller nyckel valvet är i Borttaget tillstånd. 
- DNS-namnet för ett borttaget nyckel valv är reserverat så att det inte går att skapa ett nytt nyckel valv med samma namn.  

Du kan Visa borttagna tillstånds nyckel valv som är kopplade till din prenumeration med hjälp av följande kommando:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* kan användas för att identifiera resursen vid återställning eller rensning. 
- *Resurs-ID* är det ursprungliga resurs-ID: t för det här valvet. Eftersom det här nyckel valvet nu är i ett borttaget tillstånd finns det ingen resurs med det resurs-ID: t. 
- *Schemalagt rensnings datum* är när valvet tas bort permanent, om ingen åtgärd vidtas. Standard logg perioden, som används för att beräkna det *schemalagda rensnings datumet*, är 90 dagar.

## <a name="recovering-a-key-vault"></a>Återställer ett nyckel valv

Om du vill återställa ett nyckel valv anger du namnet, resurs gruppen och platsen för nyckel valvet. Observera platsen och resurs gruppen för det borttagna nyckel valvet, när du behöver dem för återställnings processen.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

När ett nyckel valv återställs skapas en ny resurs med nyckel valvets ursprungliga resurs-ID. Om den ursprungliga resurs gruppen tas bort måste du skapa en med samma namn innan du försöker återställa.

## <a name="deleting-and-purging-key-vault-objects"></a>Ta bort och rensa Key Vault-objekt

Följande kommando tar bort nyckeln "ContosoFirstKey" i ett nyckel valv med namnet "ContosoVault", som har mjuk borttagning aktiverat:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

När ditt nyckel valv har Aktiver ATS för mjuk borttagning visas en borttagen nyckel fortfarande för borttagning, om du inte uttryckligen listar borttagna nycklar. De flesta åtgärder på en nyckel i det borttagna läget Miss söker, förutom lista, återställning och rensning av en borttagen nyckel. 

Följande kommando visar till exempel borttagna nycklar i nyckel valvet "ContosoVault":

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Över gångs tillstånd 

När du tar bort en nyckel i ett nyckel valv med mjuk borttagning aktive rad kan det ta några sekunder innan över gången har slutförts. Under den här över gången kan det verka som om nyckeln inte är aktiv eller har statusen borttagen. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Använda mjuk borttagning med Key Vault-objekt

Precis som nyckel valv, har en borttagen nyckel, hemlighet eller certifikat kvar i Borttaget läge i upp till 90 dagar, om du inte återställer det eller rensar det. 

#### <a name="keys"></a>Nycklar

Återställa en mjuk borttagnings nyckel:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

För att permanent ta bort (även kallat rensa) en mjuk borttagnings nyckel:

> [!IMPORTANT]
> Om du rensar en nyckel tas den bort permanent och den går inte att återställa! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Åtgärder för att **återställa** och **Rensa** har sina egna behörigheter som är kopplade till en åtkomst princip för nyckel valv. För att en användare eller tjänstens huvud namn ska kunna utföra en åtgärd för att **återställa** eller **Rensa** måste de ha respektive behörighet för nyckeln eller hemligheten. Som standard läggs **Rensa** inte till i ett nyckel valvs åtkomst princip när genvägen "all" används för att bevilja alla behörigheter. Du måste särskilt bevilja behörigheten **Rensa** . 

#### <a name="set-a-key-vault-access-policy"></a>Ange en åtkomst princip för nyckel valvet

Följande kommando ger user@contoso.com behörighet att använda flera åtgärder på nycklar i *ContosoVault* , inklusive **Rensa**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Om du har ett befintligt nyckel valv som bara har haft mjuk borttagning aktiverat, kanske du inte har behörighet att **återställa** och **Rensa** .

#### <a name="secrets"></a>Hemligheter

Som nycklar hanteras hemligheter med sina egna kommandon:

- Ta bort en hemlighet med namnet SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Lista alla borttagna hemligheter i ett nyckel valv: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Återställa en hemlighet i Borttaget läge: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Rensa en hemlighet i Borttaget läge: 

  > [!IMPORTANT]
  > Om du rensar en hemlighet tas den bort permanent och den går inte att återställa!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certifikat

Du kan hantera certifikat med hjälp av följande kommandon:

- Ta bort ett certifikat: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Visa en lista med alla borttagna certifikat i ett nyckel valv: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Återställ ett certifikat i Borttaget läge: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Rensa ett certifikat i Borttaget läge: 

  > [!IMPORTANT]
  > Om du rensar ett certifikat tas det bort permanent och det går inte att återställa!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Rensar ett skyddat nyckel valv med mjuk borttagning

> [!IMPORTANT]
> Om du rensar ett nyckel valv eller ett av dess inneslutna objekt tas det bort permanent, vilket innebär att det inte går att återställa!

Funktionen rensa används för att permanent ta bort ett Key Vault-objekt eller ett helt nyckel valv, som tidigare varit borttaget. Som det visas i föregående avsnitt, kan objekt som lagras i ett nyckel valv med funktionen mjuk borttagning aktive ras i flera tillstånd:
- **Aktiv**: före borttagning.
- **Soft-borttagen**: efter borttagning kan de listas och återställas tillbaka till aktivt tillstånd.
- **Permanent borttagen**: efter rensning går det inte att återställa.


Samma sak gäller för nyckel valvet. Om du vill ta bort ett mjukt borttaget nyckel valv och dess innehåll permanent måste du ta bort själva nyckel valvet.

### <a name="purging-a-key-vault"></a>Rensar ett nyckel valv

När ett nyckel valv rensas tas hela innehållet bort permanent, inklusive nycklar, hemligheter och certifikat. Om du vill rensa ett mjukt borttaget nyckel valv använder du `Remove-AzKeyVault` kommandot med alternativet `-InRemovedState` och genom att ange platsen för det borttagna nyckel valvet med `-Location location` argumentet. Du kan hitta platsen för ett borttaget valv med hjälp av kommandot `Get-AzKeyVault -InRemovedState` .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Rensnings behörighet krävs
- För att rensa ett borttaget nyckel valv behöver användaren RBAC-behörighet till *Microsoft. nyckel valvet/locations/deletedVaults/rensning/åtgärd-* åtgärden. 
- Om du vill visa ett borttaget nyckel valv behöver användaren RBAC-behörighet till *Microsoft. nyckel valvet/deletedVaults/Read-* åtgärden. 
- Som standard har endast en prenumerations administratör dessa behörigheter. 

### <a name="scheduled-purge"></a>Schemalagd rensning

Om du visar borttagna Key Vault-objekt visas även när de är schemalagda att rensas av Key Vault. *Schemalagt rensnings datum* visar när ett Key Vault-objekt tas bort permanent, om ingen åtgärd utförs. Kvarhållningsperioden för ett borttaget Key Vault-objekt är som standard 90 dagar.

>[!IMPORTANT]
>Ett rensat valv objekt, utlöst av det *schemalagda rensnings datum* fältet, tas bort permanent. Det går inte att återskapa.

## <a name="enabling-purge-protection"></a>Aktiverar rensnings skydd

När rensnings skyddet är aktiverat kan ett valv eller ett objekt i Borttaget läge inte rensas förrän kvarhållningsperioden har passerat. Detta valv eller objekt kan fortfarande återställas. Den här funktionen ger ytterligare garantier för att ett valv eller ett objekt aldrig kan tas bort permanent förrän kvarhållningsperioden har passerat. Standard kvarhållningsperiod är 90 dagar, men när nyckel valvet skapas är det möjligt att ange ett värde mellan 7 och 90 dagar i kvarhållningsintervall. Bevarande principen rensa skydd använder samma intervall. När du har angett går det inte att ändra lagrings princip intervallet.

Du kan bara aktivera rensnings skydd om alternativet mjuk borttagning också är aktiverat. Inaktive ring av rensnings skydd stöds inte. 

Använd cmdleten [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) för att aktivera både mjuk borttagning och rensning av skydd när du skapar ett valv:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Om du vill lägga till rensnings skydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du cmdletarna [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)och [set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) :

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Andra resurser

- En översikt över Key Vault mjuk borttagnings funktionen finns i [Azure Key Vault översikt över mjuk borttagning](soft-delete-overview.md)).
- En allmän översikt över Azure Key Vault användning finns i [Vad är Azure Key Vault?](overview.md)).
