_This is a (hopefully temporary) fork of http://github.com/fetcher/symbolmatrix which incorporates some changes from @diminish7 that we need for https://github.com/gamera-team/gamera_

SymbolMatrix
============

> Strongly inspired in [SymbolTable][symboltable] gem by [Michael Jackson][michael-jackson-home] (thanks a lot!)

> **New!**: SymbolMatrix now features a serialization syntax from which it can be parsed and to which it can be serialized. I'm calling it SMAS (SymbolMAtrix Serialization) and it's loosely inspired in both [rake][rake-link] and [thor][thor-link]'s argument syntax styles. Read more about it in the *SMAS* section downstairs.

**SymbolMatrix** is a simple Ruby Gem that extends `Hash` so that only `Symbol`s can be used as keys. Why? Because it also allows you to query the SymbolMatrix object using the dot syntax and a string key equivalent to the symbol one, making it more Principle of Least Surprise.

The showdown:

```ruby
h = Hash.new
h[:a] = 1
h[:a]       # => 1
h['a']      # => nil
h.a         # => NoMethodError: undefined method `a' for {}:Hash

require 'symbolmatrix'

m = SymbolMatrix.new
m[:a] = 1
m[:a]       # => 1
m['a']      # => 1
m.a         # => 1
```

If you are familiar with SymbolTable you may have noticed that, so far, the same functionality is provided in the original gem. SymbolMatrix adds three key features:

1.  **Recursive convertion to SymbolMatrix**

    ```ruby
    require "symbolmatrix"

    m = SymbolMatrix :quantum => { :nano => "data" }

    m.quatum.nano # => "data"
    ```

2.  **JSON/YAML autoloading**

    ```ruby
    require "symbolmatrix"

    m = SymbolMatrix "quantum: of solace"
    m.quantum   # => "of solace"

    # Or alternatively
    m.from.yaml "quantum: of solace"

    # Provided a file exists "configuration.yaml" with "database_uri: mysql://root@localhost/database"
    m.from.file "configuration.yaml"
    m.database_uri  # => "mysql://root@localhost/database"

    # Or simply
    m = SymbolMatrix "configuration.yaml"
    ```

    Since all JSON is valid YAML... yey, it works with JSON too!
	```ruby
    require 'symbolmatrix'

    data = SymbolMatrix '{"message":"Awesome"}'
    data.message # => 'Awesome'
    ```

3.  **Own serialization format for command line**

    Look the next section

[symboltable]: https://github.com/mjijackson/symboltable
[michael-jackson-home]: http://mjijackson.com/
[rake-link]: https://github.com/jimweirich/rake
[thor-link]: https://github.com/wycats/thor

SMAS: SymbolMatrix Serialization
--------------------------------

The serialization format is designed to serve as an alternative for the command line environment, when multiline (YAML) and special characters (JSON) are not well suited. It's very intuitive and designed to closely resemble the way SymbolMatrix is used within Ruby code. For example:

```ruby
require 'symbolmatrix'

s = SymbolMatrix 'this.serialization:format i.think:rocks!'

s.this.serialization    # => "format"
s.i.think               # => "rocks!"

s.from.serialization 'works.with.numbers:793'

s.works.with.numbers    # => 793
```

You can get the serialization from any SymbolMatrix:

```ruby
require 'symbolmatrix'

SymbolMatrix(:where => {
  :is => {
    :paris  => "france",
    :roma   => "italia"
  }
).to.serialization # => 'where.is.paris:france where.is.roma:italia'
```

> This serialization format is better exploited in the [Empezar Ruby Gem][empezar-link]

[empezar-link]: https://github.com/Fetcher/empezar

Further details
---------------

### Recursive `#to.hash` convertion

SymbolMatrix provides the `#to.hash` method that recursively transforms the matrix into a n-dimentional Hash.

### Disabling the extras

If for some reason you don't want this class to use YAML nor the serialization format SMAS, you can just require `symbolmatrix/symbolmatrix` and those functionalities will not be loaded.

### Exceptions

Whenever a key is not found in the SymbolMatrix, a custom `SymbolMatrix::KeyNotDefinedException` will be raised.
Similarly, if you attempt to use an inconvertible key (inconvertible to `Symbol`) a `SymbolMatrix::InvalidKeyException` will be raised.

Changelog for 1.0.0
-------------------

Serialization format aside, there had been some minor changes to the API worth noting:

### Deprecations

As of version 1.0.0, Writers and Readers has been added, so now the regular methods `from_yaml`, `from_file` and `to_hash` are deprecated in favor of `from.yaml`, `from.file` and `to.hash`. Please change to the new API is you're working with SymbolMatrix right now.

### `.new` no longer needed

`SymbolMatrix :a => 'b'` is now equivalent to `SymbolMatrix.new(:a => 'b')`.

SymbolMatrix is mostly about the developers having an easier time with code so I place strong priority in these small changes for comfort.

### `#recursive_merge` available

> Note: this is not yet working with any Hash, just SymbolMatrices.

Yey! This one was missing quite a while from Ruby Hashes (if you are like me and spend a lot of time dealing with multidimensional hashes).

The API is straightforward:
```ruby
sm = SymbolMatrix(:a => { :b => "c" })
sm.recursive_merge SymbolMatrix(:a => { :c => "d"})

sm.a.b # => "c"
sm.a.c # => "d"
```

Installation
------------

    gem install symbolmatrix

### Or using Bundler
Add this line to your application's Gemfile:

    gem 'symbolmatrix'

And then execute:

    bundle install

## Known issues

- When loading a YAML/JSON document that has an sequence &ndash;array&ndash; as the root item, SymbolMatrix will crash. This is because SymbolMatrix wasn't intended for arrays, just Hashes, and the recursive conversion from Hash to SymbolMatrix is not working with Arrays.
- Some keys overlap with method names, so can't be used with the dot syntax. For example, `key`. Just use them with the hash syntax instead :)

## Future

- Make SymbolMatrix raise a more relevant error when the file does not exist (something along the lines of "There was an error parsing the YAML string, may be it was a file path and the file does not exist?"). It is possible to increase the chances of detecting properly with a really simple regular expression...

## Testing

RSpec specs are provided to check functionality.

## License

Copyright (C) 2012 Fetcher

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
