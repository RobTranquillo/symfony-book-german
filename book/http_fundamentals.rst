.. index::
   single: Symfony Fundamentals

.. _symfony2-and-http-fundamentals:

Symfony und HTTP Grundlagen
===========================

Herzlichen Glückwunsch zur Entscheidung Symfony zu lernen, Du bist auf einem guten Weg dazu eine/ein produktive/r, rundum gebildete/r und berühmte/r Entwicklerin/Entwickler zu werden. Letzteres musst Du allerdings selber hinkriegen. Symfony wurde etwickelt um sich auf das Wesentliche zu konzentrieren: um tools zu entwicklen die dich schneller entwicklen und die Anwendungen robuster sein zu lassen. Symfony wurde aus den besten Ideen verschiedenster Technologien: die Werkzeuge und Konzepte die Du hier lernst basieren auf Erfahrungen und Leistungen von tausenden Menschen über viele Jahre. Du lerns also nicht nnur Symfony sondern auch die Grundlagen des Webs, bewährte Methoden der Entwicklung und wie viele tolle neue PHP Bibliotheken aus Symfony oder unabhängig davon. So, get ready.

Direkt zur Symfony Philosophie, dieses Kapitel beginnt mit der Erklärung des fudamentalsten Konzeptes von Webentwicklung: HTTP. Losgelöst von Deinem Vorwissen oder Sprachvorlieben ist dieses Kapitel ein **must-read**!


HTTP ist Einfach
----------------

HTTP (Hypertext Transfer Protocol für die Profis) ist eine Textsprache die es zwei Maschienen erlaubt miteinander zu kommunizieren. That's it! Zum Beispiel, beim ansehen des neuesten `xkcd`_ Comics, findet (ungefähr) folgende Unterhaltung statt:
([Server]-> Hallo, kannst Du mir bitte den heutigen Comic zeigen? -> [Server]
 [Server erstellt im Hintergrund die angefragte Seite]
 [Server]<- Na klar, hier ist die Seite <- [Server])

.. image:: /images/http-xkcd.png
   :align: center

Die tatsächlich verwendete Sprache ist ein bisschen formaler, aber es ist wirklich so einfach. HTTP ist dabei der Ausdruck um diese Text-basierende Sprache zu beschreiben. Gang egal was Du im Web entwickelst, das Ziel Deines Servers ist *immer* einfache Requests (Anfragen) zu verstehen und einfache Responses (Antworten) zurückzugeben.

Symfony baut sich vom Kern her entlang dieser Realität auf. Ob Du es wahrnimmst oder nicht, HTTP verwendest Du täglich. Und mit Symfony ernst Du, wie Du damit richtig umgehst.


.. index::
   single: HTTP; Request-response paradigm

Schritt 1: Der Client sendet einen Request
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Jede Unterhaltung im Netz startet mit einem *Request* (Aufruf). Der Request ist dabei eine simple Textnachricht die vom Client erstellt wird (zB. einem Browser, einer Smartphone App, etc) die in einem speziellen Format ist, das HTTP genannt wird. Der Client sendet seinen Request zum Server und wartet auf dessen Response (Antwort).

Wirf noch mal einen Blick auf den ersten Teil der Interaktion zwischen Browser und dem xkcd Webserver:

.. image:: /images/http-xkcd-request.png
   :align: center

In HTTP gesprochen, sieht dieser request ungefähr so aussehen:

.. code-block:: text

    GET / HTTP/1.1
    Host: xkcd.com
    Accept: text/html
    User-Agent: Mozilla/5.0 (Macintosh)

Diese einfache Nachricht kommunziert *alles* notwendige darüber, welche Ressource (Quelle) der Client genau will. Die erste Zeile eines HTTP Request ist die wichtigste und enthält zwei Dinge: die URI und die HTTP Methode.

