# Fields

[[toc]]

This GraphServer slightly diverges from the specifications naming terms as we allow you to define fields on types. 
fields are just another name for child types and consist of types based on the 6 base types.

this helps us to easily describe your aplications resources

## Defining Fields

each GraphServer type (appart from the scalar types) contains a `fields` method. This method returns an array of types.
GraphServer contains a number of types out of the box, including types for text inputs, booleans, dates and more.

to add fields to a resource, we can simply add it to the types `fields` method. Typically, types may be created using their static `make` method. This method accepts several arguments; however, you usually only need to pass the name of the field.

```php
use Tools4Schools\Graph\Fields\ID;
use Tools4Schools\Graph\Fields\Text;

/**
 * Returns all the fields that are part of this Type.
 *
 * @return array
 */
public function fields():array
{
    return [
        ID::make(),
        Text::make('Name'),
    ];
}
```

## Field Types

:::tip Relationship Fields

This portion of the documentation only discusses non-relationship fields. To learn more about relationship fields, [check out their documentation](/2.0/types/relationships.html).
:::

GraphServer ships with a variety of field types. So, let's explore all of the available types and their options:


- [Boolean](#boolean-field)
- [Currency](#currency-field)
- [Date](#date-field)
- [DateTime](#datetime-field)
- [ID](#id-field)
- [Number](#number-field)
- [Text](#text-field)

### Boolean Field

The `Boolean` field may be used to represent a boolean / "tiny integer" column in your database. For example, assuming your database has a boolean column named `active`, you may attach a `Boolean` field to your resource like so:

```php
use Tools4Schools\Graph\Fields\Boolean;

Boolean::make('Active'),
```

#### Customizing True / False Values

If you are using values other than `true`, `false`, `1`, or `0` to represent "true" and "false", you may instruct Nova to use the custom values recognized by your application. To accomplish this, chain the `trueValue` and `falseValue` methods onto your field's definition:

```php
Boolean::make('Active')
    ->trueValue('On')
    ->falseValue('Off'),
```

### Currency Field

The `Currency` field generates a `Number` field that is automatically displayed using PHP's `money_format` function. You may specify the display format using the `format` method; otherwise, the `%i` format will be used:

```php
use Laravel\Nova\Fields\Currency;

Currency::make('Price'),

Currency::make('Price')->format('%.2n'),
```
### Date Field

The `Date` field may be used to store a date value (without time). For more information about dates and timezones within Nova, check out the additional [date / timezone documentation](./date-fields.md):

```php
use Laravel\Nova\Fields\Date;

Date::make('Birthday'),
```

#### Date Formats

You may customize the display format of your `Date` fields using the `format` method. The format must be a format supported by [Moment.js](https://momentjs.com/docs/#/parsing/string-format/):

```php
Date::make('Birthday')->format('DD MMM'),
```

To customize the display format used for the JavaScript date picker widget, you can use the `pickerFormat` method:

```php
Date::make('Birthday')->pickerFormat('d.m.Y'),
```

To learn about the available options, you may see the flatpickr reference here: [https://flatpickr.js.org/formatting/](https://flatpickr.js.org/formatting/).

#### Customize First Day Of Week

You can customize the first day of the week using the `firstDayOfWeek` method:

```php
Date::make('Birthday')->firstDayOfWeek(1), // First day of the week is Monday
```

### DateTime Field

The `DateTime` field may be used to store a date-time value. For more information about dates and timezones within Nova, check out the additional [date / timezone documentation](./date-fields.md):

```php
use Laravel\Nova\Fields\DateTime;

DateTime::make('Updated At')->hideFromIndex(),
```

You may customize the display format of your `DateTime` fields using the `format` method. The format must be a format supported by [Moment.js](https://momentjs.com/docs/#/parsing/string-format/):

To customize the display format used for the JavaScript date picker widget, you can use the `pickerFormat` method:

```php
Date::make('Birthday')->pickerFormat('d.m.Y'),
```

To learn about the available options, you may see the flatpickr reference here: [https://flatpickr.js.org/formatting/](https://flatpickr.js.org/formatting/).

```php
DateTime::make('Created At')->format('DD MMM YYYY'),
```


### ID Field

The `ID` field represents the primary key of your resource's database table. Typically, each Nova resource you define should contain an `ID` field. By default, the `ID` field assumes the underlying database column is named `id`:

```php
use use Tools4Schools\Graph\Fields\ID;

// Using the "id" column...
ID::make(),

// Using the "id_column" column...
ID::make('id_column'),

// Resolve BIGINT ID fields
ID::make()->asBigInt(),
```
### Number Field

The `Number` field provides an `input` control with a `type` attribute of `number`:

```php
use Laravel\Nova\Fields\Number;

Number::make('price'),
```

You may use the `min`, `max`, and `step` methods to set their corresponding attributes on the generated `input` control:

```php
Number::make('price')->min(1)->max(1000)->step(0.01),
```
### Text Field

The `Text` field provides an `input` control with a `type` attribute of `text`:

```php
use Laravel\Nova\Fields\Text;

Text::make('Name'),
```

Text fields may be customized further by setting any attribute on the field. This can be done by calling the `withMeta` methods and passing in a valid `extraAttributes` value:

```php
Text::make('Name')->withMeta([
    'extraAttributes' => [
        'placeholder' => 'David Hemphill',
    ],
]);

## Customization

### Readonly Fields

There are times where you may want to allow the user to only create and update certain fields on a resource. You can do this by using the `readonly` method on the field, which will disable the field's corresponding input:

```php
Text::make('Email')->readonly(optional($this->resource)->trashed()),
```

You may also pass a `Closure` to the `readonly` method. It will receive the current `NovaRequest` as the first argument:

```php
Text::make('Email')->readonly(function ($request) {
    return !$request->user()->isAdmin();
}),
```

If you only want to set the fields to readonly when creating or attaching resources, you may use the `isCreateOrAttachRequest` and `isUpdateOrUpdateAttachedRequest` methods from `NovaRequest`:

```php
Text::make('Email')->readonly(function ($request) {
    return $request->isUpdateOrUpdateAttachedRequest();
}),
```

### Required Fields

By default, Nova will use a red asterisk to indicate a field is required:

![Required Fields](./img/required-field.png)

Nova does this by looking for the `required` rules inside the field's validation rules to determine if it should show the required state. For example, a field with this definition would receive the required treatment:

```php
Text::make('Email')->rules('required'),
```

However, you can also manually mark the field as required by passing a boolean to the `required` method on the field definition:

```php
Text::make('Email')->required(true),
```

In addition, you may also pass a closure to the `required` method to determine if the field should be marked as required. The closure will receive an instance of `NovaRequest`, which you may use to define any complex logic which should be used to evaluate the field's required state:

```php
Text::make('Email')->required(function ($request) {
    return $request->isUpdateOrUpdateAttachedRequest();
}),

Text::make('Email')->required(function ($request) {
    return $this->account_locked !== true;
}),
```

### Nullable Fields

By default, Nova attempts to store all fields with a value, however, there are times where you'd like to explicitly direct Nova to store a `null` value when the field is empty. To do this, you may use the `nullable` method on your field:

```php
Text::make('Position')->nullable(),
```

You may also set which values should be interpreted as a `null` value using the `nullValues` method:

```php
Text::make('Position')->nullable()->nullValues(['', '0', 'null']),

Text::make('Position')->nullable()->nullValues(function ($value) {
    return $value == '' || $value == 'null' || (int)$value === 0;
}),
```

### Field Resolution / Formatting

The `resolveUsing` method allows you to customize how a field is formatted after it is retrieved from your database but before it is sent to the Nova front-end. This method accepts a callback which receives the raw value of the underlying database column:

```php
Text::make('Name')->resolveUsing(function ($name) {
    return strtoupper($name);
}),
```
