# Laboratoire CSP

Une Content Security Policy (CSP) ou strat�gie de s�curit� du contenu permet d'am�liorer la s�curit� des sites
web en permettant de d�tecter et r�duire certains types d'attaques, dont les attaques XSS (en-US) 
(Cross Site Scripting), ClickJacking, l'interception de donn�es non crypt�es et les injections de contenu. Ces attaques peuvent �tre utilis�es dans divers buts, 
comme le vol de donn�es, le d�facement de site ou la diffusion de malware.

Concernant les vuln�rabilit�s XSS notamment, il ne faut pas voir les CSP comme une baguette magique permettant de rendre l'application s�curis�e. Cela permet de limiter l'exploitabilit� des vuln�rabilit�s, mais elle ne les enl�ve pas.

## Activation

Pour activer CSP, vous devez configurer vos serveurs web afin d'ajouter un en-t�te (header) HTTP Content-Security-Policy aux r�ponses. Vous pouvez rencontrer des documents qui mentionnent X-Content-Security-Policy comme en-t�te, il s'agit d'une version obsol�te qu'il n'est plus utile de supporter.

Une autre possibilit� consiste � utiliser l'�l�ment HTML <meta> pour configurer la r�gle, par exemple : 

    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">

L'utilisation du header plut�t que la balise meta est fortement recommand�e afin d'�viter des attaques par manipulation du DOM.

## Cr�er votre r�gle CSP

On peut utiliser l'en-t�te HTTP Content-Security-Policy pour d�finir la r�gle ainsi :

    Content-Security-Policy: r�gle

## �crire une r�gle

Une r�gle est d�finie par une s�rie de directives qui d�crivent chacune le comportement attendu pour un certain type de contenu ou pour l'ensemble des requ�tes. Une r�gle peut inclure une directive default-src pour la r�gle par d�faut qui s'applique aux ressources pour lesquelles aucune r�gle n'est d�finie. Pour les autres types de r�gle, on pourra se r�f�rer � la page default-src. Pour bloquer les scripts int�gr�s au code HTML (JavaScript inline) et l'utilisation de eval(), une r�gle doit au moins contenir une directive default-src ou script-src. 

Pour bloquer les modifications de style int�gr�es au code HTML (CSS inline avec les attributs HTML &lt;style&gt;) et l'utilisation des balises style, une r�gle doit au moins contenir une directive default-src ou style-src.

## Documentation : 

 - https://developer.mozilla.org/fr/docs/Web/HTTP/CSP
  - CSP Level 2 (2016) : https://www.w3.org/TR/CSP2/ 
 - CSP Level 3 (2021) : https://www.w3.org/TR/CSP3/ (derni�res sp�cifications, par toujours impl�ment�e par les navigateurs actuels)
 - indication du degr� de support par navigateur : https://content-security-policy.com/

## Articles : 

 - https://www.imperva.com/learn/application-security/content-security-policy-csp-header/
 - https://web.dev/csp/
 - https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html

## Exemples pour les cas courants
### Exemple 1

Ici, on souhaite que tout le contenu du site soit fourni par la m�me origine (on exclut les sous-domaines) :

    Content-Security-Policy: default-src 'self';

Pour chacune des directives suivantes, l'agent utilisateur consultera la directive default-src et utilisera sa valeur pour la directive demand�e si celle-ci est absente :

| Sous directive impact�e | Description |
| :---:   | :---: |
| child-src | source de navigation (frame et web-workers) |
| connect-src | restreint les URL chargeables (<a> avec attribut ping, XMLHttpRequest, ...) |
| font-src | chargement des fonts |
| frame-src | similaire � child-src |
| img-src | chargement des images |
| manifest-src | chargement du fichier manifest (descriptif utilis� par les ) |
| media-src | chargement des vid�os |
| object-src | chargement d'objets tel que des applets |
| prefetch-src | sp�cifie les ressources pr�-charg�es |
| script-src | chargement des scripts JavaScript ou encore les feuilles de style XSLT |
| script-src-elem | chargement des sources des scripts JavaScript (ne concerne pas le code inline) |
| script-src-attr | chargement des sources des scripts JavaScript pour le code inline (concerne la gestion des �v�nements) |
| style-src | chargement des feuilles de style CSS |
| style-src-elem | chargement des sources des feuilles de style CSS (ne concerne pas le code inline) |
| style-src-attr | chargement des sources des feuilles de style CSS (concerne des �l�ments sp�cifiques) |
| worker-src | sp�cifie les sources valides pour les workers |

Dans cette page, on remarque que le code javascript inline se charge normalement en l'abscence de l'en-t�te CSP : 
    <a href="http://localhost/csp/example%201%20src%20self/01_example1%20without%20csp.html">Lien</a>

