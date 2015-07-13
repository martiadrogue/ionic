
# Introducció

Entorn de per desenvolupar aplicacions mobils multiplataforma amb cordova, ionic
i angular. El sistema operatiu utilitzat és un Ubuntu 15.04.

- ionic 1.0.1
- cordova 5.1.1
- angular 1.3.13
- nodejs v0.12.7

Per conneixer la versió d'angular que s'està utilitzant pots executar la comanda
**angular.version** des de la consola. Per connexier la versió de la resta de
programes invoca la comanda *ionic info*.

# Instalació

install java, android, nodejs, apache ant, cordova i ionic framework.

Amb el nodejs instalat executa la comanda *npm* amb drets d'administrador per
instalar cordova i ionic.

```shell
npm install -g cordova ionic
```

Per crear un nou projecte utilitza la comanda *ionic*.

```shell
ionic start freshlypressed
```

Una vegada creat apareix un nou subdirectori en el working directory amb el nom
del projecte creat. Aquest projecte incorpora una configuració per defecte.

# Configuració

Obre el fitxer config.xml i canvia les propietats per les del projecte actual.

L'etiqueta **<name />** ja té el nom del projecte. Posa-li espais o les
majuscules que fassin falta.

Com que anem a desenvolupar una app des de zero, elimina tots els fitxers
predefinits del directory www/.

```shell
cd www/
rm index.html
yes | rm -r js/ templates/
```

I tornem a crear els fitxers buits.

```shell
cd www/
touch index.html
mkdir js/
touch js/app.js
mkdir templates/
```

# La app

Comencem treballant amb l'index.html i app.js.

Creem un templae HTML5. En aquest temple afegim les llibreries d'ionic i les
fulles d'estil per defecte.

```html
<link rel="stylesheet" href="lib/ionic/css/ionic.css">
<script src="lib/ionic/js/ionic.bundle.js"></script>
```

El script bundle conté AngularJS i Ionic.

També s'han d'afegir les els fitxers de la app,

```html
<link rel="stylesheet" href="css/style.css">
<script src="js/app.js"></script>
```

## HeaderBar

La primera cosa a afegir per l'aplicació és el header. Es pot trovar tota la
informació referent al header a la pàgina web oficial. Documetnació > CSS >
Header. Es tracta d'un component d'angular.

Per utilitzar aquesta action var necessitem la directiva ionHeaderBar. Podem
trovar més informació sobre aquesta directiva a la pàgina web oficial.
Dins Documentació > JavaScript > Headers/Footers > ion-header-bar.

Una vegada tenim la classe que anem a utilizar i la directiva que li correspon
la podem incloure al index.html.

```html
<ion-header-bar class="bar-balanced"></ion-header-bar>
```

Per el titol s'utilitza un tag h1 amb la classe title.

```html
<ion-header-bar class="bar-balanced">
  <h1 class="title">Freshly Pressed!</h1>
</ion-header-bar>
```

http://ionicframework.com/docs/components/#header
http://ionicframework.com/docs/api/directive/ionHeaderBar/

## Content

Per al panell destinat al contingut utiltem la directiva Content.

```html
<ion-content></ion-content>
```

I posem una mica de contingut per l'aplicació.

```html
<ion-content>
  <h1>Some content</h1>
</ion-content>
```

Per comprovar el resultat. Crea un servidor amb la commanda.

```html
ionic serve
```
Connectet amb el navegador a la URL http://127.0.0.1:8000/index.html

http://ionicframework.com/docs/api/directive/ionContent/
http://ionicframework.com/docs/guide/testing.html

## Anglular

Tenim la estructura pero segueix sense ser una aplicació d'angular. Perar
convertir el projecte en una aplicació d'angular cal editar el fitxer app.js.

Declarem l'aplicació com quasevol aplicació d'angular utilitzant la classe
angular i cridant el metode module, aquest metode utilitza la dependencia ionic
a més de demanar el nom de la app en que trevallarà.

```js
var myApp = angular.module('myApp', ['ionic']);
```

Per treballar amb dades necessitem crear un controlador i afegir-lo a la app.

```js
myApp.controller('MyAppCtrl', ['$scope', '$log', MyAppCtrl]);

function MyAppCtrl ($scope, $log) {
  // body...
}
```

El seguent pass és associar l'aplicació i model amb el fitxer html. Important
mencionar que al HTML s'utilitzen els noms definits en els a argumetns de les
funcions.

```html
<html lang="en" data-ng-app="myApp">
  <!-- some html -->
  <body data-ng-controller="MyAppCtrl">
    <!-- some html -->
  </body>
</html>
```

## Butto per Refrescar

El primer pass és implementar el boto a la la HeaderBar. També ens interessa que
el boto tigui una icona que l'identifiqui. Utilitza l'etiqueta **<i/>** amb les
classes corresponent.

```html
<ion-header-bar class="bar-balanced">
  <h1 class="title">Freshly Pressed!</h1>
  <button class="button" data-ng-click="refresh()">
    <i class="icon ion-android-refresh"></i>
  </button>
</ion-header-bar>
```

Des de la vista s'ha de bindejar el boto amb l'acció de refresc.

```html
<button class="button" data-ng-click="refresh()" />
```

Crea aquesta funció in vincula-la a l'objecte $scope.