Die URI (zB: ``/``, ``/contact``, etc) ist der eindeutige Bezeichner der gewünschten Ressource. Mit einer HTTP Methode (zB: ``GET``) wird definiert, was genau mit der Quelle gemacht werden soll. Die HTTP-Methoden sind definierte Kommandos, welche einige übliche Wege definieren mit denen über den Request auf die Ressource zugegriffen werden kann.


+----------+---------------------------------------+
| *GET*    | Erhalten der Ressource vom Server     |
+----------+---------------------------------------+
| *POST*   | Erzeugen von Ressourcen auf dem server|
+----------+---------------------------------------+
| *PUT*    | Erneuern der Ressource auf dem Server |
+----------+---------------------------------------+
| *DELETE* | Löschen der Ressource vom Server      |
+----------+---------------------------------------+


Sind diese verstanden, ist es leicht vorstellbar, wie ein HTTP Request aussehen könnte, der einen bestimmten Blogeintrag löschen soll. Zum Beispiel:


.. code-block:: text

    DELETE /blog/15 HTTP/1.1

.. note::

    Derzeit gibt es neun HTTP Methoden (auch "verbs" genannt) welche durch die HTTP Spezifikation definiert sind, aber viele davon sind nicht weit verbreitet. Die Realität ist, dass viele moderne Browser in Formularen nur ``POST`` und ``GET`` unterstützen. Viele werden jededoch in XMLHttpRequests unterstützt, sowie Symfonys router.



Nach der ersten Zeile folgen zwangsläufig weitere Zeilen im HTTP Request, die Request Headers (Kopfzeilen) genannt werden. Diese Headers können eine große Bandbreite an weiteren Informationen übertragen, zum Beispiel INformationen über den anfragenden ``Host`` (Computer) und die vom Client akzeptierten Antwortformate (``Accept``). Viele weitere Headers existieren und deren Beschreibung findet sich auf der entsprechenden Wikipediaseite `Liste_der_HTTP-Headerfelder`_.

Schritt 2: Der sendet eine Serverantwort
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Hat ein Server einen Request erhalten (received), ist damit genau bekannt, welche Ressource der Client möchte (dank der URI) und was der Client damit machen möchte (durch die HTTP Methoden). Beispielsweise im Fall eines GET Requests, holt der Server die Daten und gibt sie in einer HTTP Response (Antwort) wieder zurück an die Client. Um im Beispiel des xkcd Webservers zu bleiben:

.. image:: /images/http-xkcd.png
   :align: center

Übersetzt in HTTP, sieht die Antwort an den Browser etwa so aus:

.. code-block:: text

    HTTP/1.1 200 OK
    Date: Sat, 02 Apr 2011 21:05:05 GMT
    Server: lighttpd/1.4.19
    Content-Type: text/html

    <html>
      <!-- ... HTML for the xkcd comic -->
    </html>


Die HTTP Response (Antwort) entält die die angeforderte Ressource (den HTML Seite in dem Fall), sowie weitere Informationen über die Antwort. Die erste Zeile ist besonders wichtig, da sie den HTTP Response Status Code enthält (200 in diesem Fall). Der Status Code gibt dabei eine Art Gesamtergebnis an den Client zurück. War die Anfrage erfolgreich? Traten Fehler auf? Es existieren verschiedenste Stautus Codes die Erfolg oder Fehler angeben oder den CLient zu einer Handlung auffordern (zum Beispiel eine Weiterleitung auf eine andere Seite). Die ganze Liste der Status Codes findet sich auf der Wikipedia Seite `HTTP-Statuscode`_.

Genauso wie die Anfrage, enthält auch die HTTP Antwort weitere Informationen, die HTTP headers. Beispielsweise ist ein sehr wichtiger HTTP response header der ``Content-Type``. Der Inhalt der selben Ressource kann in unterschiedlichen Formaten zurück gegeben werden, wie HTML, XML oder JSON und der ``Content-Type`` header verwendet Internet Media Types wie zum Beispiel: ``text/html`` um dem Client mitzuteilen in welchem Format die Antwort ist. Mehr dazu findet sich hier: `list of common media types`_ in der Liste der üblichen Media-Types der IANA.

