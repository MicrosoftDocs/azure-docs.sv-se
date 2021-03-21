---
title: Integrera Azure Key Vault med Azure Policy
description: Lär dig hur du integrerar Azure Key Vault med Azure Policy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92927761"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrera Azure Key Vault med Azure Policy

[Azure policy](../../governance/policy/index.yml) är ett styrnings verktyg som ger användare möjlighet att granska och hantera sin Azure-miljö i stor skala. Azure Policy ger möjlighet att placera guardrails på Azure-resurser för att se till att de är kompatibla med tilldelade princip regler. Det gör det möjligt för användare att utföra granskning, real tids genomförande och reparation av deras Azure-miljö. Resultaten av granskningar som utförs av en princip blir tillgängliga för användare på en instrument panel för efterlevnad där de kan se en detalj nivå som visar vilka resurser och komponenter som är kompatibla och vilka som inte är det.  Mer information finns i [översikten över tjänsten Azure policy](../../governance/policy/overview.md).

Exempel på användnings scenarier:

- Du vill förbättra position för ditt företag genom att implementera krav inom de minsta nyckel storlekarna och de maximala giltighets tiderna för certifikat i företagets nyckel valv, men du vet inte vilka team som kommer att vara kompatibla och vilka som inte är det.
- Du har för närvarande ingen lösning för att utföra en granskning i organisationen, eller så genomför du manuella granskningar av din miljö genom att be enskilda team inom organisationen att rapportera deras efterlevnad. Du letar efter ett sätt att automatisera den här uppgiften, utföra granskningar i real tid och garantera att granskningen är korrekt.
- Du vill tvinga företagets säkerhets principer och hindra personer från att skapa självsignerade certifikat, men du har inte ett automatiskt sätt att blockera skapandet av dem. 
- Du vill minska vissa krav för dina test team, men du vill ha bättre kontroll över din produktions miljö. Du behöver ett enkelt automatiserat sätt för att särskilja dina resurser.
- Du vill vara säker på att du kan återställa verk ställande av nya principer i händelse av ett problem med en aktiv webbplats. Du behöver en lösning med ett klick för att inaktivera tvång av principen. 
- Du förlitar dig på en lösning från tredje part för att granska din miljö och du vill använda ett internt Microsoft-erbjudande.

## <a name="types-of-policy-effects-and-guidance"></a>Typer av princip effekter och rikt linjer

**Granskning**: när en princips inverkan är inställd på granskning kommer principen inte att orsaka några större ändringar i din miljö. Den varnar dig endast om komponenter som inte följer princip definitionerna inom ett angivet omfång, genom att markera dessa komponenter som icke-kompatibla på instrument panelen för policyn för efterlevnad. Audit är standard om ingen princip påverkan har valts.

**Neka**: när en princips inverkan är inställd på neka, kommer principen att blockera skapandet av nya komponenter, t. ex. certifikat och blockera nya versioner av befintliga komponenter som inte överensstämmer med princip definitionen. Befintliga icke-kompatibla resurser i ett nyckel valv påverkas inte. Gransknings funktionerna kommer att fortsätta att köras.

## <a name="available-built-in-policy-definitions"></a>Tillgängliga "inbyggda" princip definitioner

Key Vault har skapat en uppsättning principer som kan användas för att hantera nyckel-, certifikat-och hemliga objekt. Dessa principer är inbyggda, vilket innebär att de inte kräver att du skriver någon anpassad JSON för att aktivera dem och att de är tillgängliga i Azure Portal som du kan tilldela. Du kan fortfarande anpassa vissa parametrar så att de passar din organisations behov.

# <a name="certificate-policies"></a>[Certifikatprinciper](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certifikaten ska ha den angivna maximala giltighets perioden (förhands granskning)

Med den här principen kan du hantera den maximala giltighets tiden för dina certifikat som lagras i Key Vault. Det är en bra säkerhets rutin att begränsa den maximala giltighets tiden för dina certifikat. Om en privat nyckel i certifikatet skulle bli komprometterad utan identifiering, minimeras tids ramen för pågående skada vid användning av korta livs längds certifikat och det minskar certifikatets värde till en angripare.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certifikat bör använda tillåtna nyckel typer (förhands granskning)

