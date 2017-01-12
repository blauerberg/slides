## Topics

* About Drupal cache
* Usecase of large scale system
* For Drupal 8

---

## About Drupal cache

![logo](resources/images/druplicon-large.png) <!-- .element: style="background: none; border: none; width: 40%;" -->

---

## Provided by core

11 cache tables (D7)
- cache
- cache_block
- cache_boostrap
- cache_field
- cache_filter
- cache_form
- cache_image
- cache_menu
- cache_page
- cache_path
- cache_update

---

## Provided by contribute modules

* cache_apachesolr
* cache_libraries
* cache_location
* cache_panels
* cache_rules
* cache_tmgmt
* cache_variable
* cache_views
* cache_views_data
* etc..

---

## Structure of cache table

```
MariaDB [drupal]> describe cache;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| cid        | varchar(255) | NO   | PRI |         |       |
| data       | longblob     | YES  |     | NULL    |       |
| expire     | int(11)      | NO   | MUL | 0       |       |
| created    | int(11)      | NO   |     | 0       |       |
| serialized | smallint(6)  | NO   |     | 0       |       |
+------------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```
<!-- .element: style="font-size: 0.8em;" -->

---

## Structure of cache table

```
MariaDB [drupal]> describe cache_page;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| cid        | varchar(255) | NO   | PRI |         |       |
| data       | longblob     | YES  |     | NULL    |       |
| expire     | int(11)      | NO   | MUL | 0       |       |
| created    | int(11)      | NO   |     | 0       |       |
| serialized | smallint(6)  | NO   |     | 0       |       |
+------------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```
<!-- .element: style="font-size: 0.8em;" -->

---

## Structure of cache table

```
MariaDB [drupal]> describe cache_views;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| cid        | varchar(255) | NO   | PRI |         |       |
| data       | longblob     | YES  |     | NULL    |       |
| expire     | int(11)      | NO   | MUL | 0       |       |
| created    | int(11)      | NO   |     | 0       |       |
| serialized | smallint(6)  | NO   |     | 0       |       |
+------------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```
<!-- .element: style="font-size: 0.8em;" -->

---

## All of caches has same structure and can access by same API.

---

## cache_get, cache_set

```php
cache_get($cid, $bin = 'cache')
cache_set($cid, $data, $bin = 'cache', $expire = CACHE_PERMANENT)
```
<!-- .element: style="font-size: 0.8em;" -->

https://api.drupal.org/api/drupal/includes%21cache.inc/function/cache_get/7.x <!-- .element: style="font-size: 0.7em;" -->
https://api.drupal.org/api/drupal/includes%21cache.inc/function/cache_set/7.x <!-- .element: style="font-size: 0.7em;" -->

---

## Create a custom cache table

```php
/**
 * Implements of hook_schema().
 */
function custom_module_scheme() {
  $schema = [];
  $schema['cache_custom'] = drupal_get_schema_unprocessed('system', 'cache');
  return $scheme;
}
```
<!-- .element: style="font-size: 0.7em;" -->

https://api.drupal.org/api/drupal/modules%22system%21system.api.php/function/hook_schema/7.x <!-- .element: style="font-size: 0.7em;" -->
https://api.drupal.org/api/drupal/includes!common.inc/function/drupal_get_schema_unprocessed/7.x <!-- .element: style="font-size: 0.7em;" -->

---

## Structure of custom cache table

```
MariaDB [drupal]> describe cache_custom;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| cid        | varchar(255) | NO   | PRI |         |       |
| data       | longblob     | YES  |     | NULL    |       |
| expire     | int(11)      | NO   | MUL | 0       |       |
| created    | int(11)      | NO   |     | 0       |       |
| serialized | smallint(6)  | NO   |     | 0       |       |
+------------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```
<!-- .element: style="font-size: 0.8em;" -->

---

cidにユーザーのコンテキストが特定できる識別子を組み込むだけで、ユーザー単位のキャッシュが管理できる

```php

function respond_contents() {
  global $user;
  $cid = "{$user->uid}-SOME-CACHE-HASH-STRING";
  
  $cache = cache_get($cid, 'cache_custom');
  if (is_null($cache) || is_expire($cache)) {
    $generated_contents = create_some_user_specific_contents();
    cache_set($cid, serialize($generated_contents), 'cache_custom', time() + 600);
    return $generated_contents;
  }
  return $cache; 
}
```
<!-- .element: style="font-size: 0.7em;" -->

---

## Usecase of large scale system

![logo](resources/images/druplicon-large.png) <!-- .element: style="background: none; border: none; width: 40%;" -->

---

## Requirements of our system

- Multilingual (25+ countries)
- Fast response to access from all over the world
- More than 1,000,000 PV/month
- More than 100,000 authenticated users
- Many user/role specific contents, almost user logged in to use the system
- Access log for every user
- etc..

---

## User specific contents?

---

<!-- .slide: data-background="resources/images/amazon_anonymous.png" data-background-size="100%" -->

---

<!-- .slide: data-background="resources/images/amazon_loggedin.png" data-background-size="100%" -->

---

