# Rancher::Api

[![Code Climate](https://codeclimate.com/github/akurkin/rancher-api/badges/gpa.svg)](https://codeclimate.com/github/akurkin/rancher-api)
[![Test Coverage](https://codeclimate.com/github/akurkin/rancher-api/badges/coverage.svg)](https://codeclimate.com/github/akurkin/rancher-api/coverage)


Rancher::Api is a Ruby wrapper around [Rancher](http://rancher.com/) API built with [Her](http://www.her-rb.org/)

Connect to Rancher and execute requests from your ruby scripts.
It allows you to use some of the super nice features from Rancher:

- provision VMs from different cloud providers (behind the scenes Rancher uses docker-machine, so that means you can create VMs in DigitalOcean, AWS, Rackspace and many more, check [list of supported drivers by Docker Machine](https://docs.docker.com/machine/drivers/))
- deploy your containers
- watch your containers
- scale your containers as you wish

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'rancher-api'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install rancher-api

## Rancher Version

Tested with:
Rancher v0.32.0

## Usage

Configure Rancher::Api first by providing url, access and secret keys:

### Entities

**Project** - Top level object that represents "Environment" in Rancher UI
**Service** - Service (combines containers from the same image)
**Machine** - Physical docker hosts
**Instance** - represents containers that were ever installed via Rancher. Better to query containers as nested resource, cuz there can be thousands of containers that were running before and still available to query via API. Removed containers are marked as 'removed' respectively.
**Environment** - In rancher UI these are known as **Stack**, though in API they are **environments**. We're sticking to API resource name.
**Host** - these are hosts, with detailed information about docker installation and resources

### Setup

```ruby
Rancher::Api.configure do |config|
  config.url = 'http://127.0.0.1:8080/v1/'
  config.access_key = '8604A1FC8C108BAFB1E3'
  config.secret_key = '4BhuyyyAaaaaBbbbi7yaZzzAaa3y13pC6D7e569'
end
```

### Querying

Now, you're able to query entities like this:

```ruby
project = Rancher::Api::Project.all.to_a
machine = Rancher::Api::Machine.find('1ph1')
```

### Creating new machines
Creating new machine using **Digital Ocean** driver:

```ruby
project = Rancher::Api::Project.all.to_a.first

new_machine = project.machines.build
new_machine.driver = Rancher::Api::Machine::DIGITAL_OCEAN
new_machine.driver_config = Rancher::Api::Machine::DriverConfig.new(
    accessToken: 'xyz',
    size: '1gb',
    region: 'ams3',
    image: 'ubuntu-14-04-x64'
)

new_machine.save
```

**NOTICE**: First specify driver, so that driver_config= accessor can correctly map config on the right attribute. I.e. for 'digitalocean' config attribute is 'digitaloceanConfig'.


### Executing shell commands in containers

```ruby
container = Rancher::Api::Instance.find('1i382')
puts container.execute('whoami').response
```

## Development

To load environment with pry run `pry -I lib -r rancher/api`

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake rspec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/akurkin/rancher-api.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