Med den här principen kan du begränsa vilken typ av certifikat som kan finnas i nyckel valvet. Du kan använda den här principen för att se till att dina certifikats privata nycklar är RSA, ECC eller är HSM-baserade. Du kan välja i följande lista vilka typer av certifikat som tillåts.

- RSA
- RSA-HSM
- ECC
- ECC-HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Certifikaten ska ha angiven livs längds åtgärd utlösare (för hands version)

Med den här principen kan du hantera den livs längds åtgärd som anges för certifikat som antingen ligger inom ett visst antal dagar efter att de gått ut eller har nått en viss procent andel av deras användbara livs längd.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certifikat ska utfärdas av den angivna integrerade certifikat utfärdaren (för hands version)

Om du använder en Key Vault integrerad certifikat utfärdare (DigiCert eller GlobalSign) och vill att användarna ska använda en eller någon av dessa providers kan du använda den här principen för att granska eller tillämpa ditt val. Den här principen kan även användas för att granska eller neka skapandet av självsignerade certifikat i Key Vault.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certifikat ska utfärdas av den angivna icke-integrerade certifikat utfärdaren (för hands version)

Om du använder en intern certifikat utfärdare eller en certifikat utfärdare som inte är integrerad med Key Vault och du vill att användarna ska använda en certifikat utfärdare från en lista som du anger, kan du använda den här principen för att skapa en lista över certifikat utfärdare med hjälp av utfärdarens namn. Den här principen kan även användas för att granska eller neka skapandet av självsignerade certifikat i Key Vault.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certifikat som använder Elliptic Curve-kryptografi måste ha tillåtna kurv namn (förhands granskning)

Om du använder Elliptic Curve Cryptography eller ECC-certifikat kan du anpassa listan över tillåtna kurv namn från listan nedan. Standard alternativet tillåter alla följande kurv namn.

- P-256
- P-256 K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certifikat med RSA-kryptografi hantera minsta nyckel storlek för RSA-certifikat (för hands version)

Om du använder RSA-certifikat kan du välja en minsta nyckel storlek som certifikaten måste innehålla. Du kan välja ett alternativ i listan nedan.

- 2048 bitar
- 3072 bitar
- 4096 bitar

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Hantera certifikat som ligger inom ett angivet antal dagars förfallo datum (förhands granskning)

Tjänsten kan drabbas av ett avbrott om ett certifikat som inte övervakas på ett bra sätt inte roteras innan det upphör att gälla. Den här principen är viktig för att se till att dina certifikat som lagras i Key Vault övervakas. Vi rekommenderar att du tillämpar den här principen flera gånger med olika förfallo trösklar, till exempel vid 180, 90, 60 och 30 dagars tröskelvärden. Den här principen kan användas för att övervaka och prioritering förfallo datum för certifikat i din organisation.

# <a name="key-policies"></a>[Nyckel principer](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Nycklar ska inte vara aktiva längre än det angivna antalet dagar (förhands granskning)

Om du vill se till att dina nycklar inte har varit aktiva längre än ett visst antal dagar kan du använda den här principen för att granska hur länge din nyckel har varit aktiv.

**Om din nyckel har en aktive rad aktiverings datum**, beräknar den här principen antalet dagar som har förflutit från **aktiverings datumet** för nyckeln till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du har angett markeras nyckeln som icke-kompatibel med principen.

**Om din nyckel inte har något aktiverings datum**, beräknar den här principen antalet dagar som har förflutit från det **skapande datumet** för nyckeln till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du har angett markeras nyckeln som icke-kompatibel med principen.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Nycklar ska vara den angivna kryptografiska typen RSA eller EG (för hands version)

Med den här principen kan du begränsa den typ av nycklar som kan finnas i nyckel valvet. Du kan använda den här principen för att se till att dina nycklar är RSA, ECC eller är HSM-baserade. Du kan välja i följande lista vilka typer av certifikat som tillåts.

- RSA
- RSA-HSM
- ECC
- ECC-HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Nycklar som använder Elliptic Curve-kryptografi ska ha de angivna kurv namnen (förhands granskning)

