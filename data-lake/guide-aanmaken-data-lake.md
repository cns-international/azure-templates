# Aanmaken Data Lake Storage Account

Om dagelijks toegang te hebben tot de laatste data door middel van een Data Lake is het nodig om een Azure Storage Account aan te maken in de eigen tenant.
Hiervoor hebben wij een aparte beschrijving gemaakt welke alle benodigde stappen beschrijft.

## Verzamelen gegevens

Ga na in welke regio de Power BI Online service is gevestigd voor de organisatie.
Om de Power BI regio te vinden, volg de instucties op https://docs.microsoft.com/en-us/power-bi/admin/service-admin-where-is-my-tenant-located.

Stel vast welke gebruikers groepen later toegang nodig hebben tot de data in de Data Lake.

## Aanmaken Resource Group

Maak in het Azure Portaal een resource group aan welke het Data Lake gaat huisvesten.
Zorg er voor dat de Resource Group in dezelfde regio wordt aangemaakt als waar Power BI Online draait.

## Aanmaken Azure Storage Account d.m.v. ARM-template

Voor het aanmaken van het Azure Storage Account hebben wij een ARM-template beschikbaar welke grotendeels de gewenste instellingen bevat.

### Uitrollen via Azure Portaal

1. Ga in het Azure Portaal naar de eerder aangemaakte Resource Group, klik op Create en dan Custom Deployment.
2. Klik dan op de link 'Build you own template in the editor'.
3. Vervang de template-stub met de door CNS aangeleverde ARM-template en klik op Save.
4. Vul de juiste gegevens in onder Project details.
5. Klik op Next: Review + Create.
6. Controleer de gegevens waaronder de regio waarin de resource aangemaakt zal worden en klik op Create.
7. Wacht op de deployment.

### Uitrollen via PowerShell

TODO

## Toegang op Containers

Om gebruikers rapporten op basis van de data te kunnen laten maken hebben deze rechten nodig op de containers.
Deze rechten moeten vanaf het begin al correct staan omdat individuele blobs deze rechten alleen bij het aanmaken over nemen van boven.
Wij adviseren daarom om gebruik te maken van groepen.

Onderstaande moet voor beide containers (Datamart, Sources) gevolgd worden.

### Instellen Access Control (IAM)

1. Ga naar het eerder aangemaakte Azure Storage Account en klik op Containers.
2. Klik op de container (Datamart, Sources).
3. Klik op Access Control (IAM) en klik op Add.
4. Kies bij Role voor Storage Blob Data Reader.
5. Kies bij Assign access to voor User, group, or service principal.
6. Kies de groepen sg-BI-Members en sg-BI-Admins.
   NOTE: sg-BI-Readers hebben geen rechten nodig (maken doorgaans geen rapporten aan).
7. Klik op Save.

### Instellen ACL's

1. Ga naar het eerder aangemaakte Azure Storage Account en klik op Containers.
2. Klik op de container (Datamart, Sources).
3. Klik op Manage ACL
4. Klik op Default permissions.
5. Vink Configure default permissions aan.
6. Klik op Add principal en selecteer de groepen sg-BI-Members en sg-BI-Admins.
7. Klik op Select.
8. Vink bij de zojuist toegevoegde principals de rollen Read en Execute aan.
9. Klik op Save.

### Aanmaken SAS Token

1. Ga naar het eerder aangemaakte Azure Storage Account en klik op Containers.
2. Klik op de container (Datamart, Sources).
3. Klik op Shared access signature.
4. Vink bij Permissions alles aan.
5. Vul bij Expiry een ruime datum in bijv. 2030.
6. Klik op Generate SAS token and URL.
7. Kopieer de Blob SAS token en Blob SAS URL naar een veilige locatie, deze zijn voor CNS nodig voor het uploaden van de data.

## Aanmaken Containers

Indien een bestaand Storage Account (Data Lake) gebruikt moet worden is het nodig om twee containers aan te maken, één voor de brondata en één voor de datamart.
Ga daarna verder met het sectie Toegang op Containers.

1. Ga naar het eerder aangemaakte Azure Storage Account en klik op Containers.
2. Klik op Container.
3. Geef een naam op (cns-datamart, empire).
   Public Access Level kan op Private blijven staan.
4. Klik op Create.
