# Getting started with Pacto

The first thing we'll do is creating a .ruby-version file with `2.2.3`. What this does is setting the ruby version we'll be working with. 

`echo '2.2.3' >> .ruby-version`

Next step is creating a Gemfile with the following content:

```ruby
source 'http://rubygems.org'

gem 'pacto'
gem 'json-schema', '2.2.2'
gem 'rake'
```


