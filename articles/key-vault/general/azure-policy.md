---
title: Integrera Azure Key Vault med Azure Policy
description: Lär dig hur du integrerar Azure Key Vault med Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: cddc7b931bf59412d4a7ec8e6b0eecfe148f3d5e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749284"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrera Azure Key Vault med Azure Policy

[Azure Policy](../../governance/policy/index.yml) är ett styrningsverktyg som ger användarna möjlighet att granska och hantera sin Azure-miljö i stor skala. Azure Policy ger möjlighet att placera skyddsräcken på Azure-resurser för att säkerställa att de är kompatibla med tilldelade principregler. Den gör det möjligt för användare att utföra granskning, framtvingande i realtid och reparation av sin Azure-miljö. Resultaten av granskningar som utförs av principen kommer att vara tillgängliga för användare på en instrumentpanel för efterlevnad där de kan se en detaljgranskning av vilka resurser och komponenter som är kompatibla och vilka som inte är det.  Mer information finns i [Översikt över Azure Policy tjänsten](../../governance/policy/overview.md).

Exempel på användningsscenarier:

- Du vill förbättra företagets säkerhetsstatus genom att implementera krav på minsta nyckelstorlekar och längsta giltighetsperioder för certifikat i företagets nyckelvalv, men du vet inte vilka team som kommer att vara kompatibla och vilka som inte är det.
- Du har för närvarande ingen lösning för att utföra en granskning i organisationen, eller så utför du manuella granskningar av din miljö genom att be enskilda team i din organisation att rapportera om deras efterlevnad. Du letar efter ett sätt att automatisera den här uppgiften, utföra granskningar i realtid och garantera att granskningen är korrekt.
- Du vill framtvinga företagets säkerhetsprinciper och hindra enskilda användare från att skapa själv signerade certifikat, men du har inte något automatiserat sätt att blockera deras skapande. 
- Du vill lätta på vissa krav för testteamen, men du vill ha en strikt kontroll över produktionsmiljön. Du behöver ett enkelt automatiserat sätt att separera framtvingandet av dina resurser.
- Du vill vara säker på att du kan återställa framtvingandet av nya principer i händelse av problem med en live-webbplats. Du behöver en lösning med ett klick för att inaktivera tillämpningen av principen. 
- Du förlitar dig på en tredjepartslösning för att granska din miljö och du vill använda ett internt Microsoft-erbjudande.

## <a name="types-of-policy-effects-and-guidance"></a>Typer av principeffekter och vägledning

**Granskning:** När effekten av en princip är inställd på granskning kommer principen inte att orsaka några större ändringar i din miljö. Du får bara aviseringar om komponenter, till exempel certifikat som inte uppfyller principdefinitionerna inom ett angivet omfång, genom att markera dessa komponenter som icke-kompatibla i instrumentpanelen för principefterlevnad. Granskning är standard om ingen principeffekt har valts.

**Neka:** När effekten av en princip är inställd på att neka blockerar principen skapandet av nya komponenter, till exempel certifikat, samt blockerar nya versioner av befintliga komponenter som inte uppfyller principdefinitionen. Befintliga icke-kompatibla resurser i ett nyckelvalv påverkas inte. Granskningsfunktionerna fortsätter att fungera.

## <a name="available-built-in-policy-definitions"></a>Tillgängliga "inbyggda" principdefinitioner

Key Vault har skapat en uppsättning principer som kan användas för att hantera nyckel-, certifikat- och hemliga objekt. Dessa principer är inbyggda, vilket innebär att de inte kräver att du skriver någon anpassad JSON för att aktivera dem och att de är tillgängliga i Azure Portal som du kan tilldela. Du kan fortfarande anpassa vissa parametrar så att de passar organisationens behov.

# <a name="certificate-policies"></a>[Certifikatprinciper](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certifikat ska ha den angivna maximala giltighetsperioden (förhandsversion)

Med den här principen kan du hantera den maximala giltighetsperioden för dina certifikat som lagras i nyckelvalvet. Det är en bra säkerhetspraxis att begränsa den maximala giltighetsperioden för dina certifikat. Om en privat nyckel i certifikatet skulle komprometteras utan identifiering, minimerar användningen av kortvariga certifikat tidsramen för pågående skada och minskar värdet av certifikatet för en angripare.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certifikat ska använda tillåtna nyckeltyper (förhandsversion)

