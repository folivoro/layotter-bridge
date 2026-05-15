<p align="center">
<a href="https://folivoro.com" target="_blank">
<img src="https://raw.githubusercontent.com/folivoro/art/refs/heads/main/sloth-logo.svg" alt="Sloth Logo" width="200" height="200" />
</a>
</p>
<p align="center">
<a href="https://packagist.org/packages/folivoro/sloth"><img src="https://img.shields.io/packagist/dt/folivoro/sloth" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/folivoro/sloth"><img src="https://img.shields.io/packagist/v/folivoro/sloth" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/folivoro/sloth"><img src="https://img.shields.io/packagist/l/folivoro/sloth" alt="License"></a>
<a href="https://github.com/folivoro/sloth/actions/workflows/ci.yml"><img src="https://github.com/folivoro/sloth/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
</p>

# Folivoro Layotter Bridge

Integrates the [Layotter](https://github.com/layotter/layotter) page builder plugin with [folivoro/sloth](https://github.com/folivoro/sloth).

Layotter is a WordPress page builder plugin originally developed by Dennis Hingst at [Quäntchen + Glück](https://www.qug.de/). It provides structured content editing through rows, columns, and elements — and is one of the core inspirations for Sloth itself.

> **Note**  
> Layotter must be installed and activated as a WordPress plugin for this integration to work.

---

## Installation

```bash
composer require folivoro/layotter-bridge
```

## Usage

### Enabling Layotter for a post type

Set `$layotter` on your Sloth model:

```php
class Project extends Model
{
    // Enable with default settings
    public static $layotter = true;

    // Enable with custom row layouts
    public static $layotter = [
        'allowed_row_layouts' => ['full', 'half', 'third'],
    ];

    // Disable (default)
    public static $layotter = false;
}
```

### Turning a module into a Layotter element

Set `$layotter` on your module with an ACF field group key and display settings:

```php
class HeroModule extends Module
{
    public static $layotter = [
        'field_group' => 'group_hero',
        'title'       => 'Hero',
        'description' => 'Full-width hero section',
        'icon'        => 'star',
    ];
}
```

The bridge registers the module as a Layotter element automatically. The ACF fields defined by `field_group` are passed to the module as view variables when rendered.

### Backend preview

By default, Layotter shows a table of field values as the backend preview. To use a custom Twig template instead, create a `_layotter` variant of your module template:

```
theme/views/module/hero.twig              # Frontend template
theme/views/module/hero_layotter.twig     # Backend preview template
```

### Custom views

Override the default markup for elements, columns, rows, or the entire post layout by placing Twig templates in your theme:

```
theme/views/layotter/element/default.twig
theme/views/layotter/column/default.twig
theme/views/layotter/row/default.twig
theme/views/layotter/post/default.twig
```

Each view receives the rendered content and contextual options (`post_options`, `row_options`, `col_options`, etc.).

### Column classes

Column classes default to a 12-column Bootstrap grid (`col-lg-1` through `col-lg-12`). Publish the config and override via `custom_classes`:

```bash
wp sloth vendor:publish --provider="Folivoro\LayotterBridge\LayotterBridgeServiceProvider" --tag=config
```

```php
// config/layotter.php
return [
    'row_layouts' => ['full', '1/2', '1/3', '2/3'],
    'custom_classes' => [
        '1/2' => 'col-md-6',
        '1/3' => 'col-md-4',
        '2/3' => 'col-md-8',
    ],
];
```

---

## Configuration reference

| Key | Type | Description |
|---|---|---|
| `row_layouts` | `array` | Restrict available row layouts globally |
| `custom_classes` | `array` | Override default Bootstrap column CSS classes |
| `prepare_fields` | `bool` | Whether to prepare ACF fields before rendering elements |