<!-- .slide: data-background="resources/images/amazon_loggedin_marker.png" data-background-size="100%" -->

---

## ユーザー特有のコンテンツが多いとHTML全体をキャッシュすることは難しい

---

## では、このようなユースケースではCDNなどのキャッシュ機構は使えないのか？

---

## Lazy Loading

> Lazy loading is a design pattern commonly used in computer programming to defer initialization of an object until the point at which it is needed. It can contribute to efficiency in the program's operation if properly and appropriately used. The opposite of lazy loading is eager loading.

https://en.wikipedia.org/wiki/Lazy_loading <!-- .element: class="grow" -->

---

<!-- .slide: data-background="resources/images/amazon_loggedin_lazyload.png" data-background-size="100%" -->

---

## Cache strategy

- CDN deriver contents from the edge server near the user.
- CDN won't not always return a completed page. User specific contents will be loaded lazy by Drupal (parallelly & asynchronous), and then derivery and update partially.
- Drupal try to return user specific contents from internal cache that is managed per user or role.
- If internal cache doesn't exists, Drupal generate these contents and set contents to the internal cache to response faster in next time.

---

## System structure

![sytem structure](resources/images/system_structure.png) <!-- .element: style="background: none; border: none; width: 80%;" -->

---

### if CDN has cached content

![hit cdn](resources/images/hit_cdn.png) <!-- .element: style="background: none; border: none; width: 70%;" -->

---

### if Varnish has cached content

![hit varnish](resources/images/hit_varnish.png) <!-- .element: style="background: none; border: none; width: 70%;" -->

---

### if internal cache has cached content

![hit internal cache](resources/images/hit_internal_cache.png) <!-- .element: style="background: none; border: none; width: 70%;" -->

---

### no cache

![no cache](resources/images/no_cache.png) <!-- .element: style="background: none; border: none; width: 70%;" -->

---

### Lazy loading user specific contents.

![lazy load](resources/images/lazy_load.png) <!-- .element: style="background: none; border: none; width: 70%;" -->

---

## Implementation

- Per user/role cache control
- Custom drush command for cache management
- Lazy loadable all elements (menu, sidebar, main content, etc..)

---


## For Drupal 8

![logo](resources/images/druplicon-large.png) <!-- .element: style="background: none; border: none; width: 40%;" -->

---

## Can we use this architecture in Drupal 8 ?

### you can implement more easily using by awesome Cache API.
<!-- .element: class="fragment" data-fragment-index="1" -->

https://www.drupal.org/docs/8/api/cache-api
<!-- .element: style="font-size: 0.9em;" class="fragment" data-fragment-index="2" -->

---

## Cache Contexts

複数のコンテキストがcoreで定義され、独自のコンテキストの定義も可能

```json
cookies
  :name
headers
  :name
ip
languages
  :type
...
session
  .exists
...
user
  .is_super_user
  .node_grants
    :operation
  .permissions
  .roles
    :role
```

---

core.services.yml
```yml
  ...
  cache_context.user:
    class: Drupal\Core\Cache\Context\UserCacheContext
    arguments: ['@current_user']
    tags:
      - { name: cache.context}
  cache_context.user.permissions:
  ...
```

---

UserCacheContext.php

```php
class UserCacheContext extends UserCacheContextBase implements CacheContextInterface {

  /**
   * {@inheritdoc}
   */
  public static function getLabel() {
    return t('User');
  }

  /**
   * {@inheritdoc}
   */
  public function getContext() {
    return $this->user->id();
  }

  /**
   * {@inheritdoc}
   */
  public function getCacheableMetadata() {
    return new CacheableMetadata();
  }
}
```
<!-- .element: style="font-size: 0.5em;" -->

---

DatabaseBackend.php
```php
class DatabaseBackend implements CacheBackendInterface {
  ...

  public function get($cid, $allow_invalid = FALSE) {
    $cids = array($cid);
    $cache = $this->getMultiple($cids, $allow_invalid);
    return reset($cache);
  }

  public function getMultiple(&$cids, $allow_invalid = FALSE) {
    $cid_mapping = array();
    foreach ($cids as $cid) {
      $cid_mapping[$this->normalizeCid($cid)] = $cid;
    }
    $result = array();
    try {
      $result = $this->connection->query('SELECT cid, data, created, expire, serialized, tags, checksum FROM {' . $this->connection->escapeTable($this->bin) . '} WHERE cid IN ( :cids[] ) ORDER BY cid', array(':cids[]' => array_keys($cid_mapping)));
    }

    ...

    return $cache;
  }
...
```
<!-- .element: style="font-size: 0.5em;" -->

---

## Cache API

D7同様のバックエンドに依存しないシンプルで統一されたAPI

```php
public CacheBackendInterface::get($cid, $allow_invalid = FALSE)
public CacheBackendInterface::set($cid, $data, $expire = Cache::PERMANENT, array $tags = array())
```
<!-- .element: style="font-size: 0.6em;" -->

---

## Cache tag

キャッシュタグにより簡単にコントロール可能に

> Cache tags = data dependencies

> Cache tags describe dependencies on data managed by Drupal

ex. "user:1", "role:admin", "node:10"

