# Filament Export

[![Latest Stable Version](http://poser.pugx.org/alperenersoy/filament-export/v)](https://packagist.org/packages/alperenersoy/filament-export) [![Total Downloads](http://poser.pugx.org/alperenersoy/filament-export/downloads)](https://packagist.org/packages/alperenersoy/filament-export) [![License](http://poser.pugx.org/alperenersoy/filament-export/license)](https://packagist.org/packages/alperenersoy/filament-export)

Customizable export and print functionality for Filament Admin Panel.

This package provides a bulk action and header action to export your filament tables easily.

![filament-export-3](https://user-images.githubusercontent.com/83382417/179013026-14ddd872-fedc-45d2-954a-1447005777bb.png)

## Requirements
- PHP 8
- [Filament 2.0](https://github.com/laravel-filament/filament)

### Dependencies
- [spatie/simple-excel](https://github.com/spatie/simple-excel)
- [barryvdh/laravel-dompdf](https://github.com/barryvdh/laravel-dompdf)

## Installation

```bash
composer require alperenersoy/filament-export
```

### Configuring for Standalone Table Builder (Experimental)

To use this package in a standalone table builder instead of Filament Admin Panel you need to follow these steps. Otherwise, some features such as print and preview may not work properly.

1. Import `filament-export.css` in your `/resources/app.css`

```css
@import '../../vendor/alperenersoy/filament-export/resources/css/filament-export.css';
```

2. Import `filament-export.js` in your `/resources/app.js`

```js
import '../../vendor/alperenersoy/filament-export/resources/js/filament-export.js';
```

3. Compile your assets

```bash
npm run dev
```

## Using

### Simple Usage

#### Bulk Action

You can export selected rows with the bulk action.

**Filament Admin Panel**

```php
$table->bulkActions([
    ...
    FilamentExportBulkAction::make('export')
    ...
]);
```

**Filament Table Builder**
    
```php
protected function getTableBulkActions(): array
{
    return [
        ...
        FilamentExportBulkAction::make('Export'),
        ...
    ];
}
```

#### Header Action

You can filter, search, sort and export your table with the header action.

**Filament Admin Panel**

```php
$table->headerActions([
    ...
    FilamentExportHeaderAction::make('export')
    ...
]);
```

**Filament Table Builder**
    
```php
protected function getTableHeaderActions(): array
{
    return [
        ...
        FilamentExportHeaderAction::make('Export'),
        ...
    ];
}
```

Since ButtonAction is deprecated you may use this action with ->button() instead.

### Full Usage

Both actions provide functions for configuration.

```php
FilamentExportBulkAction::make('export')
    ->fileName('My File') // Default file name
    ->timeFormat('m y d') // Default time format for naming exports
    ->defaultFormat('pdf') // xlsx, csv or pdf
    ->defaultPageOrientation('landscape') // Page orientation for pdf files. portrait or landscape
    ->directDownload() // Download directly without showing modal
    ->disableAdditionalColumns() // Disable additional columns input
    ->disableFilterColumns() // Disable filter columns input
    ->disableFileName() // Disable file name input
    ->disableFileNamePrefix() // Disable file name prefix
    ->disablePreview() // Disable export preview
    ->withHiddenColumns() //Show the columns which are toggled hidden
    ->fileNameFieldLabel('File Name') // Label for file name input
    ->formatFieldLabel('Format') // Label for format input
    ->pageOrientationFieldLabel('Page Orientation') // Label for page orientation input
    ->filterColumnsFieldLabel('filter columns') // Label for filter columns input
    ->additionalColumnsFieldLabel('Additional Columns') // Label for additional columns input
    ->additionalColumnsTitleFieldLabel('Title') // Label for additional columns' title input 
    ->additionalColumnsDefaultValueFieldLabel('Default Value') // Label for additional columns' default value input 
    ->additionalColumnsAddButtonLabel('Add Column') // Label for additional columns' add button 
```
You can also use default bulk action and header action functions to customize actions.

## Performance Tips for Large Datasets
- Since header action does server-side pagination you may choose header action over bulk action.
- You may disable preview.
- You may enable direct download.

## Configuration

Publish configuration

```bash
php artisan vendor:publish --provider="AlperenErsoy\FilamentExport\FilamentExportServiceProvider" --tag="config"
```

You can configure these settings:

```php
return [
    'default_format' => 'xlsx',
    'time_format' => 'M_d_Y-H_i',
    'default_page_orientation' => 'portrait',
    'disable_additional_columns' => false,
    'disable_filter_columns' => false,
    'disable_file_name' => false,
    'disable_preview' => false,
    'use_snappy' => false,
    'action_icon' => 'heroicon-o-document-download',
    'preview_icon' => 'heroicon-o-eye',
    'export_icon' => 'heroicon-o-download',
    'print_icon' => 'heroicon-o-printer',
    'cancel_icon' => 'heroicon-o-x-circle'
];
```

## Overriding Views

Publish views

```bash
php artisan vendor:publish --provider="AlperenErsoy\FilamentExport\FilamentExportServiceProvider" --tag="views"
```

This package has two views:

1. "components\table_view.blade.php" view is used for preview.

2. "pdf.blade.php" view is used as pdf export template.

3. "print.blade.php" view is used as print template.
   
### Using Custom Variables In Templates

```php
FilamentExportBulkAction::make('export')
    ->extraViewData([
        'myVariable' => 'My Variable'
    ])
```

or use closures

```php
FilamentExportHeaderAction::make('export')
    ->extraViewData(fn ($action) => [
        'recordCount' => $action->getRecords()->count()
    ])
```

Then use them in the templates as regular blade variables:

```blade
{{ $myVariable }}
```

## Using Snappy

By default, this package uses [dompdf](https://github.com/barryvdh/laravel-dompdf) as pdf generator.

If you want to use Snappy instead you need to install **barryvdh/laravel-snappy** to your project and configure it yourself. (See [barryvdh/laravel-snappy](https://github.com/barryvdh/laravel-snappy) for more information.)

To use snappy for PDF exports:

1. You can simply add ->snappy() to your actions.
   
```php
FilamentExportBulkAction::make('export')
    ->snappy()
```
or
```php
FilamentExportHeaderAction::make('export')
    ->snappy()
```
2. You can update the config file to use it as default.
```php
[
    ...
    'use_snappy' => true,
    ...
]
```