La m�me page charg�e avec la CSP : <a href="http://localhost/csp/example%201%20src%20self/02_example1%20with%20csp.html">Lien</a>

Le code est charg� depuis une page externe sans CSP : <a href="http://localhost/csp/example%201%20src%20self/03_example1%20external.html">Lien</a>

Le code est charg� depuis une page externe avec CSP : <a href="http://localhost/csp/example%201%20src%20self/04_example1%20external%20with%20csp.html">Lien</a>

Le code est charg� depuis une page externe, et en externalisant le code inline : <a href="http://localhost/csp/example%201%20src%20self/05_example1%20fully%20external%20with%20csp.html">Lien</a>

On peut pr�ciser d'avantage la r�gle en utilisant l'attribut script-src <a href="http://localhost/csp/example%201%20src%20self/06_example1%20csp%20script%20path%20all.html">Lien</a>

On peut renseigner �galement l'attribut script-src avec le mot-cl� self <a href="http://localhost/csp/example%201%20src%20self/07_example1%20csp%20script%20path%20unique%20file.html">Lien</a>

Note : pour �viter qu'un script JavaScript ne manipule les cookies, on peut en suppl�menter utiliser l'en-t�te Set-Cookie avec le param�tre HttpOnly.

### Exemple 2

Pour un site dont tout le contenu est fourni par le site lui-m�me ou par les sous-domaines de source-sure.example.net (qui peut �tre un autre site) :

    Content-Security-Policy: default-src 'self' *.source-sure.example.net

Exemple d'une image charg�e sans CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/01_example2%20without%20csp.html">Lien</a>

Exemple d'une image charg�e avec CSP : <a href="http://localhost/csp/example%202%20src%20sub%20domain/02_example2%20with%20csp%20copy.html">Lien</a>

Note : on peut faire l'inverse en bloquant un navigateur d'acc�der aux ressources de notre site avec l'en-t�te Cross-Origin-Resource-Policy avec la valeur same-site ou same-origin.

### Exemple 3

Pour un site dont les images peuvent venir de n'importe o�, les musiques et vid�os de toto.local ou tata.local, les scripts par scripts.local :

    Content-Security-Policy: default-src 'self'; img-src *; media-src toto.local tata.local; script-src scripts.local

Exemple d'une image et d'une vid�o charg�es sans CSP : <a href="http://localhost/csp/example%203%20img%20media/01_example3%20without%20csp.html">Lien</a>

Exemple d'une image et d'une vid�o charg�es avec CSP, tout est bloqu� : <a href="http://localhost/csp/example%203%20img%20media/02_example3%20with%20csp%20refuse%20all.html">Lien</a>

Exemple d'une image et d'une vid�o charg�es avec CSP, blocage partiel : <a href="http://localhost/csp/example%203%20img%20media/03_example3%20with%20csp%20accept%20media.html">Lien</a>

### Exemple 4

Pour un site dont les donn�es sont critiques/priv�es et pour lequel toutes les donn�es devraient �tre transmises en HTTPS depuis un domaine pr�cis :

    Content-Security-Policy: default-src https://confidentiel.example.net

Cette r�gle force l'utilisation de HTTPS et exclut tout usage de contenu ne venant pas de https://confidentiel.example.net.

 Chargement d'images sans CSP : <a href="http://localhost/csp/example%204%20force%20HTTPS/01_example4%20without%20csp.html">Lien</a>

 Chargement d'images avec CSP pour forcer HTTPS : <a href="http://localhost/csp/example%204%20force%20HTTPS/02_example4%20with%20csp.html">Lien</a>

 Bloque le chargement des images charg�es en HTTP quand on acc�de � la page via HTTPS: <a href="https://localhost:443/csp/example%204%20force%20HTTPS/03_example4%20with%20csp.html">Lien</a>

Converti les appels HTTP en HTTPS automatiquement : <a href="http://localhost/csp/example%204%20force%20HTTPS/04_example4%20with%20csp%20upgrade.html">Lien</a>

Note : on peut �galement utiliser le header Strict-Transport-Security afin d'indiquer au browser qu'il faut obligatoirement utiliser la version s�curis�e de l'application. Tous les futurs appels sur le domaine seront automatiquement redirig�s par le navigateur vers le canal s�curis�.

### Exemple 5 : Tester une r�gle CSP

Pour faciliter le d�ploiement de CSP, on peut configurer le serveur afin de rapporter uniquement les violations de r�gle sans appliquer r�ellement la r�gle. Ainsi, on peut s'assurer que la r�gle ne bloque pas les usages du site en r�cup�rant les rapports de violation de la r�gle en test. On peut aussi tester des modifications d'une r�gle en place via ce m�me m�canisme.