Om du använder Elliptic Curve Cryptography eller ECC-nycklar kan du anpassa en lista över tillåtna kurv namn i listan nedan. Standard alternativet tillåter alla följande kurv namn.

- P-256
- P-256 K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Nycklar ska ha datum som har angetts för förfallo datum (för hands version)

Den här principen granskar alla nycklar i nyckel valven och flagg nycklar som inte har något förfallo datum inställt som icke-kompatibelt. Du kan också använda den här principen för att blockera skapandet av nycklar som inte har angett ett förfallo datum.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Nycklar ska ha fler än det angivna antalet dagar före förfallo datum (för hands version)

Om en nyckel är för nära förfallo datum kan en organisations fördröjning för att rotera nyckeln leda till ett avbrott. Nycklar ska roteras vid ett visst antal dagar före förfallo datum för att ge tillräckligt med tid för att reagera på ett haveri. Den här principen granskar nycklar som är för nära deras förfallo datum och gör att du kan ange tröskelvärdet i dagar. Du kan också använda den här principen för att förhindra att nya nycklar som är för nära utgångs datum skapas.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Nycklar bör backas upp av en modul för maskin varu säkerhet (för hands version)

En HSM är en modul för maskin varu säkerhet som lagrar nycklar. En HSM tillhandahåller ett fysiskt skydds lager för kryptografiska nycklar. Krypterings nyckeln kan inte lämna en fysisk HSM som ger en högre säkerhets nivå än en program varu nyckel. Vissa organisationer har krav på efterlevnad som bestämmer användningen av HSM-nycklar. Använd den här principen om du vill granska nycklar som är lagrade i ditt nyckel valv som inte är HSM-backade. Du kan också använda den här principen för att blockera skapandet av nya nycklar som inte är HSM-säkerhetskopierade. Den här principen gäller för alla nyckel typer, RSA och ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Nycklar som använder RSA-kryptografi måste ha en angiven minsta nyckel storlek (förhands granskning)

Att använda RSA-nycklar med mindre nyckel storlekar är inte en säker design praxis. Du kan vara underkastad gransknings-och certifierings standarder som bestämmer användningen av en minsta nyckel storlek. Med följande princip kan du ange en minimi krav på nyckel storlek i ditt nyckel valv. Du kan granska nycklar som inte uppfyller minimi kravet. Den här principen kan också användas för att blockera skapandet av nya nycklar som inte uppfyller minimi kraven för nyckel storlek.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Nycklar ska ha den angivna maximala giltighets perioden (förhands granskning)

Hantera organisationens krav på efterlevnad genom att ange den maximala tid i dagar som en nyckel kan vara giltig i ditt nyckel valv. Nycklar som är giltiga längre än det angivna tröskelvärdet markeras som icke-kompatibel. Du kan också använda den här principen för att blockera skapandet av nya nycklar som har ett förfallo datum som infaller längre än den maximala giltighets perioden du anger.

# <a name="secret-policies"></a>[Hemliga principer](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Hemligheter ska inte vara aktiva längre än det angivna antalet dagar (förhands granskning)

Om du vill se till att dina hemligheter inte har varit aktiva längre än ett visst antal dagar kan du använda den här principen för att granska hur länge din hemlighet har varit aktiv.

**Om din hemlighet har en aktive rad aktiverings datum**, beräknar den här principen antalet dagar som har förflutit från **aktiverings datumet** för hemligheten till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du har angett markeras hemligheten som inkompatibel med principen.

**Om din hemlighet inte har en aktive rad aktiverings datum**, beräknar den här principen antalet dagar som har förflutit från det **datum** då hemligheten skapades till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du har angett markeras hemligheten som inkompatibel med principen.

### <a name="secrets-should-have-content-type-set-preview"></a>Hemligheter ska ha en innehålls typ uppsättning (förhands granskning)

