---
id: tintin
title: Tintin Template
---

- [Introduction](#introduction)
  - [Configuration](#configuration)
  - [Affichage des données](#affichage-des-données)
    - [Affichage des données non échappées](#affichage-des-données-non-échappées)
  - [Ajouter un commentaire](#ajouter-un-commentaire)
  - [#if / #elseif ou #elif / #else](#if--elseif-ou-elif--else)
  - [#unless](#unless)
  - [#loop / #for / #while](#loop--for--while)
    - [L'utilisation de #loop](#lutilisation-de-loop)
    - [Les sucres syntaxiques #jump et #stop](#les-sucres-syntaxiques-jump-et-stop)
    - [L'utilisation de #for](#lutilisation-de-for)
    - [L'utilisation de #while](#lutilisation-de-while)
  - [Inclusion de fichier](#inclusion-de-fichier)
    - [Exemple d'inclusion](#exemple-dinclusion)
- [Héritage avec #extends, #block et #inject](#héritage-avec-extends-block-et-inject)
  - [Explication](#explication)
- [Directive personnelisée](#directive-personnelisée)
  - [Ajouter vos directive de la configuration](#ajouter-vos-directive-de-la-configuration)
  - [Exemple de création de directive](#exemple-de-création-de-directive)
  - [Utilisation des directives](#utilisation-des-directives)
  - [Compilation du template](#compilation-du-template)
  - [Sortie après compilation](#sortie-après-compilation)
- [IDE support](#ide-support)
  - [Comment installer Sublime Package Control](#comment-installer-sublime-package-control)
  - [Installer le package Tintin](#installer-le-package-tintin)

## Introduction

Tintin est un template PHP qui se veut très simple et extensible. Il peut être utilisable dans n'importe quel projet PHP. Par défaut #Tintin est le moteur de template de Bow Framework utilise.

### Configuration

Vous trouverez la configuration dans le fichier `config/view.php` et c'est dans le dossier `templates` que sont sauvegardés le fichier de template avec l'extension `.tintin.php`.

### Affichage des données

Vous pouvez afficher le contenu de la variable name de la manière suivante:

```css
Hello, {{ $name }}.
```

Bien entendu, vous n'êtes pas limité à afficher le contenu des variables transmises à la vue. Vous pouvez également faire écho aux résultats de toute fonction PHP. En fait, vous pouvez insérer n'importe quel code PHP dans une instruction echo Blade:

```css
Hello, {{ strtoupper($name) }}.
```

> Les instructions Tintin `{{ }}` sont automatiquement envoyées via la fonction PHP `htmlspecialchars` pour empêcher les attaques XSS.

#### Affichage des données non échappées

Par défaut, les instructions Tintin `{{ }}` sont automatiquement envoyées via la fonction PHP `htmlspecialchars` pour empêcher les attaques XSS. Si vous ne souhaitez pas que vos données soient protégées, vous pouvez utiliser la syntaxe suivante:

```css
Hello, {{{ $name }}}.
```

### Ajouter un commentaire

Cette clause `{# comments #}` permet d'ajouter un commentaire à votre code `tintin`.

### #if / #elseif ou #elif / #else

Ce sont les clauses qui permettent d'établir des branchements conditionnels comme dans la plupart des langages de programmation.

```css
<p>
#if ($name == 'tintin')
  {{ $name }}
#elseif ($name == 'template')
  {{ $name }}
#else
  {{ $name }}
#endif
</p>
```

> Vous pouvez utiliser `#elif` à la place de `#elseif`.

### #unless

Petite spécificité, le `#unless` quant à lui, il permet de faire une condition inverse du `#if`.
Pour faire simple, voici un exemple:

```css
#unless ($name == 'tintin')

// Egale à
#if (!($name == 'tintin'))
```

### #loop / #for / #while

Souvent vous pouvez être amener à faire des listes ou répétitions sur des éléments. Par exemple, afficher tout les utilisateurs de votre plateforme.

#### L'utilisation de #loop

Cette clause faire exactement l'action de `foreach`.

```css
#loop ($names as $name)
  Bonjour {{ $name }}
#endloop
```

Cette clause peux être aussi coupler avec tout autre clause telque `#if`. Voici un exemple rapide:

```css
#loop ($names as $name)
  #if ($name == 'tintin')
    Bonjour {{ $name }}
    #stop
  #endif
#endloop
```

Vous avez peut-être remarquer le `#stop` il permet de stoper l'éxécution de la boucle. Il y a aussi son conjoint le `#jump`, lui parcontre permet d'arrêter l'éxécution à son niveau et de lancer s'éxécution du prochain tour de la boucle.

#### Les sucres syntaxiques #jump et #stop

Souvent le dévéloppeur est amené à faire des conditions d'arrêt de la boucle `#loop` comme ceci:

```css
#loop ($names as $name)
  #if ($name == 'tintin')
    #stop
    // Ou
    #jump
  #endif
#endloop
```

Avec les sucres syntaxique, on peut réduire le code comme ceci:

```css
#loop ($names as $name)
  #stop($name == 'tintin')
  // Ou
  #jump($name == 'tintin')
#endloop
```

#### L'utilisation de #for

Cette clause faire exactement l'action de `for`.

```css
#for ($i = 0; $i < 10; $i++)
 // ..
#endfor
```

#### L'utilisation de #while

Cette clause faire exactement l'action de `while`.

```css
#while ($name != 'tintin')
 // ..
#endwhile
```

### Inclusion de fichier

Souvent lorsque vous dévéloppez votre code, vous êtes amener à subdiviser les vues de votre application pour être plus flexible et écrire moin de code.

`#include` permet d'include un autre fichier de template dans un autre.

```css
 #include('filename', data)
```

#### Exemple d'inclusion

Considérons le fichier `hello.tintin.php` suivant:

```css
Hello {{ $name }}
```

Utilisation:

```css
#include('hello', ['name' => 'Tintin'])
// => Hello Tintin
```

## Héritage avec #extends, #block et #inject

Comme tout bon système de template **tintin** support le partage de code entre fichier. Ceci permet de rendre votre code flexible et maintenable.

Considérérons le code **tintin** suivant:

```html
// le fichier `layout.tintin.php`
<!DOCTYPE html>
<html>
<head>
  <title>Hello, world</title>
</head>
<body>
  <h1>Page header</h1>
  <div id="page-content">
    #inject('content')
  </div>
  <p>Page footer</p>
</body>
</html>
```

Et aussi, on a un autre fichier qui hérite du code du fichier `layout.tintin.php`

```css
// le fichier se nomme `content.tintin.php`
#extends('layout')

#block('content')
  <p>This is the page content</p>
#endblock
```

### Explication

Le fichier `content.tintin.php` va hérité du code de `layout.tintin.php` et si vous rémarquez bien, dans le fichier `layout.tintin.php` on a la clause `#inject` qui a pour paramètre le nom du `#block` de `content.tintin.php` qui est `content`. Ce qui veut dire que le contenu du `#block` `content` sera remplacé par `#inject`. Ce qui donnéra à la fin ceci:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Hello, world</title>
</head>
<body>
  <h1>Page header</h1>
  <div id="page-content">
    <p>This is the page content</p>
  </div>
  <p>Page footer</p>
</body>
</html>
```

## Directive personnelisée

Tintin peut être étendu avec son systême de directive personnalisé, pour ce faire utilisé la méthode `directive`. Pour se faire nous avons créer un configuration et extendre la configuration native `\Tintin\Bow\TintinConfiguration::class`.

```bash
php bow add:configuration TintinConfiguration
```

### Ajouter vos directive de la configuration

Après avoir créé la configuration vous trouverez le fichier `app/Configurations/TintinConfiguration.php` qui va étendre la configuration par défaut de Tintin qui est `\Tintin\Bow\TintinConfiguration::class` et ensuite modifier la méthode `onRunning`.

```php
use Tintin\Tintin;

class TintinConfiguration extends \Tintin\Bow\TintinConfiguration
{
  /**
   * Add action in tintin
   *
   * @param Tintin $tintin
   */
  public function onRunning(Tintin $tintin)
  {
    $tintin->directive('super', function (array $attributes = []) {
      return "Super !";
    });
  }
}
```

Pour fin en beauté. remplacez la configuration par defaut de Tintin dans le `app/Kernel.php` par `App/Configurations/TintinConfiguration::class`.

Maintenant la directive `#super` est disponible et vous pouvez l'utiliser.

```php
  return $tintin->render('#super');
  // => Super !
```

### Exemple de création de directive

Ajoutons une directive simple. On l'appelera `#hello`

```php
$tintin->directive('hello', function (array $attributes = []) {
  return 'Hello, '. $attributes[0];
});

echo $tintin->render('#hello("Tintin")');
// => Hello, Tintin
```

Création de directive pour gérer un formulaires:

```php
$tintin->directive('input', function (array $attributes = []) {
  $attribute = $attributes[0];

  return '<input type="'.$attribute['type'].'" name="'.$attribute['name'].'" value="'.$attribute['value'].'" />';
});

$tintin->directive('textarea', function (array $attributes = []) {
  $attribute = $attributes[0];

  return '<textarea name="'.$attribute['name'].'">"'.$attribute['value'].'"</textarea>';
});

$tintin->directive('button', function (array $attributes = []) {
  $attribute = $attributes[0];

  return '<button type="'.$attribute['type'].'">'.$attribute['label'].'"</button>';
});

$tintin->directive('form', function (array $attributes = []) {
  $attribute = " ";
  
  if (isset($attributes[0])) {
    foreach ($attributes[0] as $key => $value) {
      $attribute .= $key . '="'.$value.'" ';
    }
  }

  return '<form "'.trim($attribute).'">';
});

$tintin->directive('endform', function (array $attributes = []) {
  return '</form>';
});
```

### Utilisation des directives

Pour utiliser ces directives, rien de plus simple. Ecrivez le nom de la directive précédé la par `#`. Ensuite si cette directive prend des paramètres, lancer la directive comme vous lancez les fonctions dans votre programme. Les paramètres seront régroupés dans la varibles `$attributes` dans l'ordre d'ajout.

```css
/** Fichier form.tintin.php **/
#form(['method' => 'post', "action" => "/posts", "enctype" => "multipart/form-data"])
  #input(["type" => "text", "value" => null, "name" => "name"])
  #textarea(["value" => null, "name" => "content"])
  #button(['type' => 'submit', 'label' => 'Add'])
#endform
```

### Compilation du template

La compilation se fait comme d'habitude, pour plus d'information sur la [compilation](#utilisation).

```php
echo $tintin->render('form');
```

### Sortie après compilation

```html
<form action="/posts" method="post" enctype="multipart/form-data">
  <input type="text" name="name" value="" />
  <textarea name="content"></textarea>
  <button type="submit">Add</button>
</form>
```

## IDE support

Tintin est supporté actuellement par [sublime text](https://www.sublimetext.com).

### Comment installer Sublime Package Control

Allez sur le [site](https://packagecontrol.io/installation) et suivez les instructions.

### Installer le package Tintin

- Recherchez **Bow Tintin** et installez-le/Téléchargez ou clonez ce référentiel dans [install-dir]/Packages/bow-tintin
- Redémarrez Sublime Text.
- Rouvrez n’importe quel fichier `.tintin` ou `.tintin.php`.
- Enjoy :smiley:
