# Laboratoire CSP

Une Content Security Policy (CSP) ou stratégie de sécurité du contenu permet d'améliorer la sécurité des sites
web en permettant de détecter et réduire certains types d'attaques, dont les attaques XSS (en-US) 
(Cross Site Scripting), ClickJacking, et les injections de contenu. Ces attaques peuvent être utilisées dans divers buts, 
comme le vol de données, le défacement de site ou la diffusion de malware.

## Activation

Pour activer CSP, vous devez configurer vos serveurs web afin d'ajouter un en-tête (header) HTTP Content-Security-Policy aux réponses. Vous pouvez rencontrer des documents qui mentionnent X-Content-Security-Policy comme en-tête, il s'agit d'une version obsolète qu'il n'est plus utile de supporter.

Une autre possibilité consiste à utiliser l'élément HTML <meta> pour configurer la règle, par exemple : 

    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">

L'utilisation du header plutôt que la balise meta est fortement recommandée afin d'éviter des attaques par manipulation du DOM.

## Créer votre règle CSP

On peut utiliser l'en-tête HTTP Content-Security-Policy pour définir la règle ainsi :

    Content-Security-Policy: règle

## Écrire une règle

Une règle est définie par une série de directives qui décrivent chacune le comportement attendu pour un certain type de contenu ou pour l'ensemble des requêtes. Une règle peut inclure une directive default-src pour la règle par défaut qui s'applique aux ressources pour lesquelles aucune règle n'est définie. Pour les autres types de règle, on pourra se référer à la page default-src. Pour bloquer les scripts intégrés au code HTML (JavaScript inline) et l'utilisation de eval(), une règle doit au moins contenir une directive default-src ou script-src. 

Pour bloquer les modifications de style intégrées au code HTML (CSS inline avec les attributs HTML &lt;style&gt;) et l'utilisation des balises style, une règle doit au moins contenir une directive default-src ou style-src.

## Documentation : 

 - https://developer.mozilla.org/fr/docs/Web/HTTP/CSP
 - https://www.w3.org/TR/CSP2/
 - https://www.imperva.com/learn/application-security/content-security-policy-csp-header/
 - https://web.dev/csp/
 - https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html
 - CSP Level 3 (2021) : https://www.w3.org/TR/CSP3/
 - CSP Level 2 (2016) : https://www.w3.org/TR/CSP2/ 
 - indication du degré de support par navigateur : https://content-security-policy.com/

## Exemples pour les cas courants
### Exemple 1

Ici, on souhaite que tout le contenu du site soit fourni par la même origine (on exclut les sous-domaines) :

    Content-Security-Policy: default-src 'self';

Pour chacune des directives suivantes, l'agent utilisateur consultera la directive default-src et utilisera sa valeur pour la directive demandée si celle-ci est absente :

| Sous directive impactée | Description |
| :---:   | :---: |
| child-src | source de navigation (frame et web-workers) |
| connect-src | restreint les URL chargeables (<a> avec attribut ping, XMLHttpRequest, ...) |
| font-src | chargement des fonts |
| frame-src | similaire à child-src |
| img-src | chargement des images |
| manifest-src | chargement du fichier manifest (descriptif utilisé par les ) |
| media-src | chargement des vidéos |
| object-src | chargement d'objets tel que des applets |
| prefetch-src | spécifie les ressources pré-chargées |
| script-src | chargement des scripts JavaScript ou encore les feuilles de style XSLT |
| script-src-elem | chargement des sources des scripts JavaScript (ne concerne pas le code inline) |
| script-src-attr | chargement des sources des scripts JavaScript pour le code inline (concerne la gestion des évènements) |
| style-src | chargement des feuilles de style CSS |
| style-src-elem | chargement des sources des feuilles de style CSS (ne concerne pas le code inline) |
| style-src-attr | chargement des sources des feuilles de style CSS (concerne des éléments spécifiques) |
| worker-src | spécifie les sources valides pour les workers |

Dans cette page, on remarque que le code javascript inline se charge normalement en l'abscence de l'en-tête CSP : 
    <a href="http://localhost/csp/example%201%20src%20self/01_example1%20without%20csp.html">Lien</a>

La même page chargée avec la CSP : <a href="http://localhost/csp/example%201%20src%20self/02_example1%20with%20csp.html">Lien</a>

Le code est chargé depuis une page externe sans CSP : <a href="http://localhost/csp/example%201%20src%20self/03_example1%20external.html">Lien</a>

Le code est chargé depuis une page externe avec CSP : <a href="http://localhost/csp/example%201%20src%20self/04_example1%20external%20with%20csp.html">Lien</a>

Le code est chargé depuis une page externe, et en externalisant le code inline : <a href="http://localhost/csp/example%201%20src%20self/05_example1%20fully%20external%20with%20csp.html">Lien</a>

### Exemple 2

Pour un site dont tout le contenu est fourni par le site lui-même ou par les sous-domaines de source-sure.example.net (qui peut être un autre site) :

    Content-Security-Policy: default-src 'self' *.source-sure.example.net