Oformaterad text eller kodad fil kan lagras som en nyckel valv hemlighet. Din organisation kan dock vilja ange olika rotations principer och begränsningar för lösen ord, anslutnings strängar eller certifikat som lagras som nycklar. En innehålls typs tagg kan hjälpa en användare att se vad som lagras i ett hemligt objekt utan att läsa värdet för hemligheten. Du kan använda den här principen för att granska hemligheter som inte har en innehålls typs tag-uppsättning. Du kan också använda den här principen för att förhindra att nya hemligheter skapas om de inte har en innehålls typs tag-uppsättning.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Hemligheter bör ha förfallo datum angivet (förhands granskning)

Den här principen granskar alla hemligheter i ditt nyckel valv och flaggar hemligheter som inte har ett förfallo datum som är inkompatibla. Du kan också använda den här principen för att blockera skapandet av hemligheter som inte har angett ett förfallo datum.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Hemligheter ska ha mer än det angivna antalet dagar före förfallo datum (för hands version)

Om en hemlighet är för nära förfallo datum kan en organisations fördröjning för att rotera hemligheten leda till ett avbrott. Hemligheter bör roteras vid ett visst antal dagar före förfallo datum för att ge tillräckligt med tid för att reagera på ett haveri. Den här principen granskar hemligheter som ligger för nära sitt förfallo datum och gör att du kan ange tröskelvärdet i dagar. Du kan också använda den här principen för att förhindra att nya hemligheter skapas för nära sitt förfallo datum.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Hemligheter ska ha den angivna maximala giltighets perioden (förhands granskning)

Hantera organisationens krav på efterlevnad genom att ange den maximala tid i dagar som en hemlighet kan vara giltig i ditt nyckel valv. Hemligheter som är giltiga längre än det angivna tröskelvärdet markeras som icke-kompatibel. Du kan också använda den här principen för att blockera skapandet av nya hemligheter som har ett förfallo datum som infaller längre än den maximala giltighets perioden du anger.

---

## <a name="example-scenario"></a>Exempel på ett scenario

Du hanterar ett nyckel valv som används av flera team som innehåller 100-certifikat och du vill kontrol lera att inget av certifikaten i nyckel valvet är giltiga i mer än två år.

