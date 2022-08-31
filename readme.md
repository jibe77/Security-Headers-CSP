# Laboratoire CSP

Une Content Security Policy (CSP) ou stratégie de sécurité du contenu permet d'améliorer la sécurité des sites
web en permettant de détecter et réduire certains types d'attaques, dont les attaques XSS (en-US) 
(Cross Site Scripting), ClickJacking, l'interception de données non cryptées et les injections de contenu. Ces attaques peuvent être utilisées dans divers buts, 
comme le vol de données, le défacement de site ou la diffusion de malware.

Concernant les vulnérabilités XSS notamment, il ne faut pas voir les CSP comme une baguette magique permettant de rendre l'application sécurisée. Cela permet de limiter l'exploitabilité des vulnérabilités, mais elle ne les enlève pas.

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
  - CSP Level 2 (2016) : https://www.w3.org/TR/CSP2/ 
 - CSP Level 3 (2021) : https://www.w3.org/TR/CSP3/ (dernières spécifications, par toujours implémentée par les navigateurs actuels)
 - indication du degré de support par navigateur : https://content-security-policy.com/

## Articles : 

 - https://www.imperva.com/learn/application-security/content-security-policy-csp-header/
 - https://web.dev/csp/
 - https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html

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

On peut préciser d'avantage la règle en utilisant l'attribut script-src <a href="http://localhost/csp/example%201%20src%20self/06_example1%20csp%20script%20path%20all.html">Lien</a>

On peut renseigner également l'attribut script-src avec le mot-clé self <a href="http://localhost/csp/example%201%20src%20self/07_example1%20csp%20script%20path%20unique%20file.html">Lien</a>

Note : pour éviter qu'un script JavaScript ne manipule les cookies, on peut en supplémenter utiliser l'en-tête Set-Cookie avec le paramètre HttpOnly.

### Exemple 2

Pour un site dont tout le contenu est fourni par le site lui-même ou par les sous-domaines de source-sure.example.net (qui peut être un autre site) :

    Content-Security-Policy: default-src 'self' *.source-sure.example.net

Exemple d'une image chargée sans CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/01_example2%20without%20csp.html">Lien</a>

Exemple d'une image chargée avec CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/02_example2%20with%20csp%20copy.html">Lien</a>

Note : on peut faire l'inverse en bloquant un navigateur d'accéder aux ressources de notre site avec l'en-tête Cross-Origin-Resource-Policy avec la valeur same-site ou same-origin.

### Exemple 3

Pour un site dont les images peuvent venir de n'importe où, les musiques et vidéos de toto.local ou tata.local, les scripts par scripts.local :

    Content-Security-Policy: default-src 'self'; img-src *; media-src toto.local tata.local; script-src scripts.local

Exemple d'une image et d'une vidéo chargées sans CSP : <a href="http://localhost/csp/example%203%20img%20media/01_example3%20without%20csp.html">Lien</a>

Exemple d'une image et d'une vidéo chargées avec CSP, tout est bloqué : <a href="http://localhost/csp/example%203%20img%20media/02_example3%20with%20csp%20refuse%20all.html">Lien</a>

Exemple d'une image et d'une vidéo chargées avec CSP, blocage partiel : <a href="http://localhost/csp/example%203%20img%20media/03_example3%20with%20csp%20accept%20media.html">Lien</a>

### Exemple 4

Pour un site dont les données sont critiques/privées et pour lequel toutes les données devraient être transmises en HTTPS depuis un domaine précis :

    Content-Security-Policy: default-src https://confidentiel.example.net

Cette règle force l'utilisation de HTTPS et exclut tout usage de contenu ne venant pas de https://confidentiel.example.net.

 Chargement d'images sans CSP : <a href="http://localhost/csp/example%204%20force%20HTTPS/01_example4%20without%20csp.html">Lien</a>

 Chargement d'images avec CSP pour forcer HTTPS : <a href="http://localhost/csp/example%204%20force%20HTTPS/02_example4%20with%20csp.html">Lien</a>

 Bloque le chargement des images chargées en HTTP quand on accède à la page via HTTPS: <a href="https://localhost:443/csp/example%204%20force%20HTTPS/03_example4%20with%20csp.html">Lien</a>

Converti les appels HTTP en HTTPS automatiquement : <a href="http://localhost/csp/example%204%20force%20HTTPS/04_example4%20with%20csp%20upgrade.html">Lien</a>

