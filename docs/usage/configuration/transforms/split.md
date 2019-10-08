---
description: Accepts `log` events and allows you to split a field's value on a given separator and zip the tokens into ordered field names.
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the template located at:

     scripts/generate/templates/docs/usage/configuration/transforms/split.md.erb
-->

# split transform

![][assets.split_transform]


The `split` transform accepts [`log`][docs.data-model.log] events and allows you to split a field's value on a given separator and zip the tokens into ordered field names.

## Config File

{% code-tabs %}
{% code-tabs-item title="vector.toml (simple)" %}
```coffeescript
[transforms.my_transform_id]
  type = "split" # must be: "split"
  inputs = ["my-source-id"]
  field_names = ["timestamp", "level", "message"]

  # For a complete list of options see the "advanced" tab above.
```
{% endcode-tabs-item %}
{% code-tabs-item title="vector.toml (advanced)" %}
```coffeescript
[transforms.split_transform]
  #
  # General
  #

  # The component type
  # 
  # * required
  # * no default
  # * must be: "split"
  type = "split"

  # A list of upstream source or transform IDs. See Config Composition for more
  # info.
  # 
  # * required
  # * no default
  inputs = ["my-source-id"]

  # The field names assigned to the resulting tokens, in order.
  # 
  # * required
  # * no default
  field_names = ["timestamp", "level", "message"]

  # If `true` the `field` will be dropped after parsing.
  # 
  # * optional
  # * default: true
  drop_field = true

  # The field to apply the split on.
  # 
  # * optional
  # * default: "message"
  field = "message"

  # The separator to split the field on. If no separator is given, it will split
  # on whitespace.
  # 
  # * optional
  # * no default
  separator = ","

  #
  # Types
  #

  [transforms.split_transform.types]
    # A definition of mapped field types. They key is the field name and the value
    # is the type. `strftime` specifiers are supported for the `timestamp` type.
    # 
    # * required
    # * no default
    # * enum: "string", "int", "float", "bool", and "timestamp|strftime"
    status = "int"
    duration = "float"
    success = "bool"
    timestamp = "timestamp|%s"
    timestamp = "timestamp|%+"
    timestamp = "timestamp|%F"
    timestamp = "timestamp|%a %b %e %T %Y"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Examples

Given the following log line:

{% code-tabs %}
{% code-tabs-item title="log" %}
```json
{
  "message": "5.86.210.12,zieme4647,19/06/2019:17:20:49 -0400,GET /embrace/supply-chains/dynamic/vertical,201,20574"
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And the following configuration:

{% code-tabs %}
{% code-tabs-item title="vector.toml" %}
```coffeescript
[transforms.<transform-id>]
type = "split"
field = "message"
fields = ["remote_addr", "user_id", "timestamp", "message", "status", "bytes"]
  [transforms.<transform-id>.types]
    status = "int"
    bytes = "int"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

A [`log` event][docs.data-model.log] will be emitted with the following structure:

```javascript
{
  // ... existing fields
  "remote_addr": "5.86.210.12",
  "user_id": "zieme4647",
  "timestamp": "19/06/2019:17:20:49 -0400",
  "message": "GET /embrace/supply-chains/dynamic/vertical",
  "status": 201,
  "bytes": 20574
}
```

A few things to note about the output:

1. The `message` field was overwritten.
2. The `status` and `bytes` fields are integers because of type coercion.

## How It Works

### Environment Variables

Environment variables are supported through all of Vector's configuration.
Simply add `${MY_ENV_VAR}` in your Vector configuration file and the variable
will be replaced before being evaluated.

You can learn more in the [Environment Variables][docs.configuration#environment-variables]
section.

### Types

By default, extracted (parsed) fields all contain `string` values. You can
coerce these values into types via the `types` table as shown in the
[Config File](#config-file) example above. For example:

```coffeescript
[transforms.my_transform_id]
  # ...

  # OPTIONAL - Types
  [transforms.my_transform_id.types]
    status = "int"
    duration = "float"
    success = "bool"
    timestamp = "timestamp|%s"
    timestamp = "timestamp|%+"
    timestamp = "timestamp|%F"
    timestamp = "timestamp|%a %b %e %T %Y"
```

The available types are:

| Type        | Desription                                                                                                          |
|:------------|:--------------------------------------------------------------------------------------------------------------------|
| `bool`      | Coerces to a `true`/`false` boolean. The `1`/`0` and `t`/`f` values are also coerced.                               |
| `float`     | Coerce to 64 bit floats.                                                                                            |
| `int`       | Coerce to a 64 bit integer.                                                                                         |
| `string`    | Coerces to a string. Generally not necessary since values are extracted as strings.                                 |
| `timestamp` | Coerces to a Vector timestamp. [`strftime` specificiers][urls.strftime_specifiers] must be used to parse the string. |

## Troubleshooting

The best place to start with troubleshooting is to check the
[Vector logs][docs.monitoring#logs]. This is typically located at
`/var/log/vector.log`, then proceed to follow the
[Troubleshooting Guide][docs.troubleshooting].

If the [Troubleshooting Guide][docs.troubleshooting] does not resolve your
issue, please:

1. Check for any [open `split_transform` issues][urls.split_transform_issues].
2. If encountered a bug, please [file a bug report][urls.new_split_transform_bug].
3. If encountered a missing feature, please [file a feature request][urls.new_split_transform_enhancement].
4. If you need help, [join our chat/forum community][urls.vector_chat]. You can post a question and search previous questions.


### Alternatives

Finally, consider the following alternatives:

* [`grok_parser` transform][docs.transforms.grok_parser]
* [`lua` transform][docs.transforms.lua]
* [`regex_parser` transform][docs.transforms.regex_parser]
* [`tokenizer` transform][docs.transforms.tokenizer]

## Resources

* [**Issues**][urls.split_transform_issues] - [enhancements][urls.split_transform_enhancements] - [bugs][urls.split_transform_bugs]
* [**Source code**][urls.split_transform_source]


[assets.split_transform]: ../../../assets/split-transform.svg
[docs.configuration#environment-variables]: ../../../usage/configuration#environment-variables
[docs.data-model.log]: ../../../about/data-model/log.md
[docs.monitoring#logs]: ../../../usage/administration/monitoring.md#logs
[docs.transforms.grok_parser]: ../../../usage/configuration/transforms/grok_parser.md
[docs.transforms.lua]: ../../../usage/configuration/transforms/lua.md
[docs.transforms.regex_parser]: ../../../usage/configuration/transforms/regex_parser.md
[docs.transforms.tokenizer]: ../../../usage/configuration/transforms/tokenizer.md
[docs.troubleshooting]: ../../../usage/guides/troubleshooting.md
[urls.new_split_transform_bug]: https://github.com/timberio/vector/issues/new?labels=transform%3A+split&labels=Type%3A+bug
[urls.new_split_transform_enhancement]: https://github.com/timberio/vector/issues/new?labels=transform%3A+split&labels=Type%3A+enhancement
[urls.split_transform_bugs]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22transform%3A+split%22+label%3A%22Type%3A+bug%22
[urls.split_transform_enhancements]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22transform%3A+split%22+label%3A%22Type%3A+enhancement%22
[urls.split_transform_issues]: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22transform%3A+split%22
[urls.split_transform_source]: https://github.com/timberio/vector/tree/master/src/transforms/split.rs
[urls.strftime_specifiers]: https://docs.rs/chrono/0.3.1/chrono/format/strftime/index.html
[urls.vector_chat]: https://chat.vector.dev