Med den här principen kan du begränsa vilken typ av certifikat som kan finnas i ditt nyckelvalv. Du kan använda den här principen för att se till att dina privata certifikatnycklar är RSA, ECC eller är HSM-backade. Du kan välja vilka certifikattyper som tillåts i följande lista.

- RSA
- RSA – HSM
- Ecc
- ECC – HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Certifikat ska ha de angivna utlösarna för livstidsåtgärden (förhandsversion)

Med den här principen kan du hantera den livslängdsåtgärd som angetts för certifikat som antingen infaller inom ett visst antal dagar efter förfallodatumet eller som har nått en viss procentandel av deras användbara livslängd.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certifikat ska utfärdas av den angivna integrerade certifikatutfärdaren (förhandsversion)

Om du använder en Key Vault-integrerad certifikatutfärdare (Digicert eller GlobalSign) och du vill att användarna ska använda en eller någon av dessa leverantörer kan du använda den här principen för att granska eller framtvinga ditt val. Den här principen kan också användas för att granska eller neka skapandet av själv signerade certifikat i Key Vault.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certifikat ska utfärdas av den angivna icke-integrerade certifikatutfärdaren (förhandsversion)

Om du använder en intern certifikatutfärdare eller en certifikatutfärdare som inte är integrerad med nyckelvalvet och du vill att användarna ska använda en certifikatutfärdare från en lista som du anger, kan du använda den här principen för att skapa en lista över tillåtna certifikatutfärdare efter utfärdarens namn. Den här principen kan också användas för att granska eller neka skapandet av själv signerade certifikat i Key Vault.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certifikat som använder kryptografi med ellipskurva bör ha tillåtna kurvanamn (förhandsversion)

Om du använder elliptic curve cryptography eller ECC-certifikat kan du anpassa en lista över tillåtna kurvanamn från listan nedan. Standardalternativet tillåter alla följande kurvanamn.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certifikat som använder RSA-kryptografi Hantera minsta nyckelstorlek för RSA-certifikat (förhandsversion)

Om du använder RSA-certifikat kan du välja en minsta nyckelstorlek som certifikaten måste ha. Du kan välja ett alternativ i listan nedan.

- 2 048 bitar
- 3072 bitar
- 4 096 bitar

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Hantera certifikat som är inom ett angivet antal dagar efter förfallodatum (förhandsversion)

Det kan bli avbrott i tjänsten om ett certifikat som inte övervakas korrekt inte roteras innan det upphör att gälla. Den här principen är viktig för att se till att dina certifikat som lagras i nyckelvalvet övervakas. Vi rekommenderar att du tillämpar den här principen flera gånger med olika tröskelvärden för förfallodatum, till exempel med tröskelvärdena 180, 90, 60 och 30 dagar. Den här principen kan användas för att övervaka och kontrollera förfallodatum för certifikat i din organisation.

# <a name="key-policies"></a>[Viktiga principer](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Nycklar bör inte vara aktiva längre än det angivna antalet dagar (förhandsversion)

Om du vill kontrollera att dina nycklar inte har varit aktiva längre än ett angivet antal dagar kan du använda den här principen för att granska hur länge nyckeln har varit aktiv.

**Om din nyckel har ett** aktiveringsdatum inställt beräknar den här principen  det antal dagar som har förflutit från aktiveringsdatumet för nyckeln till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du anger markeras nyckeln som icke-kompatibel med principen.

**Om nyckeln inte har** något aktiveringsdatum inställt beräknar den här principen det  antal dagar som har förflutit från skapandedatumet för nyckeln till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du anger markeras nyckeln som icke-kompatibel med principen.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Nycklarna ska vara den angivna kryptografiska typen RSA eller EC (förhandsversion)

Med den här principen kan du begränsa vilken typ av nycklar som kan finnas i ditt nyckelvalv. Du kan använda den här principen för att se till att dina nycklar är RSA, ECC eller är HSM-backade. Du kan välja vilka certifikattyper som tillåts i följande lista.