Pour cela, il suffit d'utiliser l'en-t�te Content-Security-Policy-Report-Only, comme cela :

    Content-Security-Policy-Report-Only: r�gle

Si les en-t�tes HTTP Content-Security-Policy-Report-Only et Content-Security-Policy sont tous deux pr�sents dans la r�ponse du serveur, les deux r�gles seront respect�es, ce qui permet le test d'une nouvelle r�gle quand il y en a d�j� une en place.

Il n'est pas autoris� de l'utiliser via la balise meta, notamment pour �viter des attaques via manipulation du DOM afin de rediriger des rapports d'erreur et ainsi surcharger une URL de reporting.

Si une r�gle contient une directive report-uri valide, les navigateurs qui prennent en charge CSP doivent envoyer un rapport pour chaque violation de la r�gle qu'ils d�tectent.

Pour les exemples suivants on se base sur cette page affichant un alerte JavaScript: <a href="http://localhost/csp/example%205%20report%20only/01_example5%20without%20csp.html">Lien</a>

On voit que cette fonctionnalit� ne fonctionne pas avec la balise meta, il faut obligatoirement utiliser l'en-t�te HTTP: <a href="http://localhost/csp/example%205%20report%20only/02_example5%20with%20csp.html">Lien</a>

On a donc configur� un acc�s � cette page pour que le serveur Web ajoute l'en-t�te  : <a href="http://localhost/csp/example%205%20report%20only/03_example5%20with%20csp%20in%20header.html">Lien</a>

Il est �galement possible de sp�cifier une URL de reporting : <a href="http://localhost/csp/example%205%20report%20only/04_example5%20with%20csp%20report.html">Lien</a>

Par d�faut, les violations de la r�gle de s�curit� ne sont pas rapport�es. Pour avoir des rapports de violation, il faut fournir directive report-uri avec au moins une URL valide � laquelle envoyer les rapports :

Content-Security-Policy: default-src 'self'; report-uri http://reportcollector.example.com/collector.cgi

Voici un exemple de rapport envoy�:

    {
        "csp-report": {
            "document-uri": "http://example.com/connexion.html",
            "referrer": "",
            "blocked-uri": "http://example.com/css/style.css",
            "violated-directive": "style-src cdn.example.com",
            "original-policy": "default-src 'none'; style-src cdn.example.com; report-uri /_/csp-reports"
        }
    }

### Exemple 6 : d�finition d'une exception � la policy pour le code inline

Il est possible d'autoriser l'ex�cution de code inline. Ce n'est pas conseill� mais cela peut amener les �quipes de d�veloppement � effectuer une transition en douceur, en appliquant dans un premier temps une policy laxiste mais ne bloquant pas les fonctionnalit�s essentielles. 

On repart de l'exemple de base sans CSP : <a href="http://localhost/csp/example%206%20unsafe%20inline/01_example6%20without%20csp.html">Lien</a>

On applique une CSP par d�faut, en sp�cifiant l'exception unsafe-inline : <a href="http://localhost/csp/example%206%20unsafe%20inline/02_example6%20with%20csp.html">Lien</a>

### Exemple 7 : application d'un hash

Permet d'autoriser du code inline mais de fa�on un plus s�curis�e. Pour cela, un hash doit �tre g�n�r� du script afin que le navigateur autorise uniquement ce qui est permis par la policy.

La syntaxe est la suivante : 

    script-src 'self' 'sha256-So6kX9Z8JoHryyBu7rkEDpdM0H4XaxXtjvjPYsneglE=' 'unsafe-hashes';

On repart de l'exemple de base sans CSP : <a href="http://localhost/csp/example%207%20hash/01_example7%20without%20csp.html">Lien</a>

L'exemple suivant autorise l'ex�cution d'un code JavaScript inline : <a href="http://localhost/csp/example%207%20hash/02_example7%20with%20csp.html">Lien</a>

L'exemple suivant autorise l'ex�cution de plusieurs codes JavaScript inline :  : <a href="http://localhost/csp/example%207%20hash/03%20example7%20with%20multiple%20hashes.html">Lien</a>

### Exemple 8 : utilisation d'un nonce

Plus simple � mettre en place qu'un hash mais moins s�curis�, on sp�cifie l'identifiant d'un bloc de code :

    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'nonce-myNonce';">

    <script type="text/javascript" nonce="myNonce">
      ...
    </script>

On repart de l'exemple de base sans CSP : <a href="https://localhost/csp/example%208%20nonce/01_example8%20without%20csp.html">Lien</a>