Note : on peut également utiliser le header Strict-Transport-Security afin d'indiquer au browser qu'il faut obligatoirement utiliser la version sécurisée de l'application. Tous les futurs appels sur le domaine seront automatiquement redirigés par le navigateur vers le canal sécurisé.

### Exemple 5 : Tester une règle CSP

Pour faciliter le déploiement de CSP, on peut configurer le serveur afin de rapporter uniquement les violations de règle sans appliquer réellement la règle. Ainsi, on peut s'assurer que la règle ne bloque pas les usages du site en récupérant les rapports de violation de la règle en test. On peut aussi tester des modifications d'une règle en place via ce même mécanisme.

Pour cela, il suffit d'utiliser l'en-tête Content-Security-Policy-Report-Only, comme cela :

    Content-Security-Policy-Report-Only: règle

Si les en-têtes HTTP Content-Security-Policy-Report-Only et Content-Security-Policy sont tous deux présents dans la réponse du serveur, les deux règles seront respectées, ce qui permet le test d'une nouvelle règle quand il y en a déjà une en place.

Il n'est pas autorisé de l'utiliser via la balise meta, notamment pour éviter des attaques via manipulation du DOM afin de rediriger des rapports d'erreur et ainsi surcharger une URL de reporting.

Si une règle contient une directive report-uri valide, les navigateurs qui prennent en charge CSP doivent envoyer un rapport pour chaque violation de la règle qu'ils détectent.

Pour les exemples suivants on se base sur cette page affichant un alerte JavaScript: <a href="http://localhost/csp/example%205%20report%20only/01_example5%20without%20csp.html">Lien</a>

On voit que cette fonctionnalité ne fonctionne pas avec la balise meta, il faut obligatoirement utiliser l'en-tête HTTP: <a href="http://localhost/csp/example%205%20report%20only/02_example5%20with%20csp.html">Lien</a>

On a donc configuré un accès à cette page pour que le serveur Web ajoute l'en-tête  : <a href="http://localhost/csp/example%205%20report%20only/03_example5%20with%20csp%20in%20header.html">Lien</a>

Il est également possible de spécifier une URL de reporting : <a href="http://localhost/csp/example%205%20report%20only/04_example5%20with%20csp%20report.html">Lien</a>

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

### Exemple 6 : définition d'une exception à la policy pour le code inline

Il est possible d'autoriser l'exécution de code inline. Ce n'est pas conseillé mais cela peut amener les équipes de développement à effectuer une transition en douceur, en appliquant dans un premier temps une policy laxiste mais ne bloquant pas les fonctionnalités essentielles. 

On repart de l'exemple de base sans CSP : <a href="http://localhost/csp/example%206%20unsafe%20inline/01_example6%20without%20csp.html">Lien</a>

On applique une CSP par défaut, en spécifiant l'exception unsafe-inline : <a href="http://localhost/csp/example%206%20unsafe%20inline/02_example6%20with%20csp.html">Lien</a>

### Exemple 7 : application d'un hash

Permet d'autoriser du code inline mais de façon un plus sécurisée. Pour cela, un hash doit être généré du script afin que le navigateur autorise uniquement ce qui est permis par la policy.

La syntaxe est la suivante : 

    script-src 'self' 'sha256-So6kX9Z8JoHryyBu7rkEDpdM0H4XaxXtjvjPYsneglE=' 'unsafe-hashes';

On repart de l'exemple de base sans CSP : <a href="http://localhost/csp/example%207%20hash/01_example7%20without%20csp.html">Lien</a>

L'exemple suivant autorise l'exécution d'un code JavaScript inline : <a href="http://localhost/csp/example%207%20hash/02_example7%20with%20csp.html">Lien</a>

L'exemple suivant autorise l'exécution de plusieurs codes JavaScript inline :  : <a href="http://localhost/csp/example%207%20hash/03%20example7%20with%20multiple%20hashes.html">Lien</a>

### Exemple 8 : utilisation d'un nonce

Plus simple à mettre en place qu'un hash mais moins sécurisé, on spécifie l'identifiant d'un bloc de code :

    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'nonce-myNonce';">

    <script type="text/javascript" nonce="myNonce">
      ...
    </script>

On repart de l'exemple de base sans CSP : <a href="https://localhost/csp/example%208%20nonce/01_example8%20without%20csp.html">Lien</a>