Exemple d'une image chargée sans CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/01_example2%20without%20csp.html">Lien</a>

Exemple d'une image chargée avec CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/02_example2%20with%20csp%20copy.html">Lien</a>

### Exemple 3

Pour un site dont les images peuvent venir de n'importe où, les musiques et vidéos de toto.local ou tata.local, les scripts par scripts.local :

    Content-Security-Policy: default-src 'self'; img-src *; media-src toto.local tata.local; script-src scripts.local

### Exemple 4

Pour un site dont les données sont critiques/privées et pour lequel toutes les données devraient être transmises en HTTPS depuis un domaine précis :

    Content-Security-Policy: default-src https://confidentiel.example.net

Cette règle force l'utilisation de HTTPS et exclut tout usage de contenu ne venant pas de https://confidentiel.example.net.

### Exemple 5

Pour un webmail qui permet d'afficher des mails incluant de l'HTML, des images provenant de n'importe où mais pas de JavaScript ou d'autres contenus potentiellement dangereux :

    Content-Security-Policy: default-src 'self'; img-src *; child-src: *

On notera que dans cet exemple, on n'a pas de directive script-src. C'est la directive default-src qui indique le comportement par défaut et donc qui limite le chargement des scripts à l'origine.

### Exemple 10

Gestion des frames.

B
+-------------+
|             |
|   A         |
|   +------+  |
|   |      |  |
|   |      +-------> frame-ancestors B;
|   +------+  |
|             |
+-----+-------+
      |
      |
      +------------> child-src A;

### Tester une règle CSP

Pour faciliter le déploiement de CSP, on peut configurer le serveur afin de rapporter uniquement les violations de règle sans appliquer réellement la règle. Ainsi, on peut s'assurer que la règle ne bloque pas les usages du site en récupérant les rapports de violation de la règle en test. On peut aussi tester des modifications d'une règle en place via ce même mécanisme.

Pour cela, il suffit d'utiliser l'en-tête Content-Security-Policy-Report-Only, comme cela :

    Content-Security-Policy-Report-Only: règle

Si les en-têtes HTTP Content-Security-Policy-Report-Only et Content-Security-Policy sont tous deux présents dans la réponse du serveur, les deux règles seront respectées, ce qui permet le test d'une nouvelle règle quand il y en a déjà une en place.

Il n'est pas autorisé de l'utiliser via la balise meta, notamment pour éviter des attaques via manipulation du DOM afin de rediriger des rapports d'erreur et ainsi surcharger une URL de reporting.

Si une règle contient une directive report-uri valide, les navigateurs qui prennent en charge CSP doivent envoyer un rapport pour chaque violation de la règle qu'ils détectent.

### Gérer les rapports

Par défaut, les violations de la règle de sécurité ne sont pas rapportées. Pour avoir des rapports de violation, il faut fournir directive report-uri avec au moins une URL valide à laquelle envoyer les rapports :

Content-Security-Policy: default-src 'self'; report-uri http://reportcollector.example.com/collector.cgi

Voici un exemple de rapport envoyé:

    {
        "csp-report": {
            "document-uri": "http://example.com/connexion.html",
            "referrer": "",
            "blocked-uri": "http://example.com/css/style.css",
            "violated-directive": "style-src cdn.example.com",
            "original-policy": "default-src 'none'; style-src cdn.example.com; report-uri /_/csp-reports"
        }
    }

## Menaces
### XSS

L'un des objectifs de CSP est la réduction et le rapport d'attaques XSS (injections de contenu). Les attaques XSS exploitent la confiance que les navigateurs ont dans le contenu reçu des serveurs. 

CSP permet aux administrateurs système de réduire ou éliminer les moyens de réaliser des attaques XSS en permettant de spécifier les domaines autorisés à fournir des scripts pour la page visitée. Un navigateur compatible avec CSP n'exécute que les scripts provenant d'une origine autorisée par les règles CSP reçues et ignore ceux qui ne sont pas autorisés. On peut ainsi bloquer les domaines non autorisés, les scripts inline (inclus dans une page HTML) ou associés à des événements via les attributs HTML dédiés.

### Empêcher les écoutes du trafic

Le serveur peut indiquer quels protocoles doivent être utilisés et par exemple forcer l'utilisation de HTTPS afin d'améliorer la sécurité. Une stratégie de sécurité complète pour la transmission des données peut non seulement forcer l'utilisation de TLS via HTTPS mais aussi forcer l'utilisation de cookies sécurisés (qui ne peuvent être envoyés qu'en HTTPS) et indiquer de convertir automatiquement toutes les requêtes qui auraient été faites en HTTP simple en requêtes HTTPS. L'utilisation de l'en-tête Strict-Transport-Security (en-US) permet de s'assurer que les navigateurs utilisent obligatoirement des connexions chiffrées en TLS (HTTPS).

## Question : 

> Quelles sont les attaques qui peuvent être évitées ? Et comment ?
>>- XSS
>>- ClickJacking
>>- Forcer le chiffrement des données