Es existieren viele weitere Headers, einige davon sind sehr mächtig. Zum Beispiel können bestimmte davon genutzt werden um ein mächtiges Caching System (Puffersystem) zu bauen.


Requests, Responses und Webdevelopment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Die Anfrage-Antwort Konversation ist der fundamentale Prozess, der alle Kommunikation im WWW antreibt. Und weil der Prozess so wichtig und mächtig ist, ist er unmissverständlich einfach.

Egal mit welcher Sprache und was für ein Applikation gebaut wird (Web, Mobile, JSON API) oder die verfolgte Entwicklungs Philsophie, am Ende **muss** die Anwendung jede Anfrage verstehen und eine zutreffende Antwort ausliefern.

Symfony wurde konstruiert um dieser Realität zu entsprechen.

.. tip::

    Um alles über die HTTP Spezifikation zu erfahren, ließ die Orginal RFC `HTTP 1.1 RFC`_ oder die `HTTP Bis`_. Ein großartiges Werkzeug zum prüfen der Request and Response Headers wärend des browsens ist die Firefox Erweiterung: `Live HTTP Headers`_.

.. index::
   single: Symfony Fundamentals; Requests and responses


Requests und Responses in PHP
-----------------------------

Aber wie wird mit einem Request umgegangen oder eine Response erzeugt, mit PHP? Tatsache ist, dass PHP den Prozess ein wenig vereinfacht::

    $uri = $_SERVER['REQUEST_URI'];
    $foo = $_GET['foo'];

    header('Content-Type: text/html');
    echo 'Die angefragte URI ist: '.$uri;
    echo 'Der Wert des "foo"-Parameters ist: '.$foo;


So seltsam es klingt, aber diese kleine Awendung nimmt sich faktisch Informationen vom HTTP Request um daraus eine HTTP Response zu erzeugen. Anstatt das die rohe HTTP Request Message analysiert werden muss, erstellt PHP Superglobals (Super Globale Variablen) wie ``$_SERVER`` und ``$_GET``, welche alle Informationen des Requests enthalten. Ähnlich dazu, anstatt HTTP-Formatierte Textanworten zurück zu geben, kann die Funktion ``header()``verwendet werden die um Response Headers zu erzeugen um dann einfach den aktuellen Inhalt auszugeben, der dadurch zum Content der Response Message wird. PHP erzeugt hiermit eine valide HTTP Response und übergibt sie zurück an den Client.

.. code-block:: text

    HTTP/1.1 200 OK
    Date: Sat, 03 Apr 2011 02:14:33 GMT
    Server: Apache/2.2.17 (Unix)
    Content-Type: text/html

    The URI requested is: /testing?foo=symfony
    The value of the "foo" parameter is: symfony

Requests und Responses in Symfony
---------------------------------

Symfony bietet eine Alternative zu dem rohen PHP Ansatz, über zwei Klassen welche es auf einfachem Wege erlauben mit HTTP Request und Response zu interagieren. Die Klasse :class:`Symfony\\Component\\HttpFoundation\\Request` ist einfach eine Objekt-Orientierte Repräsentation der HTTP Request Message. Alle Informationen des Requests sind damit an der Hand::

    use Symfony\Component\HttpFoundation\Request;

    $request = Request::createFromGlobals();

    // the URI being requested (e.g. /about) minus any query parameters
    $request->getPathInfo();

    // retrieve GET and POST variables respectively
    $request->query->get('foo');
    $request->request->get('bar', 'default value if bar does not exist');

    // retrieve SERVER variables
    $request->server->get('HTTP_HOST');

    // retrieves an instance of UploadedFile identified by foo
    $request->files->get('foo');

    // retrieve a COOKIE value
    $request->cookies->get('PHPSESSID');

    // retrieve an HTTP request header, with normalized, lowercase keys
    $request->headers->get('host');
    $request->headers->get('content_type');

    $request->getMethod();    // GET, POST, PUT, DELETE, HEAD
    $request->getLanguages(); // an array of languages the client accepts