L'exemple suivant autorise l'exécution d'un bloc de code inline : <a href="https://localhost/csp/example%208%20nonce/02_example8%20with%20csp.html">Lien</a>

### Exemple 9 : policy appliquée au style

On peut également indiquer le chemin autorisé pour le chargement des feuilles de style CSS. On utilise pour cela style-src.

On repart de l'exemple de base sans CSP : <a href="https://localhost/csp/example%209%20style/01_example9%20without%20csp.html">Lien</a>

On applique une policy afin de bloquer toutes les feuilles de style : <a href="https://localhost/csp/example%209%20style/02_example9%20with%20csp.html">Lien</a>

Remarque : pour que la policy fonctionne correctement, il est conseillé de passer par le protocole HTTP. Si on ouvre la page directement depuis le système de fichier. Si on ouvre le fichier depuis le filesystem, alors la feuille de style est chargée quand même : <a href="file:///home/jrenaux/Documents/Security-Headers-CSP/example%209%20style/02_example9%20with%20csp.html">Lien</a>

### Exemple 10 : Gestion des frames

La policy prend en compte deux attributs différents. "Frame-ancestors" permet d'indiquer la page parente autorisée. "child-src" permet d'indiquer la page que l'on est autorisée à charger.

frame-src et child-src sont similaires, mais frame-src est celle conseillée dans les dernières spécifications.

Cette page essaie de charger une page externe appliquant une policy particulière pour les frame-ancestors à 'self' et https://mycourses.w3schools.com, donc l'ouverture de la page n'est pas autorisée : <a href="http://localhost/csp/example%2010%20frame/01_example10%20external%20page%20with%20csp.html">Lien</a>

Cette page charge une page locale qui est protégée par cette policy : 

    Header always set Content-Security-Policy "frame-ancestors 'none';"

<a href="http://localhost/csp/example%2010%20frame/02_example10%20with%20csp%20in%20destination.html">Lien</a>

Remarque : les attributs child-src / frame-src sont pris en compte dans l'attribut générique default-src, mais pas frame-ancestors.

TODO : faire un exemple avec child-src

### Exemple 11 : Gestion des formulaires

L'attribut form-action permet de contrôler l'envoi des formulaires. Cet attribut ne fait pas parti de l'attribut générique "default-src".
Cette fonctionnalité est utile pour éviter des fuites de données via la manipulation du DOM.

Voici un formulaire : <a href="http://localhost/csp/example%2011%20form/01_example11%20without%20csp.html">Lien</a>

L'attribut form-action permet de contrôler le fonctionnement des formulaire : <a href="http://localhost/csp/example%2011%20form/02_example11%20with%20csp%20none.html">Lien</a>

L'attribut form-action 'self' permet d'activer uniquement les formulaire sur la même origine : <a href="http://localhost/csp/example%2011%20form/03_example11%20with%20csp%20none%20self.html">Lien</a>

### Exemple 12 : Gestion de la navigation

Il existe un attribut 'navigate-to' permettant de gérer les liens hypertext, les formulaires ainsi que window.location et window.open en JavaScript.

Voici une page sans CSP : <a href="http://localhost/csp/example%2012%20navigate-to/01_example12%20without%20csp.html">Lien</a>

Voici une page avec CSP : <a href="http://localhost/csp/example%2012%20navigate-to/02_example12%20with%20csp.html">Lien</a>

On remarque que cette directive n'est actuellement pas implémentée par les navigateurs. Voir la documentation : <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/navigate-to">Lien</a>.

### Exemple 13 : Activation d'une sandbox

L'attribut sandbox active similaire à la sandbox activable sur les iframe.
Cela applique des restrictions sur les actions realisables par la page, tel que le blocage des popups, le chargement de plugins ou les scripts.
Cet attribut est complémentaires aux autres attributs vu précédemment, car ils concernaient des ressources chargées par la page.

Voici une page sans CSP : <a href="http://localhost/csp/example%2013%20sandbox/01_example13%20without%20csp.html">Lien</a>

Voici une page avec CSP sandbox : <a href="http://localhost/csp/example%2013%20sandbox/02_example13%20with%20csp.html">Lien</a>

Il est possible de définir des exceptions en utilisant ces valeurs: allow-forms allow-same-origin allow-scripts allow-popups, allow-modals, allow-orientation-lock, allow-pointer-lock, allow-presentation, allow-popups-to-escape-sandbox, and allow-top-navigation