```js
function MyAppCtrl ($scope, $log) {
  $scope.refresh = function() {
    alert('Button Pressed!');
  };
}
```

http://ionicframework.com/docs/components/#buttons
http://ionicons.com/

## Cards

En el template afegeix la card.

```html
<ion-content>
  <div class="list card">
    <div class="item item-avatar-left">
      <img src="img/ionic.png" alt="">
      <h2>Fred</h2>
      <p>Some text</p>
    </div>
    <div class="item item-body">
      <h1>Blog Title</h1>
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ipsum ducimus praesentium, ea inventore quae. Excepturi, aspernatur officiis molestiae quisquam perspiciatis iusto, temporibus non praesentium dolores.</p>
    </div>
  </div>
</ion-content>
```

http://ionicframework.com/docs/components/#cards

## Dades

Anem a crear la connecció amb l'APIREST. Per això necessitem un servei i la
dependencia $http. Cal tenir en compte que l'element primoridial per realitzar
la connecció és $http.

```js
myApp.service('FreshlyPressed', ['$http', '$log', FreshlyPressed]);
```

A continuació es crea el servei per recollir les dades JSON. Per això s'adjunta
una funció al servei per recullir el JSON per mitjar de $http.

```js
function FreshlyPressed ($http, $log) {
  this.getBlogs = function() {
    $http.jsonp("https://public-api.wordpress.com/rest/v1/freshly-pressed?callback=JSON_CALLBACK")
      .success(function(result) {
        $log.info(JSON.stringify(result.posts));
      });
  };
}
```

Una vegada tenim el servei creat s'ha de enviar al controlador, com a parametre
de la funció.

```js
myApp.controller('MyAppCtrl', ['$scope', '$log', 'FreshlyPressed', MyAppCtrl]);
```

Nota: si declares les funcions del servei amb l'alies this el controlador no les
veu. Una solució és utilitzar la clausula return, amb un objecte que contigui
les funcions. El problema persisteix.

```js
function FreshlyPressed ($http, $log) {
  return {
    getBlogs: function() {
      $http.jsonp("https://public-api.wordpress.com/rest/v1/freshly-pressed?callback=JSON_CALLBACK")
        .success(function(result) {
          $log.info(JSON.stringify(result.posts));
        });
    }
  };
}
```

Canviar l'alies this per el nom de la funció. Problema resolt.

```js
function FreshlyPressed ($http, $log) {
    FreshlyPressed.getBlogs = function() {
      $http.jsonp("https://public-api.wordpress.com/rest/v1/freshly-pressed?callback=JSON_CALLBACK")
        .success(function(result) {
          $log.info(JSON.stringify(result.posts));
        });
    };
}
```

Una vegada tenim les dades, els seguent pas és renderitzar-les per pantalla.

Definim un model al controlador dins la variable $scope, passem aquesta variable
al metode del Servei.

```js
function MyAppCtrl ($scope, $log) {
  $scope.posts = [];
  $scope.refresh = function() {
    FreshlyPressed.getBlogs($scope);
  };
}

function FreshlyPressed ($http, $log) {
    FreshlyPressed.getBlogs = function($scope) {
      $http.jsonp("https://public-api.wordpress.com/rest/v1/freshly-pressed?callback=JSON_CALLBACK")
        .success(function(result) {
          $scope.posts = result.posts;
        });
    };
}
```

I ara només queda vincular el model a la view. Per tal de mostrar el contingut
HTML com el que és s'ha d'utilitzar l'atrigut data-ng-bind-html.

```html
<ion-content>
  <div class="list card" data-ng-repeat="p in posts">
    <div class="item item-avatar-left">
      <img data-ng-src="{{ p.author.avatar_URL }}" alt="">
      <h2>{{ p.author.nice_name }}</h2>
      <p><a href="{{ p.author.URL }}">{{ p.author.URL }}</a></p>
    </div>
    <div class="item item-body">
      <h1>{{ p.title }}</h1>
      <div data-ng-bind-html="p.content"></div>
    </div>
  </div>
</ion-content>
```

## Pull on refresh

Per fer aquest pas cal utilitzar el tag **<ion-refresher />**, entre les
etiquetes header i content.

```html
<ion-refresher on-refresh="refresh();"></ion-refresher>
```

Aquesta activa una animació que no espara automaticament, s'ha de parar des
d'angular, quant el metode succes de la variable $http es cridi.

```js
$scope.broadcast("scroll.refreshComplete");
```


# Erros

Despres d'instalar l'ionic, en executar la comanda no em retorna feedback de cap
mena. I en crer un nou projecte no fa res.

Segueix els passos de l'enllaç per solucioanr el problema.

http://askubuntu.com/questions/538996/executing-certain-commands-do-absolutely-nothing

En executar la comanda ionic a seques demana actualitzar node.

Per actualitzar nodejs executa les seguents comandes.

```shell
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

http://askubuntu.com/questions/426750/how-can-i-update-my-nodejs-to-the-latest-version

A la consola de firefox apareix el missatge:
"The connection to ws://localhost:35729/livereload was interrupted while the
page was loading."

El missatge apareix en refrescar la vista. En montar el servidor no notifica cap
error. Prova de no refrescar la vista amb el navegador. El missatge no apareix i
l'app funciona.

# Altres

http://ionicframework.com/getting-started/