Und zusätzlich werden durch die ``Request`` Klasse im Hintergrund automatisch viele Dinge erledigt. Zum Beispiel prüft die ``isSecure()`` Methode alle *drei* Faktoren die angeben ob eine gesicherte Verbindung (HTTPS) vorliegt oder nicht.

.. sidebar:: ParameterBags und Request Attribute

    Wie oben zu sehen ist, sind die Variablen ``$_GET`` und ``$_POST`` über die öffentlichen ``query`` and ``request`` Eigenschaften gleichermaßen zugreifbar. Jedes dieser Objekte ist eine :class:`Symfony\\Component\\HttpFoundation\\ParameterBag` Klasse, welche Methoden wie :method:`Symfony\\Component\\HttpFoundation\\ParameterBag::get`, :method:`Symfony\\Component\\HttpFoundation\\ParameterBag::has`, :method:`Symfony\\Component\\HttpFoundation\\ParameterBag::all` und weitere enthält. Sogar jede im Beispiel verwendete öffentliche Eigenschaft ist eine Instanz des ParameterBag.

    .. _book-fundamentals-attributes:

    Die Request Klasse hat ebenso eine ``attributes`` Eigenschaft, die spezielle Daten über den inneren Ablauf der Applikation bereit hält. Für das Symfony Framework (Gerüst) selber, enhält ``attributes`` die Werte, die die zutreffenden Route zurück gibt, wie ``_controller`` zum Beispiel, oder ``id`` (für ``{id}`` Platzhalter) und den Namen der der zutreffenden Route (``_route``). Die ``attributes`` Eigenschaft existiert ausschließlich als Ort an dem Kontext-spezifische Informationen einer Anfrage vorbereitet und gespeichert werden.

Symfony bringt auch eine Klasse ``Response`` (Antwort) mit, welche eine einfache PHP Repräsentation der HTTP Message ist. So können Anwendungen eine Objekt-orientierte Schnittstelle verwenden um die Antworten zu konstruieren, die an den Client erwiedert werden::

    use Symfony\Component\HttpFoundation\Response;

    $response = new Response();

    $response->setContent('<html><body><h1>Hello world!</h1></body></html>');
    $response->setStatusCode(Response::HTTP_OK);
    $response->headers->set('Content-Type', 'text/html');

    // prints the HTTP headers followed by the content
    $response->send();


Soll sonst kein Teil von Symfony verwendet werden, ermöglicht allein dieses Werkzeug auf Anfrageinformationen zu zugreifen und einen Objekt-orientierten Ansatz die Antwort dafür zu generieren. Bei aller weiterer Beschäftigung mit den mächtigen Funktionen von Symfony, sollte dann auch nie vergessen werden was das Ziel der Applikation ist, nämlich *eine Anfrage zu verstehen und eine entsprechende Antwort, basierend auf der Applikationslogik zu generieren*.

.. tip::
    ``Request`` und ``Response`` Klasse sind Teil einer eigenständigen Komponente von Symfony die HttpFoundation heißt. Diese kann vollständig unabhängig von Symfony verwendet werden und bietet Klassen an um Sessions (Sitzungen) und Datei-uploads zu handhaben.


Eine Reise vom Request zur Response
-----------------------------------

Wie bei HTTP selbst sind die ``Request`` und ``Response`` Objekte sehr einfach gehalten. Und der anstrengende Teil eine Anwendung zu bauen, ist der zwischen diesen beiden. Mit anderen Worten, die eigentliche Arbeit ist die Anfrage zu interpretieren und eine Antwort zu generieren.

Deine Anwendung wird vielleicht auch Dinge tun wie E-Mails senden, Formulardaten auswerten, etwas in Datenbanken schreiben, HTML Seiten zusammenstellen und Inhalte gegen Gefahren schützen. Doch wie lässt sich das alles schaffen und trozdem den Code gut organisiert und wartbar halten?

Symfony wurde erschaffen um diese Probleme für Dich zu lösen.


Der Front Controller
~~~~~~~~~~~~~~~~~~~~

Üblicherweise werden Applikationen so gebaut, dass jede "Seite" durch eine eigene physikaliche Datei repräsentiert wird.

.. code-block:: text

    index.php
    contact.php
    blog.php

Aber es gibt verschiedene Probleme mit diesem Ansatz, einschließlich der Unflexibilität der URL (Was zB., wenn ``blog.php`` zu ``news.php`` geändert werden soll, ohne das alle Links brechen?) und dem Fakt, dass jede Datei manuell eine Sammlung von Kerndateien einbindet um Sicherheit, Datenbankverbindung und konsistentes Aussehen der Seite sicherzustellen.

Eine viel bessere Lösung ist es einen :term:`front controller`: zu verwenden, dieser ist eine PHP Datei die jede Anfrage an deine Applikation entgegen nimmt. Zum Beispiel:


+------------------------+------------------------+
| ``/index.php``         | führt ``index.php`` aus|
+------------------------+------------------------+
| ``/index.php/contact`` | führt ``index.php`` aus|
+------------------------+------------------------+
| ``/index.php/blog``    | führt ``index.php`` aus|
+------------------------+------------------------+

.. tip::
    Wird ``mod_rewrite`` bei Apache verwendet (oder ein Äquivalent bei anderen Webservern), lassen sich URLs leicht bis auf ``/``, ``/contact`` und
    ``/blog`` herunter bereinigen.


So wird jetzt jede Anfrage auf exakt die gleiche Weise verarbeitet, anstatt das individuelle URLs verschiedene PHP Dateien zur Ausführung bringen, wird *immer* der Front Controller ausgeführt, welcher intern das Routing von unterschiedlichen URLs zu den verschiedenen Programmteilen der Applikation vornimmt. Das löst beide Probleme mit dem orginalen Ansatz. Fast alle modernen WebApps machen es darum auch so - inklusive Apps wie WordPress.

Immer aufgeräumt bleiben
~~~~~~~~~~~~~~~~~~~~~~~~

Innerhalb des Front Controllers musst Du herausfinden welcher Programmcode ausgeführt werden sollte und was die zurück zu gegeben Inhalte sind. Um das zu ermitteln muss die eintreffende URI untersucht und die entsprechenden Programmteile dafür ausgeführt werden. IM einfachsten fall könnte das so aussehen::

    // index.php
    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Component\HttpFoundation\Response;

    $request = Request::createFromGlobals();
    $path = $request->getPathInfo(); // the URI path being requested

    if (in_array($path, array('', '/'))) {
        $response = new Response('Welcome to the homepage.');
    } elseif ('/contact' === $path) {
        $response = new Response('Contact us');
    } else {
        $response = new Response('Page not found.', Response::HTTP_NOT_FOUND);
    }
    $response->send();

Manchmal kann das ziemlich kompliziert werden, zum Glück ist Symfony genau dafür entwickelt worden.

Der Symfony Application Flow (Anwendungsfluß)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Wird Symfony die Behandlung jeder Anfrage überlassen, wird das Leben viel leichter. Symfony arbeitet bei jeder Anfrage das selbe einfache Schema ab:

.. _request-flow-figure:

.. figure:: /images/request-flow.png
   :align: center
   :alt: Symfony request flow

   Eingehende Anfragen werden vom Routing interpretiert und zum Controller gegeben der die ``Response`` (Antwort)-Objekte zurück gibt.

Jede "Seite" Deiner Anwendung ist in einer Routing-konfigurationsdatei definiert, die verschiedene URL-Aufrufe zu entsprechenden PHP Funktionen umsetzt. Diese PHP Funktionen, bei uns :term:`controller` genannt, haben den Job die Informationen aus dem Aufruf zu benutzen und -zusammen mit weiteren Tools die Symfony bereitstellt- ein ``Response``-Objekt zu generieren und zurück zu geben. Mit anderen Worten, der Controller ist wohin *Dein* Code geht: es ist wo die Anfrage ausgewertet und eine Antwort erstellt wird.