- RSA
- RSA – HSM
- Ecc
- ECC – HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Nycklar som använder kryptografi med ellipskurvan ska ha de angivna kurvans namn (förhandsversion)

Om du använder elliptic curve cryptography eller ECC-nycklar kan du anpassa en lista över tillåtna kurvanamn från listan nedan. Standardalternativet tillåter alla följande kurvanamn.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Nycklar bör ha utgångsdatum inställda (förhandsversion)

Den här principen granskar alla nycklar i dina nyckelvalv och flaggar nycklar som inte har ett utgångsdatum inställt som icke-kompatibelt. Du kan också använda den här principen för att blockera skapandet av nycklar som inte har ett anställt förfallodatum.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Nycklar bör ha mer än det angivna antalet dagar innan de upphör att gälla (förhandsversion)

Om en nyckel är för nära förfallotid kan en organisationsfördröjning för att rotera nyckeln resultera i ett avbrott. Nycklarna ska roteras vid ett angivet antal dagar innan de upphör att gälla för att ge tillräckligt med tid för att reagera på ett fel. Den här principen granskar nycklar som är för nära utgångsdatumet och gör att du kan ange det här tröskelvärdet i dagar. Du kan också använda den här principen för att förhindra att nya nycklar skapas som ligger för nära utgångsdatumet.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Nycklar bör backas upp av en maskinvarusäkerhetsmodul (förhandsversion)

En HSM är en maskinvarusäkerhetsmodul som lagrar nycklar. En HSM ger ett fysiskt skyddslager för kryptografiska nycklar. Den kryptografiska nyckeln kan inte lämna en fysisk HSM som ger en högre säkerhetsnivå än en programvarunyckel. Vissa organisationer har efterlevnadskrav som gör det möjligt att använda HSM-nycklar. Använd den här principen för att granska alla nycklar som lagras i ditt nyckelvalv som inte är HSM-säkerhetskopierade. Du kan också använda den här principen för att blockera skapandet av nya nycklar som inte är HSM-säkerhetskopierade. Den här principen gäller för alla nyckeltyper, RSA och ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Nycklar som använder RSA-kryptografi bör ha en angiven minsta nyckelstorlek (förhandsversion)

Att använda RSA-nycklar med mindre nyckelstorlekar är inte en säker designmetod. Du kan omfattas av gransknings- och certifieringsstandarder som gör det möjligt att använda en minsta nyckelstorlek. Med följande princip kan du ange ett krav på minsta nyckelstorlek för ditt nyckelvalv. Du kan granska nycklar som inte uppfyller detta minimikrav. Den här principen kan också användas för att blockera skapandet av nya nycklar som inte uppfyller minimikraven för nyckelstorlek.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Nycklarna ska ha den angivna maximala giltighetsperioden (förhandsversion)

Hantera organisationens efterlevnadskrav genom att ange den maximala tid i dagar som en nyckel kan vara giltig i ditt nyckelvalv. Nycklar som är giltiga längre än det tröskelvärde som du anger markeras som icke-kompatibla. Du kan också använda den här principen för att blockera skapandet av nya nycklar som har ett utgångsdatum som är längre än den längsta giltighetsperiod som du anger.

# <a name="secret-policies"></a>[Hemliga principer](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Hemligheter bör inte vara aktiva längre än det angivna antalet dagar (förhandsversion)

Om du vill kontrollera att dina hemligheter inte har varit aktiva längre än ett angivet antal dagar kan du använda den här principen för att granska hur länge hemligheten har varit aktiv.

**Om din hemlighet har ett aktiveringsdatum** inställt beräknar den här principen  det antal dagar som har förflutit från aktiveringsdatumet för hemligheten till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du anger markeras hemligheten som icke-kompatibel med principen.

**Om din hemlighet inte har** ett anställt aktiveringsdatum beräknar den här principen  det antal dagar som har förflutit från skapandedatumet för hemligheten till det aktuella datumet. Om antalet dagar överskrider det tröskelvärde som du anger markeras hemligheten som icke-kompatibel med principen.

### <a name="secrets-should-have-content-type-set-preview"></a>Hemligheter ska ha innehållstypsuppsättning (förhandsversion)

