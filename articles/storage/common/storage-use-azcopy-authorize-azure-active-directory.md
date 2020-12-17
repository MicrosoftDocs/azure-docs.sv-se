---
title: Auktorisera åtkomst till blobbar med AzCopy & Azure Active Directory | Microsoft Docs
description: Du kan ange autentiseringsuppgifter för AzCopy-åtgärder med hjälp av Azure Active Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 46a5c941822dd258b420b51c710734dc3152f30f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617416"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Auktorisera åtkomst till blobbar med AzCopy och Azure Active Directory (Azure AD)

Du kan ange AzCopy med hjälp av Azure AD. På så sätt behöver du inte lägga till en SAS-token (signatur för delad åtkomst) i varje kommando. 

Börja med att verifiera roll tilldelningarna. Välj sedan vilken typ av _säkerhets objekt_ som du vill auktorisera. En [användar identitet](../../active-directory/fundamentals/add-users-azure-active-directory.md), en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)och ett [huvud namn för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md) är varje typ av säkerhets objekt.

En användar identitet är en användare som har en identitet i Azure AD. Det är det enklaste säkerhets objekt som ska auktoriseras. Hanterade identiteter och tjänstens huvud namn är bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan åtgärder från användaren. En hanterad identitet passar bättre för skript som körs från en virtuell Azure-dator (VM) och ett tjänst objekt är bättre lämpat för skript som körs lokalt. 

