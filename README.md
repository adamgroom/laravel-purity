<p align="center">
  <img src="https://github.com/abbasudo/laravel-purity/raw/master/art/purity-logo.png" alt="Social Card of Laravel Purity">
  <h1 align="center">Elegant way to filter and sort queries in Laravel</h1>
</p>

[![Tests](https://github.com/abbasudo/laravel-purity/actions/workflows/tests.yml/badge.svg)](https://github.com/abbasudo/laravel-purity/actions/workflows/tests.yml)
[![License](http://poser.pugx.org/abbasudo/laravel-purity/license)](https://github.com/abbasudo/laravel-purity)
[![Latest Unstable Version](http://poser.pugx.org/abbasudo/laravel-purity/v)](https://packagist.org/packages/abbasudo/laravel-purity)
[![PHP Version Require](http://poser.pugx.org/abbasudo/laravel-purity/require/php)](https://packagist.org/packages/abbasudo/laravel-purity)
[![StyleCI](https://github.styleci.io/repos/603931433/shield)](https://packagist.org/packages/abbasudo/laravel-purity)
<!-- [![visitors](https://visitor-badge.glitch.me/badge?page_id=abbasudo.laravel-purity)](https://packagist.org/packages/abbasudo/laravel-purity) -->

> **Note**
> If you are a front-end developer and want to make queries in an API that uses this package head to the [queries](#queries-and-javascript-examples) section

> **Note**
> Version 3 changed `$filterFields` read more at [upgrade guide](#upgrade-guide) section


Laravel Purity is an elegant and efficient filtering and sorting package for Laravel, designed to simplify complex data filtering and sorting logic for eloquent queries. By simply adding `filter()` to your Eloquent query, you can add the ability for frontend users to apply filters based on URL query string parameters like a breeze.

Features :
- Livewire support (added in v2)
- Rename and restrict fields (added in v2)
- Various filter methods
- Simple installation and usage
- Filter by relation columns
- Custom filters
- Multi-column sort

Laravel Purity is not only developer-friendly but also front-end developer-friendly. Frontend developers can effortlessly use filtering and sorting of the APIs by using the popular [JavaScript qs](https://www.npmjs.com/package/qs) package.

The way this package handles filters is inspired by strapi's [filter](https://docs.strapi.io/dev-docs/api/rest/filters-locale-publication#filtering) and [sort](https://docs.strapi.io/dev-docs/api/rest/sort-pagination#sorting) functionality.

## Table of Contents
- [Tutorials](#tutorials)
  * [Video](#video)
  * [Articles](#articles)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
  * [Filters](#filters)
  * [Sort](#sort)
    + [Sort Basics](#sort-basics)
    + [Sort by Relationships](#sort-by-relationships)
- [Advanced Usage](#advanced-usage)
  * [Allowed Fields](#allowed-fields)
    + [Overwrite Allowed Fields](#overwrite-allowed-fields)
  * [Rename Fields](#rename-fields)
    + [Rename Filter Fields](#rename-filter-fields)
    + [Rename Sort Fields](#rename-sort-fields)
    + [Overwrite Renamed Fields](#overwrite-renamed-fields)
  * [Restrict Filters](#restrict-filters)
  * [Restrict filters by field](#restrict-filters-by-field)
  * [Changing Params Source](#changing-params-source)
  * [Livewire](#livewire)
  * [Custom Filters](#custom-filters)
  * [Silent Exceptions](#silent-exceptions)
  * [Sort null values last](#sort-null-values-last)
- [Queries and javascript examples](#queries-and-javascript-examples)
  * [Available Filters](#available-filters)
    + [Simple Filtering](#simple-filtering)
    + [Complex Filtering](#complex-filtering)
    + [Deep Filtering](#deep-filtering)
  * [Apply Sort](#apply-sort)
    + [Apply Basic Usage](#apply-basic-sorting)
    + [Apply Sort by Relationships](#apply-sort-by-relationships)
- [Upgrade Guide](#upgrade-guide)
  * [Version 3](#version-3)
  * [Version 2](#version-2)
- [License](#license)
- [Security](#security)

## Tutorials
### Video
[![youtube](https://user-images.githubusercontent.com/86796762/227452155-3644f431-a8ce-41bc-ad4b-95383a3209fa.png)](https://youtu.be/nvCTEKvRdec)
### Articles
- [Filter API Responses with Laravel Purity](https://laravel-news.com/filter-api-responses-with-laravel-purity)
- [Filter and Sort in Laravel](https://medium.com/@ahnabshahin/filter-and-sort-in-laravel-07bbb964f32d)
- [Filter better with Laravel Purity](https://dev.to/giuliano1993/mondev-newsletter-filter-better-with-laravel-purity-55c5)
- [The correct way of adding filters to Laravel](https://medium.com/@abbasudo/the-correct-way-of-adding-filters-to-laravel-10-bb9957c2ddc6)
- [Add filter to your laravel app](https://dev.to/abbasudo/add-filter-to-your-laravel-10-app-4f5f)
- [Enable filtering queries in your laravel 10 app with ease](https://medium.com/@abbasudo/enable-filtering-in-your-laravel-app-with-ease-a63f79b5e452)


## Installation
Install the package via composer by this command:
```sh
composer require abbasudo/laravel-purity 
```
Get configs (`configs/purity.php`) file to customize the package's behavior by this command:
```sh
php artisan vendor:publish --tag=purity 
```
## Basic Usage
### Filters
Add `Filterable` trait to your model to get filters functionalities.

```php
use Abbasudo\Purity\Traits\Filterable;

class Post extends Model
{
    use Filterable;
    
    //
}
```

Now add `filter()` to your model eloquent query in the controller.

```php
use App\Models\Post;

class PostController extends Controller
{
    public function index()
    {
        return Post::filter()->get();
    }
}
```

By default, it gives access to all filters available. here is the list of [available filters](#available-filters). if you want to explicitly specify which filters to use in this call head to [restrict filters](#restrict-filters) section.

### Sort

#### Sort Basics
Add `Sortable` trait to your model to get sorts functionalities.

```php
use Abbasudo\Purity\Traits\Sortable;

class Post extends Model
{
    use Sortable;
    
    //
}
```

Now add `sort()` to your eloquent query in the controller.

```php
use App\Models\Post;

class PostController extends Controller
{
    public function index()
    {
        return Post::sort()->get();
    }
}
```

#### Sort by Relationships
The following relationship types are supported.
- One to One
- One to Many
- Many to Many

Return type of the relationship mandatory as below in order to sort by relationships.

```php
use Illuminate\Database\Eloquent\Relations\HasMany;

class Post extends Model
{
    use Sortable;
    
    public function tags(): HasMany // This is mandatory
    {
        return $this->hasMany(Tag::class);
    }
}
```

Now sort can be applied as instructed in [apply sort](#apply-sort).

## Advanced Usage
### Allowed Fields
By default, purity allows every database column and all model relations to be filtered. you can overwrite the allowed columns as follows:

```php
// App\Models\User

protected $filterFields = [
  'email',
  'mobile',
  'posts', // relation
];
    
protected $sortFields = [
  'name',
  'mobile',
];
```
any field other than email, mobile, or posts will be rejected when filtering.
#### Overwrite Allowed Fields
to overwrite allowed fields in the controller add `filterFields` or `sortFields` before calling filter or sort method.
```php
Post::filterFields('title', 'created_at')->filter()->get();

Post::sortFields('created_at', 'updated_at')->sort()->get();
```
> **Note**
> filterFields and sortFields will overwrite fields defined in the model.

### Rename Fields
#### Rename Filter Fields
To rename filter fields simply add a value to fields defined in `$renamedFilterFields`.
```php
// App\Models\User

// ?filter[phone][$eq]=0000000000
protected $renamedFilterFields = [
  'mobile' => 'phone', // Actual database column is mobile
  'posts'  => 'writing', // actual relation is posts
];

```
The client should send phone in order to filter by mobile column in database.
#### Rename Sort Fields
To rename sort fields simply add a value to defined in `$sortFields`
```php
// ?sort=phone
protected $sortFields = [
  'name',
  'mobile' => 'phone', // Actual database column is mobile
];
```
The client should send phone in order to sort by mobile column in database.
#### Overwrite Renamed Fields
To overwrite renamed fields in the controller you pass renamed fields to `rebamedFilterFields` and `sortFields`.
```php
Post::renamedFilterFields(['created_at' => 'published_date'])->filter()->get();

Post::sortFields([
    'created_at' => 'published_date',
    'updated_at'
  ])->sort()->get();
```
> **Note**
> SortFields will overwrite fields defined in the model.

### Restrict Filters

purity validates allowed filters in the following order of priority:
- Filters specified in the `filters` configuration in the `configs/purity.php` file.
  
```php
// configs/purity.php
'filters' => [
  EqualFilter::class,
  InFilter::class,
],
```

- Filters declared in the `$filters` variable in the model.
  
note that, $filters will overwrite configs filters.

```php
// App\Models\Post

private array $filters = [
  '$eq',
  '$in',
];
    
// or
    
private array $filters = [
  EqualFilter::class,
  InFilter::class,
];
```

- Filters passed as an array to the `filterBy()` function.

note that, `filterBy` will overwrite all other defined filters.

```php
Post::filterBy('$eq', '$in')->filter()->get();
// or
Post::filterBy(EqualFilter::class, InFilter::class)->filter()->get();
```
### Restrict filters by field
There are three available options for your convenience. They take priority respectively.
- **Option 1 : Define restricted filters inside `$filterFields` property, as shown below**
```php
$filterFields = [
  'title' => ['$eq'],  // title will be limited to the eq operator
  'title' => '$eq',    // works only for one restricted operator
  'title:$eq',         // same as above
  'title',             // this won't be restricted to any operator
];
```
The drawback here is that you have to define all the allowed fields, regardless of any restrictions fields.
- **Option 2 : Define them inside `$restrictedFilters` property**
```php
$restrictedFields = [
  'title' => ['$eq', '$eq'],  // title will be limited to the eq operator
  'title:$eq,$in'             // same as above
  'title'                     // this won't be restricted to any operator
];
```
- **Option 3 : Finally, you can set it on the Eloquent builder, which takes the highest priority (overwrite all the above options)**
```php
Post::restrictedFilters(['title' => ['$eq']])->filter()->get();

```
**Note**
>All field-restricted filter operations are respected to filters defined in $filter ([here](#restrict-filters)). This means you are not allowed to restrict a field operation that is not permitted in restricted fields.
```php
$filters = ['$eq'];
$restrictedFilters = ['title' => ['$eqc']] // This won't work
```
### Changing Params Source
By Default, purity gets params from filters index in query params, overwrite this by passing params directly to filter or sort functions:

```php
Post::filter($params)->get();

Post::filter([
            'title' => ['$eq' => 'good post']
        ])->get();

Post::sort([
            'title',
            'id:desc'
        ])->get();
```
### Livewire
to add filter to your livewire app, first define `$filters` variable in your component and pass it to filter or sort method:
```php
// component

#[Url]
public $filters = [
  'title' => [],
];

public function render()
{
  $transactions = Transaction::filter($this->filters)->get();

  return view('livewire.transaction-table',compact('transactions'));
}

```

then bind the variable in your blade template.

```html
<!-- in blade template -->

<input type="text" wire:model.live="filters.title.$eq" placeholder="title" />
```

read more in [livewire docs](https://livewire.laravel.com/docs/url)

### Custom Filters
Create a custom filter class by this command:

```sh
php artisan make:filter EqualFilter
```

this will generate a filter class in `Filters` directory. By default, all classes defined in `Filters` directory are loaded into the package. you can change scan folder location in purity config file.

```php
// configs/purity.php

'custom_filters_location' => app_path('Filters'),
```

### Silent Exceptions
By default, purity silences its own exceptions. to change that behavior change the `silent` index to `false` in the config file.

```php
// configs/purity.php

'silent' => false,
```
### Sort null values last
When sorting a column that contains null values, it's typically preferred to have those values appear last, regardless of the sorting direction. You can enable this feature in the configuration as follows:
```php
// configs/purity.php

null_last => true;
```

## Queries and javascript examples
This section is a guide for front-end developers who want to use an API that uses Laravel Purity.
### Available Filters
Queries can accept a filters parameter with the following syntax:

`GET /api/posts?filters[field][operator]=value`

**By Default** the following operators are available:

| Operator        | Description                              |
|-----------------|------------------------------------------|
| `$eq`           | Equal                                    |
| `$eqc`          | Equal (case-sensitive)                   |
| `$ne`           | Not equal                                |
| `$lt`           | Less than                                |
| `$lte`          | Less than or equal to                    |
| `$gt`           | Greater than                             |
| `$gte`          | Greater than or equal to                 |
| `$in`           | Included in an array                     |
| `$notIn`        | Not included in an array                 |
| `$contains`     | Contains                                 |
| `$notContains`  | Does not contain                         |
| `$containsc`    | Contains (case-sensitive)                |
| `$notContainsc` | Does not contain (case-sensitive)        |
| `$null`         | Is null                                  |
| `$notNull`      | Is not null                              |
| `$between`      | Is between                               |
| `$notBetween`   | Is not between                           |
| `$startsWith`   | Starts with                              |
| `$startsWithc`  | Starts with (case-sensitive)             |
| `$endsWith`     | Ends with                                |
| `$endsWithc`    | Ends with (case-sensitive)               |
| `$or`           | Joins the filters in an "or" expression  |
| `$and`          | Joins the filters in an "and" expression |

#### Simple Filtering

> **Tip**
>   In javascript use [qs](https://www.npmjs.com/package/qs) directly to generate complex queries instead of creating them manually. Examples in this documentation showcase how you can use `qs`.

Find users having 'John' as their first name

`GET /api/users?filters[name][$eq]=John`
  ```js
  const qs = require('qs');
const query = qs.stringify({
  filters: {
    username: {
      $eq: 'John',
    },
  },
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/users?${query}`);
  ```
Find multiple restaurants with ids 3, 6, 8

`GET /api/restaurants?filters[id][$in][0]=3&filters[id][$in][1]=6&filters[id][$in][2]=8`
  ```js
  const qs = require('qs');
const query = qs.stringify({
  filters: {
    id: {
      $in: [3, 6, 8],
    },
  },
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/restaurants?${query}`);
  ```
#### Complex Filtering
Complex filtering is combining multiple filters using advanced methods such as combining `$and` & `$or`. This allows for more flexibility to request exactly the data needed.

Find books with 2 possible dates and a specific author.

`GET /api/books?filters[$or][0][date][$eq]=2020-01-01&filters[$or][1][date][$eq]=2020-01-02&filters[author][name][$eq]=Kai%20doe`
```js
const qs = require('qs');
const query = qs.stringify({
  filters: {
    $or: [
      {
        date: {
          $eq: '2020-01-01',
        },
      },
      {
        date: {
          $eq: '2020-01-02',
        },
      },
    ],
    author: {
      name: {
        $eq: 'Kai doe',
      },
    },
  },
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/books?${query}`);
```
#### Deep Filtering
Deep filtering is filtering on a relation's fields.

Find restaurants owned by a chef who belongs to a 5-star restaurant

`GET /api/restaurants?filters[chef][restaurants][stars][$eq]=5`
```js
const qs = require('qs');
const query = qs.stringify({
  filters: {
    chef: {
      restaurants: {
        stars: {
          $eq: 5,
        },
      },
    },
  },
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/restaurants?${query}`);
```

### Apply Sort

#### Apply Basic Sorting
Queries can accept a sort parameter that allows sorting on one or multiple fields with the following syntax's:

`GET /api/:pluralApiId?sort=value` to sort on 1 field

`GET /api/:pluralApiId?sort[0]=value1&sort[1]=value2` to sort on multiple fields (e.g. on 2 fields)

The sorting order can be defined as:
- `:asc` for ascending order (default order, can be omitted)
- `:desc` for descending order.

*Usage Examples*

Sort using 2 fields

`GET /api/articles?sort[0]=title&sort[1]=slug`

```js
const qs = require('qs');
const query = qs.stringify({
  sort: ['title', 'slug'],
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/articles?${query}`);
```

Sort using 2 fields and set the order

`GET /api/articles?sort[0]=title%3Aasc&sort[1]=slug%3Adesc`

```js
const qs = require('qs');
const query = qs.stringify({
  sort: ['title:asc', 'slug:desc'],
}, {
  encodeValuesOnly: true, // prettify URL
});

await request(`/api/articles?${query}`);
```
#### Apply Sort by Relationships

All the usages of basic sorting is applicable. Use dot(.) notation to apply relationship in the following format.

`?sort=[relationship name].[relationship column]:[sort direction]`

*Usage Examples*

The query below sorts posts by their tag name in ascending order (default sort direction).
Direction is not mandatory when sort by ascending order.

`GET /api/posts?sort=tags.name:asc`

> [!Note]
> Sorting by nested relationships is not supported by the package as of now.

## Upgrade Guide

### Version 3
changed how `$filterFields` array works. it no longer renames fields, instead, it restricts filters that are accepted by the field as mentioned in the [Restrict filters](#restrict-filters) section.
to rename fields refer to [Rename fields](#rename-fields). `sortFields` However, didnt change. 

### Version 2
changed filter function arguments. filter function no longer accepts filter methods, instead, filter function now accepts filter source as mentioned in [Custom Filters](#custom-filters) section.
to specify allowed filter methods use filterBy as mentioned in [Restrict Filters](#restrict-filters)

## License

Laravel Purity is Licensed under The MIT License (MIT). Please see [License File](https://github.com/abbasudo/laravel-purity/blob/master/LICENSE) for more information.

## Security

If you've found a bug regarding security please mail [amkhzomi@gmail.com](mailto:amkhzomi@gmail.com) instead of
using the issue tracker.