Oformaterad text eller kodad fil kan lagras som en nyckelvalvshemlighet. Men din organisation kanske vill ange olika rotationsprinciper och begränsningar för lösenord, anslutningssträngar eller certifikat som lagras som nycklar. En innehållstypstagg kan hjälpa en användare att se vad som lagras i ett hemligt objekt utan att läsa värdet för hemligheten. Du kan använda den här principen för att granska hemligheter som inte har en tagguppsättning för innehållstyp. Du kan också använda den här principen för att förhindra att nya hemligheter skapas om de inte har en tagguppsättning för innehållstyp.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Hemligheter bör ha utgångsdatum inställt (förhandsversion)

Den här principen granskar alla hemligheter i nyckelvalvet och flaggar hemligheter som inte har ett utgångsdatum inställt som icke-kompatibelt. Du kan också använda den här principen för att blockera skapandet av hemligheter som inte har ett anställt förfallodatum.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Hemligheter bör ha mer än det angivna antalet dagar innan de upphör att gälla (förhandsversion)

Om en hemlighet är för nära förfallodatum kan en organisationsfördröjning för att rotera hemligheten resultera i ett avbrott. Hemligheter bör roteras vid ett angivet antal dagar innan de upphör att gälla för att ge tillräckligt med tid för att reagera på ett fel. Den här principen granskar hemligheter som ligger för nära utgångsdatumet och gör att du kan ange det här tröskelvärdet i dagar. Du kan också använda den här principen för att förhindra att nya hemligheter skapas som ligger för nära förfallodatumet.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Hemligheter ska ha den angivna maximala giltighetsperioden (förhandsversion)

Hantera organisationens efterlevnadskrav genom att ange den maximala tid i dagar som en hemlighet kan vara giltig i ditt nyckelvalv. Hemligheter som är giltiga längre än det tröskelvärde som du anger markeras som icke-kompatibla. Du kan också använda den här principen för att blockera skapandet av nya hemligheter som har ett utgångsdatum som är längre än den längsta giltighetsperiod som du anger.

---

## <a name="example-scenario"></a>Exempel på ett scenario

Du hanterar ett nyckelvalv som används av flera team som innehåller 100 certifikat och du vill se till att inget av certifikaten i nyckelvalvet är giltigt längre än 2 år.

1. Du **tilldelar** certifikaten ska ha den angivna principen för maximal giltighetsperiod, anger att den maximala giltighetsperioden för ett certifikat är 24 månader och anger principens effekt till "granskning". 
1. Du visar [efterlevnadsrapporten](#view-compliance-results)på Azure Portal och upptäcker att 20 certifikat är icke-kompatibla och giltiga i > 2 år och att de återstående certifikaten är kompatibla. 
1. Du kontaktar ägarna av dessa certifikat och meddelar det nya säkerhetskravet att certifikat inte kan vara giltiga i mer än två år. Vissa team svarar och 15 av certifikaten förnyades med en maximal giltighetsperiod på 2 år eller mindre. Andra team svarar inte och du har fortfarande 5 icke-kompatibla certifikat i nyckelvalvet.
1. Du ändrar effekten av den princip som du har tilldelat till "neka". De fem icke-kompatibla certifikaten återkallas inte och de fortsätter att fungera. De kan dock inte förnyas med en giltighetsperiod som är längre än två år. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Aktivera och hantera en Key Vault princip via Azure Portal

### <a name="select-a-policy-definition"></a>Välj en principdefinition

1. Logga in på Azure Portal. 
1. Sök efter "Princip" i sökfältet och välj **princip.**

    ![Skärmbild som visar sökfältet.](../media/policy-img1.png)

1. I fönstret Princip väljer du **Definitioner.**

    ![Skärmbild som visar alternativet Definitioner.](../media/policy-img2.png)

1. I kategorifiltret avmarkerar du **Markera alla och** väljer **Key Vault**. 

    ![Skärmbild som visar kategorifiltret och den valda Key Vault kategori.](../media/policy-img3.png)

1. Nu bör du kunna se alla principer som är tillgängliga för offentlig förhandsversion för Azure Key Vault. Kontrollera att du har läst och förstått principvägledningen ovan och välj en princip som du vill tilldela till ett omfång.  

    ![Skärmbild som visar de principer som är tillgängliga för offentlig förhandsversion.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Tilldela en princip till ett omfång 

1. Välj en princip som du vill tillämpa. I det här exemplet visas principen Hantera **certifikatets** giltighetsperiod. Klicka på knappen Tilldela i det övre vänstra hörnet.

    ![Skärmbild som visar principen Hantera giltighetsperiod för certifikat.](../media/policy-img5.png)
  
1. Välj den prenumeration där du vill att principen ska tillämpas. Du kan välja att begränsa omfånget till endast en enskild resursgrupp i en prenumeration. Om du vill tillämpa principen på hela prenumerationen och exkludera vissa resursgrupper kan du också konfigurera en undantagslista. Ange tvingande principväljare till **Aktiverad** om du vill att effekten av  principen (granskning eller neka) ska inträffa eller Inaktiverad för att inaktivera effekten (granska eller neka). 

    ![Skärmbild som visar var du kan välja att begränsa omfånget till endast en enskild resursgrupp i en prenumeration.](../media/policy-img6.png)

1. Klicka på fliken Parametrar överst på skärmen för att ange den maximala giltighetsperioden i månader som du vill använda. Välj **granska** **eller neka** för effekten av principen enligt riktlinjerna i avsnitten ovan. Välj sedan knappen Granska + skapa. 

    ![Skärmbild som visar fliken Parametrar där du kan ange den maximala giltighetsperioden i månader som du vill ha.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Visa kompatibilitetsresultat

1. Gå tillbaka till bladet Princip och välj fliken Efterlevnad. Klicka på den principtilldelning som du vill visa kompatibilitetsresultat för.

    ![Skärmbild som visar fliken Efterlevnad där du kan välja den principtilldelning som du vill visa kompatibilitetsresultat för.](../media/policy-img8.png)

1. På den här sidan kan du filtrera resultat efter kompatibla eller icke-kompatibla valv. Här kan du se en lista över icke-kompatibla nyckelvalv inom omfånget för principtilldelningen. Ett valv betraktas som icke-kompatibelt om någon av komponenterna (certifikaten) i valvet inte är kompatibla. Du kan välja ett enskilt valv för att visa enskilda icke-kompatibla komponenter (certifikat). 


    ![Skärmbild som visar en lista över icke-kompatibla nyckelvalv inom principtilldelningens omfång.](../media/policy-img9.png)

1. Visa namnet på komponenterna i ett valv som inte är kompatibla


    ![Skärmbild som visar var du kan visa namnet på komponenterna i ett valv som inte är kompatibla.](../media/policy-img10.png)

1. Om du behöver kontrollera om användare nekas möjligheten att skapa resurser i nyckelvalvet kan du klicka på fliken Komponenthändelser **(förhandsversion)** för att visa en sammanfattning av nekade certifikatåtgärder med begärande och tidsstämplar för begäranden. 


    ![Översikt över hur Azure Key Vault fungerar](../media/policy-img11.png)

## <a name="feature-limitations"></a>Funktionsbegränsningar

Att tilldela en princip med en "nekande"-effekt kan ta upp till 30 minuter (genomsnittligt fall) och 1 timme (värsta fall) att börja neka skapandet av icke-kompatibla resurser. Principutvärderingen av befintliga komponenter i ett valv kan ta upp till 1 timme (genomsnittligt fall) och 2 timmar (värsta fall) innan kompatibilitetsresultat visas i portalens användargränssnitt. Om kompatibilitetsresultaten visas som "Inte startad" kan det bero på följande:
- Principen har inte slutförts ännu. Den inledande utvärderingssvarstid kan ta upp till 2 timmar i värsta fall. 
- Det finns inga nyckelvalv i principtilldelningens omfång.
- Det finns inga nyckelvalv med certifikat inom omfånget för principtilldelningen.

> [!NOTE]
> Azure Policy för [Resouce-providerlägen,](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)till exempel de för Azure Key Vault, anger du information om kompatibilitet på sidan [Komponentefterlevnad.](../../governance/policy/how-to/get-compliance-data.md#component-compliance)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Policy tjänsten](../../governance/policy/overview.md)
- Se Key Vault exempel: [Key Vault inbyggda principdefinitioner](../../governance/policy/samples/built-in-policies.md#key-vault)
