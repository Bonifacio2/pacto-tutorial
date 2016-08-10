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

You need to install o Bundler that provides a consistent environment for Ruby projects by tracking and installing the exact gems and versions that are needed. Run `sudo gem install bundler`.

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

Imports pacto module: 
```ruby
require 'pacto'
``` 

Starts the definition of a rake task called `generate`:
```ruby
task :generate do
```

Allows external connections (since they are not enabled by default):
```ruby
    WebMock.allow_net_connect!
```

Sets the path to where our generated contract tests will be stored:
```ruby
    Pacto.configure do |config|
      config.contracts_path = 'contracts'
    end
```

Tells pacto to listen for requests we'll make and to generate contracts based on them:
```ruby
    Pacto.generate!
```

Sends a HTTP request to http://pactodex.herokuapp.com/pokemons:

```ruby
    Net::HTTP.get('pactodex.herokuapp.com', '/pokemons')
```

Now we should be able to create our contract test. We just have to run `bundle exec rake generate` on the command line/terminal. This runs the `generate` rake task we defined in the `Rakefile`. It should create a `contracts/pactodex.herokuapp.com/` folder with a `pokemons.json` file inside of it.

The pokemons.json file should contain two keys: `request` and `response`.

The value corresponding to the `request` key should be something like this:
```javascript
"request": {
    "headers": {
    },
    "method": "get",
    "path": "/pokemons"
  }
```

This means: "Given that I send a GET request to /pokemons I expect to receive a response in the format described in the value corresponding to the `response` key", which should look something like this:

```javascript
"response": {
    "headers": {
      "Content-Type": "application/json"
    },
    "status": 200,
    "body": {
      "type": "array",
      "required": true,
      "minItems": 1,
      "uniqueItems": true,
      "items": {
        "type": "object",
        "required": true,
        "properties": {
          "name": {
            "type": "string",
            "required": true
          },
          "type": {
            "type": "string",
            "required": true
          },
          "number": {
            "type": "integer",
            "required": true
          }
        }
      }
    }
  }
```

Summing up, the value corresponding to `response` says that we expect to receive a response that:

Has "application/json" as its Content-Type,
```javascript
"headers": {
      "Content-Type": "application/json"
    },
```

has a status code equal to 200,
```javascript
"status": 200,
```

the response body is an array,
```javascript
"body": {
      "type": "array",
    ...
```

and each item of this array must have a "name" (string), a "type" (string) and a "number" (integer).

```javascript
  "name": {
    "type": "string",
    "required": true
  },
  "type": {
    "type": "string",
    "required": true
  },
  "number": {
    "type": "integer",
    "required": true
  }
```

## Validating a contract

To run our contract we'll create a rake task with the following content.

```ruby
task :validate do
  WebMock.allow_net_connect!
  contracts = Pacto.load_contracts('contracts', 'pactodex.herokuapp.com')
  test_results = contracts.validate_all
  if test_results.all?(&:empty?)
    puts 'success'
  else
    puts test_results
    fail
  end
end
```

In this line we are loading the contracts in the 'contracts' folder and telling pacto to run them against the 'pactodex.herokuapp.com' url:

`Pacto.load_contracts('contracts', 'pactodex.herokuapp.com')`

then we validate the test and store the results in the `test_results` variable
```ruby
test_results = contracts.validate_all
```

The condition in the `if` block is only `true` if all test results are empty, which means no error occurred.


```ruby
if test_results.all?(&:empty?)
    puts 'success'
  else
    puts test_results
    fail
  end
```

To run our `validate` task we just have to use `bundle exec rake validate` on the command line/terminal.

If everything went well you'll see a `success` message printed to your terminal. If something went wrong an error message with more details will be shown and the task will be aborted.

Now you are able to excute contract tests on your owner API service. Use your url to generate your contracts and after validate it.



