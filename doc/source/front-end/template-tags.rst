==================================
Elements de gabarits personnalisés
==================================

Le dossier ``zds/utils/templatetags/`` contient un ensemble de tags et filtres personnalisés pouvant être utilisés dans les gabarits (*templates*),
`voir à ce sujet la documentation de Django <https://docs.djangoproject.com/fr/1.7/howto/custom-template-tags/>`_.

Pour les utiliser, vous devez "charger" le module dans le gabarit à l'aide du code suivant:

.. sourcecode:: html

   {% load nom_du_module %}

La majorité de ces modules proposent aussi des fonctions permettant les mêmes fonctionnalités depuis le reste du code Python.

Vous retrouverez si dessous une liste des modules et chacune des fonctions disponibles dans ceux-ci.

Le module ``append_to_get``
===========================

Ce module défini l'élément ``append_to_get``, qui permet de rajouter des paramètres à la requête ``GET`` courante. Par exemple, sur une page
``module/toto``, le code de gabarit suivant :

.. sourcecode:: html

    {% load append_to_get %}
    <a href="{% append_to_get key1=var1,key2=var2 %}">Mon lien</a>

produira le code suivant :

.. sourcecode:: html

    <a href="module/toto?key1=1&key2=2">Mon lien</a>

si le contenu de ``var1`` est ``1`` et le contenu de ``var2`` est ``2``.

Le module ``captureas``
=======================

Ce module défini l'élément ``captureas``, qui permet de demander d'effectuer le rendu d'un bloc de gabarit et de stocker son contenu dans
une variable. Ainsi le code suivant :

.. sourcecode:: html

    {% load captureas %}
    {% captureas var2 %}
    {% for i in 'xxxxxxxxxx' %}
    {{forloop.counter0}}
    {% endfor %}
    {% endcaptureas %}

ne produit rien en sortie mais affecte le résultat du bloc entre les éléments ``{% captureas var2 %}`` et
``{% endcaptureas %}``, soit ``0123456789``, dans la variable de gabarit ``var2``

Le module ``date``
==================

Plusieurs filtres sont disponibles dans ce module.

``format_date``
---------------

Ce filtre formate une date au format ``DateTime`` destiné à être affiché sur le site :

.. sourcecode:: html

    {% load date %}
    {{ date | format_date}}

``tooltip_date``
----------------

Ce filtre effectue la même chose que ``format_date`` mais à destination des ``tooltip``.

``humane_time``
---------------

Formate une date au format *Nombre de seconde depuis Epoch* en un élément lisible. Ainsi :

.. sourcecode:: html

    {% load date %}
    {{ date_epoch | humane_time}}

sera rendu :

.. sourcecode:: html

    01 Jan 1970, 01:00:42

Si le contenu de ``date_epoch`` etait de ``42``.

Le module ``email_obfuscator``
==============================

Ces filtres sont principalement fondés sur https://github.com/morninj/django-email-obfuscator.


``obfuscate``
-------------

L'adresse de courriel va être encodée avec des caractères ASCII pour la protéger des robots :


.. sourcecode:: html

    {% load email_obfuscator %}
    {{ 'your@email.com'|obfuscate }}


``obfuscate_mailto``
--------------------

Ce *templatetag* ajoute en plus un ``mailto``. Il prend un paramètre optionnel qui permet d'avoir un texte personnalisé dans
la balise ``<a>`` :

.. sourcecode:: html

    {% load email_obfuscator %}
    {{ 'your@email.com'|obfuscate_mailto:"my custom text" }}

Ce qui donnera :

.. sourcecode:: html

    <a href="&#109;&#97;&#105;&#108;&#116;&#111;&#58;&#121;&#111;&#117;&#114;&#64;&#101;&#109;&#97;&#105;&#108;&#46;&#99;&#111;&#109;">my custom text</a>


``obfuscate_mailto_top_subject``
--------------------------------

Identique sur le fonctionnement à ``obfuscate_mailto``, ce *templatetag* ajoute en plus un sujet (qui remplace le champ
pouvant être inséré entre les balises ``<a>`` et ``</a>``) ainsi que ``target="_top"``.

Il est utilisé sur la page « Contact ».

Exemple :

.. sourcecode:: html

    {% load email_obfuscator %}
    {{ 'association@zestedesavoir.com'|obfuscate_mailto_top_subject:"Contact communication" }}

Ce qui sera rendu de la manière suivante:

