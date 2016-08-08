# Getting started with Pacto

The first thing we'll do is creating a .ruby-version file with `2.2.3`. What this does is setting the ruby version we'll be working with. 

`echo '2.2.3' >> .ruby-version`

Next step is creating a Gemfile with the following content:

```ruby
source 'http://rubygems.org'

gem 'pacto'
gem 'rake'
gem 'json-schema', '2.2.2'

```

A Gemfile is a file used to define our ruby project's dependencies.

Now run a `bundle install`. This should install the dependencies you need to get pacto running.

At this point we are ready to write the code that will generate our contract tests. For this we'll use rake tasks. A raketask is basically a set of instructions to be executed.

You'll create a file called `Rakefile` and it should have the following content:

```ruby
require 'pacto'

task :generate do
    WebMock.allow_net_connect!

    Pacto.configure do |c|
      c.contracts_path = 'contracts'
    end

    Pacto.generate!

    Net::HTTP.get('pactodex.herokuapp.com', '/pokemons')
end
```

What it does is, step by step:

`require 'pacto'`: imports pacto module

`task :generate do`: starts the definition of a rake task called `generate`

`    WebMock.allow_net_connect!`: allows external connections (since they are not enabled by default)

```ruby
    Pacto.configure do |config|
      config.contracts_path = 'contracts'
    end
```
: Sets the path to where our generated contract tests will be stored

And the most important line:

`    Pacto.generate!`: tells pacto to listen for requests we'll make and to generate contracts based on them

To finish

`    Net::HTTP.get('pactodex.herokuapp.com', '/pokemons')`

sends a HTTP request to http://pactodex.herokuapp.com/pokemons
