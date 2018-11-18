### rack-app
---
https://github.com/rack-app/rack-app

```
gem 'rack-app'
bundle
gem install rack-app
```

```ruby
require 'rack/app'
class App < Rack::App
  desc 'some hello endpoint'
  get '/hello' do
    'Hello'
  end
end

require 'rack/app'
class App < Rack::App
  mount SomeAppClass
    headers 'Access-Control-Allow-Origin' => '*',
            'Access-Control-Expose-Headers' => 'X-My-Custom-Header, X-Another-Custom-Header'
    serializer do |object|
      object.to_s
    end
    desc 'some hello endpoint'
      required 'words', :class => Array, :of => String, desc => 'some word', :example => ['pug']
      optional 'word', :class => String, :desc => 'one word', :example => 'pug'
      optional 'boolean', :class => :boolean, :desc => 'boolean value', :example => true
    end
    get '/hello' do
      puts(params['words'])
      'Hello'
    end
  end
  namespace '/users' do
    desc 'some restful endpoint'
    get '/:user_id' do
      response.status = 201
      params['user_id']
      say
    end
  end
  desc 'some endpint that has error and will be rescued'
  get '/make_error' do
    raise(StandardError,'error block rescued')
  end
  def say
    "hello #{params['user_id']}"
  end
  error StandardError, NoMethodError do |ex|
    {:error=>ex.message}
  end
  root '/hello'
  get '/stream' do
    stream do |out|
      out << 'data row'
    end
  end
end

require 'rack/app'
require 'rack/app/front_end'
class App < Rack::App
  apply_extensions :front_end
  helpers do
    def method_that_can_be_used_in_template
      'hello!'
    end
  end
  layout 'layout.html.erb'
  get '/' do
    render 'index.html'
  end
end

require 'spec_helper'
require 'rack/app/test'
describe App do
  include Rack::App::Test
  rack_app described_class
  describe '/hello' do
    subject { get(url: '/hello', params {'dog' => 'meat'}, headers: {'X-Cat' => 'fur'}, payload: 'some string') }
    it { expect(subject.status).to eq 200 }
    it { expect(subject.body).to eq "Hello!" }
  end
  describe '' do
    subject { get(url: '/users/1234') }
    it { expect(subject.body).to eq 'hello'}
    it { expect(subject.status).to eq 201 }
  end
  describe '/make_error' do
    subject { get(url: '/make_error') }
    it { expect(subject.body).to eq '{:error=>"error block rescued"}' }
  end
end
```

```
```