L'exemple suivant autorise l'ex�cution d'un bloc de code inline : <a href="https://localhost/csp/example%208%20nonce/02_example8%20with%20csp.html">Lien</a>

### Exemple 9 : policy appliqu�e au style

On peut �galement indiquer le chemin autoris� pour le chargement des feuilles de style CSS. On utilise pour cela style-src.

On repart de l'exemple de base sans CSP : <a href="https://localhost/csp/example%209%20style/01_example9%20without%20csp.html">Lien</a>

On applique une policy afin de bloquer toutes les feuilles de style : <a href="https://localhost/csp/example%209%20style/02_example9%20with%20csp.html">Lien</a>

Remarque : pour que la policy fonctionne correctement, il est conseill� de passer par le protocole HTTP. Si on ouvre la page directement depuis le syst�me de fichier. Si on ouvre le fichier depuis le filesystem, alors la feuille de style est charg�e quand m�me : <a href="file:///home/jrenaux/Documents/Security-Headers-CSP/example%209%20style/02_example9%20with%20csp.html">Lien</a>

### Exemple 10 : Gestion des frames

La policy prend en compte deux attributs diff�rents. "Frame-ancestors" permet d'indiquer la page parente autoris�e. "child-src" permet d'indiquer la page que l'on est autoris�e � charger.

frame-src et child-src sont similaires, mais frame-src est celle conseill�e dans les derni�res sp�cifications.

Cette page essaie de charger une page externe appliquant une policy particuli�re pour les frame-ancestors � 'self' et https://mycourses.w3schools.com, donc l'ouverture de la page n'est pas autoris�e : <a href="http://localhost/csp/example%2010%20frame/01_example10%20external%20page%20with%20csp.html">Lien</a>

Cette page charge une page locale qui est prot�g�e par cette policy : 

    Header always set Content-Security-Policy "frame-ancestors 'none';"

<a href="http://localhost/csp/example%2010%20frame/02_example10%20with%20csp%20in%20destination.html">Lien</a>

Remarque : les attributs child-src / frame-src sont pris en compte dans l'attribut g�n�rique default-src, mais pas frame-ancestors.

TODO : faire un exemple avec child-src

### Exemple 11 : Gestion des formulaires

L'attribut form-action permet de contr�ler l'envoi des formulaires. Cet attribut ne fait pas parti de l'attribut g�n�rique "default-src".
Cette fonctionnalit� est utile pour �viter des fuites de donn�es via la manipulation du DOM.

Voici un formulaire : <a href="http://localhost/csp/example%2011%20form/01_example11%20without%20csp.html">Lien</a>

L'attribut form-action permet de contr�ler le fonctionnement des formulaire : <a href="http://localhost/csp/example%2011%20form/02_example11%20with%20csp%20none.html">Lien</a>

L'attribut form-action 'self' permet d'activer uniquement les formulaire sur la m�me origine : <a href="http://localhost/csp/example%2011%20form/03_example11%20with%20csp%20none%20self.html">Lien</a>

### Exemple 12 : Gestion de la navigation

Il existe un attribut 'navigate-to' permettant de g�rer les liens hypertext, les formulaires ainsi que window.location et window.open en JavaScript.

Voici une page sans CSP : <a href="http://localhost/csp/example%2012%20navigate-to/01_example12%20without%20csp.html">Lien</a>

Voici une page avec CSP : <a href="http://localhost/csp/example%2012%20navigate-to/02_example12%20with%20csp.html">Lien</a>

On remarque que cette directive n'est actuellement pas impl�ment�e par les navigateurs. Voir la documentation : <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/navigate-to">Lien</a>.

### Exemple 13 : Activation d'une sandbox

L'attribut sandbox active similaire � la sandbox activable sur les iframe.
Cela applique des restrictions sur les actions realisables par la page, tel que le blocage des popups, le chargement de plugins ou les scripts.
Cet attribut est compl�mentaires aux autres attributs vu pr�c�demment, car ils concernaient des ressources charg�es par la page.

Voici une page sans CSP : <a href="http://localhost/csp/example%2013%20sandbox/01_example13%20without%20csp.html">Lien</a>

Voici une page avec CSP sandbox : <a href="http://localhost/csp/example%2013%20sandbox/02_example13%20with%20csp.html">Lien</a>

Il est possible de d�finir des exceptions en utilisant ces valeurs: allow-forms allow-same-origin allow-scripts allow-popups, allow-modals, allow-orientation-lock, allow-pointer-lock, allow-presentation, allow-popups-to-escape-sandbox, and allow-top-navigation