.. sourcecode:: html

    <a href="&#109;&#97;&#105;&#108;&#116;&#111;&#58;&#97;&#115;&#115;&#111;&#99;&#105;&#97;&#116;&#105;&#111;&#110;&#64;&#122;&#101;&#115;&#116;&#101;&#100;&#101;&#115;&#97;&#118;&#111;&#105;&#114;&#46;&#99;&#111;&#109;&#63;&#83;&#117;&#98;&#106;&#101;&#99;&#116;&#61;&#67;&#111;&#110;&#116;&#97;&#99;&#116;&#32;&#99;&#111;&#109;&#109;&#117;&#110;&#105;&#99;&#97;&#116;&#105;&#111;&#110;" target="_top">&#97;&#115;&#115;&#111;&#99;&#105;&#97;&#116;&#105;&#111;&#110;&#64;&#122;&#101;&#115;&#116;&#101;&#100;&#101;&#115;&#97;&#118;&#111;&#105;&#114;&#46;&#99;&#111;&#109;</a>

On conviendra du fait que c'est parfaitement illisible ;)

Le module ``emarkdown``
=======================

Ce module défini des filtres utilisés dans la transformation du markdown en HTML ou le traitement du markdown.

Markdown vers HTML
------------------

Il permet de rendre un texte Markdown en HTML. Il y a deux commandes :

- ``emarkdown`` pour une transformation classique ;
- ``emarkdown_inline`` pour une transformation uniquement des éléments *inline* et donc pas de blocs (c'est utilisé pour les
  signatures des membres).


Markdown vers Markdown
----------------------

Ces élements sont utilisés dans le cadre de la transformation du Markdown avant d'être traité par ``Pandoc`` lors de la
génération des fichiers PDF et EPUB des tutos :

- ``decale_header_1`` : Décale les titres de 1 niveau (un titre de niveau 1 devient un titre de niveau 2, etc.)
- ``decale_header_2`` : Décale les titres de 2 niveaux (un titre de niveau 1 devient un titre de niveau 3, etc.)
- ``decale_header_3`` : Décale les titres de 3 niveaux (un titre de niveau 1 devient un titre de niveau 4, etc.)

Le module ``interventions``
===========================

Les filtres de ce module sont utilisés pour récupérer et traiter la liste des interventions de l'utilisateur.

``is_read``
-----------

Employé sur un *topic*, renvoit si l'utilisateur courant a lu ou non le topic considéré. Par exemple, le code suivant mettra la classe "unread" si le *topic* n'as pas été lu par l'utilisateur :

.. sourcecode:: html

    {% load interventions %}
    <span class="{% if not topic|is_read %}unread{% endif %}">{{ topic.title}}</span>



``humane_delta``
----------------

Ce filtre renvoit le texte correspondant à une période donnée, si utilisé comme suis :

.. sourcecode:: html

    {% load interventions %}
    {{ period|humane_delta }}

En fonction de la valeur de ``period``, les chaines de caractères suivantes seront renvoyées :

- ``1`` : ``Aujourd'hui`` ;
- ``2`` : ``Hier`` ;
- ``3`` : ``Cette semaine`` ;
- ``4`` : ``Ce mois-ci`` ;
- ``5`` : ``Cette année``.


``followed_topics``
-------------------

Ce filtre renvoit la liste des *topics* suivis par l'utilisateur, sous la forme d'un dictionaire :

.. sourcecode:: html

    {% load interventions %}
    {% with follwedtopics=user|followed_topics %}
        {% for period, topics in follwedtopics.items %}
        ...
        {% endfor %}
    {% endwith %}

où ``period`` est un nombre au format attendu par ``humane_delta`` (entre 1 et 5, voir plus haut) et ``topics`` la liste des *topics* dont le dernier message est situé dans cette période de temps. Les *topics* sont des objets ``Topic`` (`voir le détail de son implémentation ici <../back-end-code/forum.html#zds.forum.models.Topic>`__).

``interventions_topics``
------------------------

Ce filtre récupère la liste des messages du forum ainsi que des commentaires de tutoriels et articles qui sont non-lus:

.. sourcecode:: html

    {% load interventions %}
    {% with unread_posts=user|interventions_topics %}
        {% for unread in unread_posts %}
        ...
        {% endfor %}
    {% endwith %}

Dans ce cas, la variable ``unread`` est un dictionnaire contentant 4 champs:

- ``unread.url`` donne l'URL du premier *post* non lu (ayant généré la notification) ;
- ``unread.author`` contient l'auteur de ce *post* ;
- ``unread.pubdate`` donne la date de ce *post* ;
- ``unread.title`` donne le titre du *topic*, article ou tutoriel dont est issus le post.


``interventions_privatetopics``
-------------------------------

Ce filtre récupère la liste des MPs non-lus :

.. sourcecode:: html

    {% load interventions %}
    {% with unread_posts=user|interventions_privatetopics %}
        {% for unread in unread_posts %}
        ...
        {% endfor %}
    {% endwith %}