Mer information om AzCopy finns [i komma igång med AZCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Verifiera roll tilldelningar

Den behörighets nivå som du behöver baseras på om du planerar att ladda upp filer eller bara hämta dem.

Om du bara vill hämta filer kontrollerar du att rollen [Storage BLOB data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) har tilldelats till din användar identitet, hanterad identitet eller tjänstens huvud namn.

Om du vill ladda upp filer kontrollerar du att någon av dessa roller har tilldelats ditt säkerhets objekt:

- [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

De här rollerna kan tilldelas till säkerhets objekt i alla dessa omfattningar:

- Behållare (fil system)
- Lagringskonto
- Resursgrupp
- Prenumeration

Information om hur du verifierar och tilldelar roller finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tänk på att Azure Role-tilldelningar kan ta upp till fem minuter att sprida.

Du behöver inte ha någon av dessa roller tilldelade till ditt säkerhets objekt om ditt säkerhets objekt läggs till i åtkomst kontrol listan (ACL) för mål behållaren eller katalogen. I ACL: en måste ditt säkerhets objekt ha Skriv behörighet för mål katalogen och köra behörigheten för behållaren och varje överordnad katalog.

Mer information finns i avsnittet [om åtkomst kontroll modell i Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Auktorisera en användar identitet

När du har kontrollerat att din användar identitet har fått den behörighet som krävs, öppnar du en kommando tolk, skriver följande kommando och trycker sedan på RETUR-tangenten.

```azcopy
azcopy login
```

Om du får ett fel meddelande kan du prova med klient-ID för den organisation som lagrings kontot tillhör.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

Det här kommandot returnerar en autentiseringsnyckel och URL: en för en webbplats. Öppna webbplatsen, ange koden och välj sedan knappen **Nästa** .

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett inloggnings fönster visas. I det fönstret loggar du in på ditt Azure-konto med hjälp av dina autentiseringsuppgifter för Azure-kontot. När du har loggat in kan du stänga webbläsarfönstret och börja använda AzCopy.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Auktorisera en hanterad identitet

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar interaktion och skriptet körs från en virtuell Azure-dator (VM). När du använder det här alternativet behöver du inte lagra några autentiseringsuppgifter på den virtuella datorn.

Du kan logga in på ditt konto genom att använda en systemomfattande hanterad identitet som du har aktiverat på den virtuella datorn, eller genom att använda klient-ID, objekt-ID eller resurs-ID för en tilldelad hanterad identitet som du har tilldelat till den virtuella datorn.

Mer information om hur du aktiverar en systemomfattande hanterad identitet eller skapar en användardefinierad hanterad identitet finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Auktorisera genom att använda en systemomfattande hanterad identitet

Kontrol lera först att du har aktiverat en systemomfattande hanterad identitet på den virtuella datorn. Se [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Skriv sedan följande kommando i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Auktorisera med hjälp av en användardefinierad hanterad identitet

Kontrol lera först att du har aktiverat en användardefinierad hanterad identitet på den virtuella datorn. Se [användarens tilldelade hanterade identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Skriv sedan något av följande kommandon i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Ersätt `<client-id>` plats hållaren med klient-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Ersätt `<object-id>` plats hållaren med objekt-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Ersätt `<resource-id>` plats hållaren med resurs-ID för den användare som tilldelats den hanterade identiteten.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Auktorisera ett huvud namn för tjänsten

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar åtgärder, särskilt när du kör lokalt. Om du planerar att köra AzCopy på virtuella datorer som körs i Azure är det enklare att administrera en hanterad tjänst identitet. Mer information finns i avsnittet [auktorisera en hanterad identitet](#authorize-a-managed-identity) i den här artikeln.

Innan du kör ett skript måste du logga in interaktivt minst en stund så att du kan ange AzCopy med autentiseringsuppgifterna för tjänstens huvud namn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att ditt skript inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klient hemlighet eller genom att använda lösen ordet för ett certifikat som är kopplat till tjänstens huvud namn för appens registrering.

Mer information om hur du skapar tjänstens huvud namn finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).

Om du vill veta mer om tjänstens huvud namn i allmänhet, se [program-och tjänst huvud objekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Auktorisera ett huvud namn för tjänsten med hjälp av en klient hemlighet

Börja med att ställa in `AZCOPY_SPA_CLIENT_SECRET` miljövariabeln på klient hemligheten för tjänstens huvud namn för appens registrering.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik.  

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Ersätt `<application-id>` plats hållaren med program-ID: t för tjänstens huvud namn för appens registrering. Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Auktorisera ett huvud namn för tjänsten med hjälp av ett certifikat

Om du föredrar att använda dina egna autentiseringsuppgifter för auktorisering kan du ladda upp ett certifikat till appens registrering och sedan använda det certifikatet för att logga in.

Förutom att ladda upp ditt certifikat till din app-registrering måste du också ha en kopia av certifikatet som har sparats på datorn eller den virtuella dator där AzCopy ska köras. Den här kopian av certifikatet bör vara i. PFX eller. PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln bör vara lösenordsskyddad. Om du använder Windows och ditt certifikat bara finns i ett certifikat Arkiv, måste du exportera certifikatet till en PFX-fil (inklusive den privata nyckeln). Vägledning finns i [export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Ställ sedan in `AZCOPY_SPA_CERT_PASSWORD` miljövariabeln på certifikatets lösen ord.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan utföra den här uppgiften i PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Ersätt `<path-to-certificate-file>` plats hållaren med den relativa eller fullständigt kvalificerade sökvägen till certifikat filen. AzCopy sparar sökvägen till det här certifikatet, men det sparar inte en kopia av certifikatet, så se till att hålla certifikatet på plats. Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik. 

## <a name="authorize-without-a-secret-store"></a>Auktorisera utan ett hemligt Arkiv

Om operativ systemet inte har något hemligt Arkiv, till exempel en Linux- *nyckelring*, `azcopy login` fungerar inte kommandot. I stället kan du ange minnesbaserade variabler i minnet innan du kör varje åtgärd. 

### <a name="authorize-a-user-identity"></a>Auktorisera en användar identitet

När du har kontrollerat att din användar identitet har fått den nödvändiga behörighets nivån skriver du följande kommando och trycker sedan på RETUR-tangenten.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Kör sedan valfritt AzCopy-kommando (till exempel: `azcopy list https://contoso.blob.core.windows.net` ).

Det här kommandot returnerar en autentiseringsnyckel och URL: en för en webbplats. Öppna webbplatsen, ange koden och välj sedan knappen **Nästa** .

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett inloggnings fönster visas. I det fönstret loggar du in på ditt Azure-konto med hjälp av dina autentiseringsuppgifter för Azure-kontot. När du har loggat in kan åtgärden slutföras.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Auktorisera genom att använda en systemomfattande hanterad identitet

Kontrol lera först att du har aktiverat en systemomfattande hanterad identitet på den virtuella datorn. Se [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Skriv följande kommando och tryck sedan på RETUR-tangenten.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Kör sedan valfritt AzCopy-kommando (till exempel: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Auktorisera med hjälp av en användardefinierad hanterad identitet

Kontrol lera först att du har aktiverat en användardefinierad hanterad identitet på den virtuella datorn. Se [användarens tilldelade hanterade identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Skriv följande kommando och tryck sedan på RETUR-tangenten.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Skriv sedan något av följande kommandon och tryck sedan på RETUR-tangenten.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

Ersätt `<client-id>` plats hållaren med klient-ID: t för den användarspecifika hanterade identiteten.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

Ersätt `<object-id>` plats hållaren med objekt-ID: t för den användarspecifika hanterade identiteten.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

Ersätt `<resource-id>` plats hållaren med resurs-ID för den användare som tilldelats den hanterade identiteten.

När du har angett dessa variabler kan du köra valfritt AzCopy-kommando (till exempel: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-a-service-principal"></a>Auktorisera ett huvud namn för tjänsten

Innan du kör ett skript måste du logga in interaktivt minst en stund så att du kan ange AzCopy med autentiseringsuppgifterna för tjänstens huvud namn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att ditt skript inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klient hemlighet eller genom att använda lösen ordet för ett certifikat som är kopplat till tjänstens huvud namn för appens registrering.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Auktorisera ett huvud namn för tjänsten med hjälp av en klient hemlighet

Skriv följande kommando och tryck sedan på RETUR-tangenten.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
```

Ersätt `<application-id>` plats hållaren med program-ID: t för tjänstens huvud namn för appens registrering. Ersätt `<client-secret>` plats hållaren med klient hemligheten.

> [!NOTE]
> Överväg att använda en uppvarning för att samla in lösen ordet från användaren. På så sätt visas inte ditt lösen ord i kommando historiken. 

Kör sedan valfritt AzCopy-kommando (till exempel: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Auktorisera ett huvud namn för tjänsten med hjälp av ett certifikat

Om du föredrar att använda dina egna autentiseringsuppgifter för auktorisering kan du ladda upp ett certifikat till appens registrering och sedan använda det certifikatet för att logga in.

Förutom att ladda upp ditt certifikat till din app-registrering måste du också ha en kopia av certifikatet som har sparats på datorn eller den virtuella dator där AzCopy ska köras. Den här kopian av certifikatet bör vara i. PFX eller. PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln bör vara lösenordsskyddad. 

Skriv följande kommando och tryck sedan på RETUR-tangenten.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
```

Ersätt `<path-to-certificate-file>` plats hållaren med den relativa eller fullständigt kvalificerade sökvägen till certifikat filen. AzCopy sparar sökvägen till det här certifikatet, men det sparar inte en kopia av certifikatet, så se till att hålla certifikatet på plats. Ersätt `<certificate-password>` plats hållaren med lösen ordet för certifikatet.

> [!NOTE]
> Överväg att använda en uppvarning för att samla in lösen ordet från användaren. På så sätt visas inte ditt lösen ord i kommando historiken. 

Kör sedan valfritt AzCopy-kommando (till exempel: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Nästa steg

- Mer information om AzCopy finns [i komma igång med AZCopy](storage-use-azcopy-v10.md)

- Om du har frågor, problem eller allmän feedback kan du skicka dem [till GitHub](https://github.com/Azure/azure-storage-azcopy) -sidan.