So einfach ist das!

It's that easy! Rückblick:

* Jede Anfrage wird durch das Front Controller File angenommen.

* Das Routingsystem bestimmt welche PHP Funktion ausgeführt werden sollte, basierend auf den Informationen aus der Anfrage und der eigenen Routing-Konfiguration.

* Die passende PHP Funktion ist die, welche das passende ``Response``-Objekt, generiert und zurückgibt.

Ein Symfony Request in Aktion
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ohne zu tief einzusteigen, hier einmal der Prozess in Aktion. Angenommen, es soll eine Seite ``/contact`` in unserer Symfony-Anwendung hinzugefügt werden. Dazu wird im ersten Schritt ein Eintrag ``/contact`` in der Routing-Konfigurations-Datei hinzugefügt.

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        contact:
            path:     /contact
            defaults: { _controller: AppBundle:Main:contact }

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>
        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd">

            <route id="contact" path="/contact">
                <default key="_controller">AppBundle:Main:contact</default>
            </route>
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\Route;
        use Symfony\Component\Routing\RouteCollection;

        $collection = new RouteCollection();
        $collection->add('contact', new Route('/contact', array(
            '_controller' => 'AppBundle:Main:contact',
        )));

        return $collection;

Wird nun die Seite ``/contact`` besucht, greift diese Route und der angegebene Controller wird ausgeführt. Wie später im Kapitel :doc:`routing chapter </book/routing>` erklärt wird, ist die Angabe ``AppBundle:Main:contact``  ein Kurzform-Befehl, welcher auf unserer speziellen PHP Methode ``contactAction`` zeigt und die in der Klasse``MainController`` liegt::

    // src/AppBundle/Controller/MainController.php
    namespace AppBundle\Controller;

    use Symfony\Component\HttpFoundation\Response;

    class MainController
    {
        public function contactAction()
        {
            return new Response('<h1>Contact us!</h1>');
        }
    }

In diesem einfachen Beispiel erzeugt der Controller ein :class:`Symfony\\Component\\HttpFoundation\\Response`-Objekt, dessen HTML Inhalt ``<h1>Contact us!</h1>`` ist. Im Kapitel über Controller: :doc:`controller chapter </book/controller>` erfährst Du wie Controller Templates rendern (Vorlagen erstellen), diese ermöglichen, das der darstellende Code (also alles das HTML ausgibt) in einer seperaten Template-Datei liegt. Das entlastet den Controller in dem sich auf das wesentliche konzentriert werden kann: mit der Datenbank zu kommunizieren, empfangene Daten verarbeiten oder E-Mails verschicken.

.. _symfony2-build-your-app-not-your-tools:

Symfony: Entwickle Deine App, nicht Deine Tools
-----------------------------------------------

Jetzt ist klar, dass das Ziel jeder App ist eingehende Anfragen zu verstehen und entsprechende Antworten zu generieren. Wächst die Anwendung, wird es schwieriger den Code strukturiert und wartbar zu halten. Weiterhin treten wieder und wieder die selben komplexen Aufgaben auf: Dinge in die Datenbank eingepflegt, Vorlagen rendern und wiederverwenden, Formulareingaben auswerten, E-Mails verschicken, Benutzereingaben kontrollieren, Sicherheitbelange.

Die gute Nachricht ist, dass keines der Probleme unlösbar ist. Mit Symfony steht ein Framework bereit, welches Dir hilft Deine Applikation zu bauen und nicht erst die Werkzeuge. Symfony bindet Dir auch keinen KLotz ans Bein: es kann immer das ganze Framework genutzt werden oder nur einzelne Bausteine selbst.

.. index::
   single: Symfony Components

.. _standalone-tools-the-symfony2-components:

Einzelwerkzeuge: Die Symfony *Components* (Komponenten)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Was *ist* Symfony? Zuerst ist Symfony eine Sammlung von über 20 verschiedenen Bibliotheken, die in jedem PHP-Projekt verwendet werden können. Diese, die *Symfony Components* (Symfony Komponenten) gennant, enthalten viel Praktisches für fast jede Situation und egal wie weit Dein Projekt bereits entwickelt ist.

Um nur mal einige zu nennen:

:doc:`HttpFoundation </components/http_foundation/introduction>`
    Enhält die Klassen ``Request`` und ``Response``, sowie weitere Klassen um Sessions (Nutzersitzungen) und Datei-Uploads zu handhaben.

:doc:`Routing </components/routing/introduction>`
    Mächtige und schnelle Ablaufplanung, welche es erlaubt spezifische URIs (zB: ``/contact``) mit Informationen darüber zu verknüpfen wie die Anfrage zu beantworten ist (zB., führe Funktion: ``contactAction()`` aus).

:doc:`Form </components/form/introduction>`
    Ein vollständiges und dennoch flexibles Framework um Formulare zu generieren und Formulareingaben zu handhaben.

`Validator`_
    Ein System zur Regelerstellung für Daten und deren Validation ob Benutzerdaten diesen folgen oder nicht.

:doc:`Templating </components/templating/introduction>`
    Ein Werkzeugkasten zum rendern von Templates (Vorlagen), für Vorlagenvererbung (zB: welches Layout eine Vorlage erhält) und weitere typische Aufgaben mit Templates.

:doc:`Security </components/security/introduction>`
    Eine mächtige Bibliothek für alle Aufgaben der Sicherheit innerhalb einer Applikation.

:doc:`Translation </components/translation/introduction>`
    Ein Framework Lokalisierung, also die Behandlung von Übersetzungen der Applikation.

Jede dieser Komponenten ist alleinstehend und kann in *jedem* PHP-Projekt verwendung finden, egal ob Symfony verwendet wird oder nicht. Jede ist so gemacht, dass sie genutzt werden kann wenn sie gebraucht wird und ersetzt, wenn notwendig.

.. _the-full-solution-the-symfony2-framework:

Das Komplettangebot: Das Symfony *Framework*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Also, was *ist* das Symfony *Framework*? Das *Symfony Framework* ist
eine PHP-Bibliothek, die zwei verschiedene Aufgaben erfüllt:

#. Anbieten einer Auswahl von Komponenten (die Symfony Components) und Drittanbieter-Bibliotheken (zB: `Swift Mailer`_ für den Mailversand);

#. Anbieten einer vernünftigen Konfiguration und einer verbindenden Bibliothek, die alle Teile zusammenschnürt.

Ziel von Symfony ist es, viele unabhängige Werkzeuge in sich zu integrieren um Enwicklerinnen und Entwicklern eine konsistente Arbeitserfahrung zu geben. Sogar das Framework selbst ist ein Symfony-Paket, das als (zB: Plugin) konfiguriert oder komplett ersetzt werden kann.

Symfony bietet eine mächtige Sammlung an Werkzeugen an, um schnell Webapplikationen zu entwickeln ohne diesen zur Last zu fallen. Normalanwender können mit Symfony schnell mit der Entwicklung eigener Anwendungen starten in dem sie eine Distribution nutzen, diese bringen ein Projekt-Grundgerüst mt sinvollen Standardwerten mit. Für Profis, ..gibt es keine Grenzen.

.. _`xkcd`: http://xkcd.com/
.. _`HTTP 1.1 RFC`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
.. _`HTTP Bis`: http://datatracker.ietf.org/wg/httpbis/
.. _`Live HTTP Headers`: https://addons.mozilla.org/en-US/firefox/addon/live-http-headers/
.. _`List of HTTP status codes`: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
.. _`List of HTTP header fields`: https://en.wikipedia.org/wiki/List_of_HTTP_header_fields
.. _`list of common media types`: https://www.iana.org/assignments/media-types/media-types.xhtml
.. _`Validator`: https://github.com/symfony/validator
.. _`Swift Mailer`: http://swiftmailer.org/