Dans ce cas, ``topic`` est un objet de type ``PrivateTopic`` (`voir son implémentation ici <../back-end-code/private-message.html#zds.mp.models.PrivateTopic>`__)

``alerts_list``
---------------

Récupère la liste des alertes (si l'utilisateur possède les droits pour le faire) :

.. sourcecode:: html

    {% load interventions %}
    {% with alerts_list=user|alerts_list %}
        {% for alert in alerts_list.alerts %}
        ...
        {% endfor %}
    {% endwith %}

``alert_list`` est un dictionnaire contenant 2 champs:

- ``alerts`` : Les 10 alertes les plus récentes (détail ci-dessous) ;
- ``nb_alerts`` : Le nombre total d'alertes existantes.


``alerts`` énuméré souvent en ``alert`` est aussi un dictionnaire contenant 4 champs:

- ``alert.url`` donne l'URL du *post* ayant généré l'alerte ;
- ``alert.username`` contient le nom de l'auteur de l'alerte ;
- ``alert.pubdate`` donne la date à laquelle l'alerte à été faite ;
- ``alert.topic`` donne le texte d'alerte.

Le module ``model_name``
========================

Ce module défini l'élément ``model_name``. À partir des résultats d'une recherche, il permet de renvoyer de quel élément il s'agit (topic, post, article, ...).

.. sourcecode:: html

    {% load model_name %}
    {% model_name result.app_label result.model_name False %}

où ``result`` est le résultat d'une recherche, un objet de type ``SearchQuery`` (`voir la documentation de haystack à ce sujet (en) <http://django-haystack.readthedocs.org/en/latest/architecture_overview.html#searchquery>`__).

Le module ``profiles``
======================

``user``
--------

Pour un objet de type ``Profile`` (`voir son implémentation <../back-end-code/member.html#zds.member.models.Profile>`__), ce filtre récupère son objet ``User`` correspondant (`voir les informations sur cet objet dans la documentation de Django <https://docs.djangoproject.com/fr/1.8/topics/auth/default/#user-objects>`__).

Par exemple, le code suivant affichera le nom de l'utilisateur :

.. sourcecode:: html

    {% load profiles %}
    {% with user=profile|user %}
        Je suis {{ user.username }}
    {% endwith %}

``profile``
-----------

Fait l'opération inverse du filtre ``user`` : récupère un objet ``Profile`` à partir d'un objet ``User``.

Par exemple, le code suivant affichera un lien vers le profil de l'utilisateur :

.. sourcecode:: html

    {% load profiles %}
    {% with profile=user|profile %}
        <a href="{{ profile.get_absolute_url }}">{{ user.username }}</a>
    {% endwith %}


``state``
---------

À partir d'un objet ``User``, ce filtre récupère "l'état" de l'utilisateur. Par exemple, il peut être employé comme décris ci-dessous:

.. sourcecode:: html

    {% load profiles %}
    {% with user_state=user|state %}
    ...
    {% endwith %}


où ``user_state`` peut alors valoir une des 4 chaines de caractères suivantes, indiquant un état particulier, **ou rien** :

- ``STAFF`` : l'utilisateur est membre du staff ;
- ``LS`` : l'utilisateur est en mode lecture seule ;
- ``DOWN`` : l'utilisateur n'a pas encore validé son compte ;
- ``BAN`` : l'utilisateur est bani.

Ce *templatetag* est employé pour l'affichage des badges. Vous trouverez plus d'informations `dans la documentation des membres <../back-end/member.html>`__ concernant les différents états dans lesquels peut se trouver un utilisateur et ce qu'ils signifient.

``liked`` et ``disliked``
-------------------------

Ces filtres récupèrent respectivement si le message a recu des "+1" (*liked*) ou des "-1" (*disliked*) de la part d'un utilisateur donné.

Par exemple, le code suivant appliquera la classe "voted" si le message a reçu un "-1" de la part de l'utilisateur :

.. sourcecode:: html

    {% load profiles %}
    <button class="{% if profile_user|disliked:message.pk %}voted{% endif %}">
        {{ message.dislike }}
    </button>

où ``profile_user`` est le profil (objet ``Profile``) d'un utilisateur et ``message`` est un objet de type ``Post`` (qu'il s'agisse d'un *post* de forum, ou d'un commentaire dans un article ou tutoriel, dont les implémentations different légèrement). Ce *templatetag* est employé dans la partie affichant les réponses.

Le module ``repo_reader``
=========================

Il est employé pour afficher le *diff* des tutoriels et articles.

``repo_blob``
-------------

Ce filtre est basé sur l'utilisation de la librairie `GitPython (en) <https://github.com/gitpython-developers/GitPython>`__ pour lire un dépôt Git et en extraire des informations. Il récupère le contenu d'un fichier donné dans le dépôt Git. Par exemple, le code suivant lit un fichier et en récupère le texte :

.. sourcecode:: html

    {% load repo_reader %}
    {% with add_next=add.b_blob|repo_blob %}
    ...
    {% endwith %}

``diff_text``
-------------

Ce filtre affiche la différence entre deux chaines de caractères, en utilisant `difflib (en) <https://docs.python.org/2/library/difflib.html>`__. Ainsi, le code suivant affiche la différence entre ``maj_prev`` et ``maj_next`` :

.. sourcecode:: html

    {% load repo_reader %}
    {{ maj_prev|diff_text:maj_next|safe }}

À noter que le résultat de ce filtre est directement en HTML, d'où l'utilisation ici de ``safe``.

Le module ``roman``
===================

Défini le filtre ``roman``, qui transforme un nombre entier en chiffre romain, utilisé pour l'affichage du sommaire des tutoriels. Par exemple, le code suivant :

.. sourcecode:: html

    {% load roman %}
    {{ 453|roman }}

affichera ``CDLIII``, qui est bien la facon d'écrire 453 en chiffres romain.

Le module ``set``
=================

Ce module défini l'élément ``set``, permetant de définir de nouvelles variables, il est donc complémentaire au module ``captureas``.

Le code suivant permet de définir la variable ``var`` comme valant ``True`` :

.. sourcecode:: html

    {% load set %}
    {% set True as var %}

Bien entendu, il est possible d'assigner à une variable la valeur d'une autre. Soit la variable ``var``, définie de la manière suivante dans le code Python :

.. sourcecode:: python

    var = {'value': u'test'}
    # passage de la variable à l'affichage du gabarit
    # ...

Si on écrit le code suivant dans le gabarit :

.. sourcecode:: html

    {% load set %}
    {% set var.value as value %}
    {{ value }}

alors celle-ci affichera bien ``test``.

.. attention::

    Il n'est actuellement pas possible d'employer des filtres à l'intérieur de cet élément.


Le module ``topbar``
====================

Ce module est utilisé pour récupéré les catégories dans le but de les afficher dans `le menu <structure-du-site.html#le-menu>`__ et dans la liste des tutoriels et articles.

``top_categories``
------------------

Ce filtre récupère les forums, classés par catégorie.

.. sourcecode:: html

    {% with top=user|top_categories %}
        {% for title, forums in top.categories.items %}
        ...
        {% endfor %}
        {% for tag in top.tags %}
        ...
        {% endfor %}
    {% endwith %}

où,

- ``top.categories`` est un dictionaire contenant le nom de la catégorie (ici ``title``) et la liste des forums situés dans cette catégorie (ici ``forums``), c'est-à-dire une liste d'objets de type ``Forum`` (`voir le détail de l'implémentation de cet objet ici <../back-end-code/forum.html#zds.forum.models.Forum>`__).
- ``top.tags`` contient une liste des 5 *tags* les plus utilisés, qui sont des objets de type ``Tag`` (`voir le détail de l'implémentation de cet objet ici <../back-end-code/utils.html#zds.utils.models.Tag>`__).


``top_categories_tuto`` et ``top_categories_articles``
------------------------------------------------------

Ces filtres renvoient une liste des catégories utilisées dans les articles/tutoriels publiés.

Par exemple, pour les tutoriels, on retrouvera le code suivant:

.. sourcecode:: html

    {% with categories=user|top_categories_tuto %}
        {% for title, subcats in categories.items %}
            ...
        {% endfor %}
    {% endwith %}

où ``categories`` est un dictionnaire contenant le nom de la catégorie (ici ``title``) et une liste des sous-catégories correspondantes, c'est-à-dire une liste d'objets de type ``CategorySubCategory`` (`voir le détail de l'implémentation de cet objet ici <../back-end-code/utils.html#zds.utils.models.CategorySubCategory>`__).

``auth_forum``
--------------

Ce filtre renvoit si un forum donné, c'est-à-dire un objet de type ``Forum`` (`voir le détail de l'implémentation de cet objet ici <../back-end-code/forum.html#zds.forum.models.Forum>`__), est accessible pour un utilisateur donné.

Par exemple, le code suivant affichera le lien vers le forum uniquement si celui-ci est accessible pour l'utilisateur ``user`` :

.. sourcecode:: html

    {% if forum|auth_forum:user %}
        <a href="{{ forum.get_absolute_url }}">{{ forum.title }}</a>
    {% endif %}
