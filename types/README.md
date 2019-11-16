# The Basics

[[toc]]

A Graph consits of a one or more Types  these types can be seen as the basic blocks and can represents resources and the fields that are part of this resource.
there are 6 base types specified by the GraphQL specification on which we can build 

These types are 

1. ScalarType (Integer,Float,String,Boolean,ID)
2. ObjectType
3. InterfaceType
4. UnionType
5. EnumType
6. InputType

This GraphServer adds a aditional EloquentType to the mix which is an extention to the ObjectType graph type and allows the graphserver to query eloquent models with the minumum configuration



## Defining Types

By default, a GraphServer's types are stored in the `app/Graph/Types` directory of your application. 
You may generate a new Types using the `graph:type` Artisan command:

```bash
php artisan graph:type Post
```

By default this command will create a new type which extends the ObjectType type if you want you can specify the type of base types to extend by adding the --type option:

```bash
php artisan graph:type Post --type=eloquent

```

Freshly created Types only contain an `ID` field definition. Don't worry, we'll add more fields to our Type soon.

## Registering Resources

:::tip Automatic Registration

By default, all types within the `app/Graph` directory will automatically be registered with  the GraphServer. You are not required to manually register them.
:::

Before types are available to query within your GraphServer, they must first be registered with the Schema. Types are typically registered in your `app/Providers/GraphServiceProvider.php` file. This file contains various configuration and bootstrapping code related to your GraphServer installation.

**As mentioned above, you are not required to manually register your Types; however, if you choose to do so, you may do so by overriding the `types` method of your `GraphServiceProvider`**.

There are two approaches to manually registering types. You may use the `typesIn` method to instruct Nova to register all types within a given directory. Alternatively, you may use the `types` method to manually register individual types:

```php
use App\Graph\User;
use App\Graph\Post;

/**
 * Register the application's graph types.
 *
 * @return void
 */
protected function resources()
{
    GraphServer::typessIn(app_path('Graph'));

    GraphSErver::types([
        User::class,
        Post::class,
    ]);
}
```

Once your types are registered with the Schema, they will be available to query.