1. Du tilldelar **certifikaten att de ska ha den angivna maximala giltighets period** principen, ange att den maximala giltighets tiden för ett certifikat är 24 månader och ange principen som granskning. 
1. Du kan visa Kompatibilitetsrapport [på Azure Portal](#view-compliance-results)och upptäcka att 20 certifikat är icke-kompatibla och giltiga för > 2 år, och att återstående certifikat är kompatibla. 
1. Du kontaktar ägarna av dessa certifikat och kommunicerar med det nya säkerhets kravet att certifikaten inte får vara giltiga i mer än två år. Vissa team som svarar och 15 av certifikaten har förnyats med en maximal giltighets tid på 2 år eller mindre. Andra team svarar inte och du har fortfarande 5 icke-kompatibla certifikat i ditt nyckel valv.
1. Du ändrar resultatet av den princip som du har tilldelat till "Neka". De 5 icke-kompatibla certifikaten har inte återkallats och de fortsätter att fungera. De kan dock inte förnyas med en giltighets period som är större än två år. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Aktivera och hantera en Key Vault-princip via Azure Portal

### <a name="select-a-policy-definition"></a>Välj en princip definition

1. Logga in på Azure Portal. 
1. Sök i "princip" i Sök fältet och välj **princip**.

    ![Skärm bild som visar Sök fältet.](../media/policy-img1.png)

1. I fönstret princip väljer du **definitioner**.

    ![Skärm bild som visar alternativet definitioner.](../media/policy-img2.png)

1. I kategori filtret avmarkerar du **Markera alla** och väljer **Key Vault**. 

    ![Skärm bild som visar kategori filtret och den valda Key Vault kategorin.](../media/policy-img3.png)

1. Nu bör du kunna se alla principer som är tillgängliga för förhands granskning för Azure Key Vault. Se till att du har läst och förstått avsnittet princip vägledning ovan och välj en princip som du vill tilldela till ett omfång.  

    ![Skärm bild som visar de principer som är tillgängliga för en offentlig för hands version.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Tilldela en princip till ett omfång 

1. Välj en princip som du vill använda. i det här exemplet visas principen **Hantera certifikatets giltighets period** . Klicka på knappen tilldela i det övre vänstra hörnet.

    ![Skärm bild som visar principen hantera certifikatets giltighets period.](../media/policy-img5.png)
  
1. Välj den prenumeration där du vill att principen ska tillämpas. Du kan välja att begränsa omfattningen till endast en enda resurs grupp i en prenumeration. Om du vill tillämpa principen på hela prenumerationen och undanta vissa resurs grupper kan du också konfigurera en undantags lista. Ställ in princip tvångs väljaren på **aktive rad** om du vill att principen (granska eller neka) ska ske eller **inaktive** ras för att aktivera effekterna (granska eller neka). 

    ![Skärm bild som visar var du kan välja att begränsa omfattningen till endast en resurs grupp i en prenumeration.](../media/policy-img6.png)

1. Klicka på fliken Parametrar överst på skärmen för att ange den längsta giltighets perioden i månader som du vill ha. Välj **Granska** eller **neka** för att påverka principen enligt anvisningarna i avsnitten ovan. Välj sedan knappen granska + skapa. 

    ![Skärm bild som visar fliken Parametrar där du kan ange den maximala giltighets perioden i månader som du vill.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Visa resultat för efterlevnad

1. Gå tillbaka till bladet princip och välj fliken efterlevnad. Klicka på den princip tilldelning som du vill visa kompatibilitetsstatus för.

    ![Skärm bild som visar fliken kompatibilitet där du kan välja den princip tilldelning som du vill visa resultat för.](../media/policy-img8.png)

1. Från den här sidan kan du filtrera resultat efter kompatibla eller icke-kompatibla valv. Här kan du se en lista över icke-kompatibla nyckel valv inom omfånget för princip tilldelningen. Ett valv anses vara icke-kompatibelt om någon av komponenterna (certifikaten) i valvet inte är kompatibel. Du kan välja ett enskilt valv om du vill visa enskilda icke-kompatibla komponenter (certifikat). 


    ![Skärm bild som visar en lista över icke-kompatibla nyckel valv inom omfånget för princip tilldelningen.](../media/policy-img9.png)

1. Visa namnet på komponenterna i ett valv som inte är kompatibla


    ![Skärm bild som visar var du kan visa namnet på komponenterna i ett valv som inte är kompatibla.](../media/policy-img10.png)

1. Om du behöver kontrol lera om användarna nekas möjlighet att skapa resurser i Key Vault kan du klicka på fliken **komponent händelser (för hands version)** om du vill visa en översikt över nekade certifikat åtgärder med begär Anden och tidsstämpel för förfrågningar. 


    ![Översikt över hur Azure Key Vault fungerar](../media/policy-img11.png)

## <a name="feature-limitations"></a>Funktionsbegränsningar

Att tilldela en princip med en "Neka"-effekt kan ta upp till 30 minuter (genomsnittligt fall) och 1 timme (värsta fall) för att börja neka skapandet av icke-kompatibla resurser. Princip utvärderingen av befintliga komponenter i ett valv kan ta upp till 1 timme (genomsnittligt fall) och 2 timmar (värsta fall) innan resultatet visas i portalens gränssnitt. Om resultatet av efterlevnaden visas som "inte startad" kan det bero på följande orsaker:
- Princip värderingen har inte slutförts än. Den första utvärderings fördröjningen kan ta upp till 2 timmar i värsta fall scenariot. 
- Det finns inga nyckel valv i omfånget för princip tilldelningen.
- Det finns inga nyckel valv med certifikat inom omfånget för princip tilldelningen.

> [!NOTE]
> Azure Policy [resurs lägen](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)för olika leverantörer, till exempel för Azure Key Vault, ger information om kompatibilitet på sidan [komponenternas efterlevnad](../../governance/policy/how-to/get-compliance-data.md#component-compliance) .

## <a name="next-steps"></a>Nästa steg

- Läs mer om [tjänsten Azure policy](../../governance/policy/overview.md)
- Se Key Vault exempel: [Key Vault inbyggda princip definitioner](../../governance/policy/samples/built-in-policies.md#key-vault)
