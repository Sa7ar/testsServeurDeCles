testsServeurDeCles
==================

test pour ajouter une fonction de serveur de clés sur gmailcrypt

Branch master :
---------------
Pour le moment la branche master est mergée avec la branche downloadXHR.


Branche downloadXHR :
---------------------
Le plugin permet d'ajouter la clé publique associée à corentinhenry@gmail.com. Lors du premier click, ça ne fonctionne pas. Il faut effectuer un deuxième click. Le champ qui permet d'entrer l'adresse mail est inutilisé, l'adresse corentinhenry@gmail.com est entrée en dur dans le javascript.

Problèmes :
* Il faut effectuer deux clicks, la première fois, la requete est bloquée
* Pour des raisons pratiques de test, le champs "adresse email" est inutilisé (a feature, not a bug)
* ça fonctionne pour corentinhenry@gmail.com parce que une seule clé est renvoyée pas le serveur, mais si on essaie avec john.smith@gmail.com, ça ne fonctionne pas.


Branche downloadXHR2 :
----------------------
Même genre.


Branche downloadjquery :
----------------------
Afin de gérer le cas ou plusieurs clés sont renvoyées par le serveur, on n'utilise plus la fonction XMLHttpRequest, mais la fonction jquery $.ajax(). Cette branche n'est pas encore fonctionnelle.


Branche upload :
---------------
Gère l'upload de clés. Pas encore fonctionnel.
