# Replicator::Consistent

Extension for [replicator](http://github.com/fatum/replicator) which provides eventual consistency

## Installation

Add this line to your application's Gemfile:

    gem 'replicator', github: 'fatum/replicator'
    gem 'replicator-consistent', github: 'fatum/replicator-consistent'

And then execute:

    $ bundle

## About

We use 2PC (two phase commit) simplified approach.
If you want to use true 2PC, look at Zookeeper for transaction management.

When record mutate

  1. Start new mutation (increment sequence id)
  2. Mutation state is :prepare
  3. Start db transaction (if used ActiveRecord)
  4. Send message to MQ (with new sequence id)
  5. Commit db transaction
  6. Commit mutation (state :commited)

## Usage

```ruby
class TransactionalProducer < ActiveRecord::Base
  include Replicator::Producer::Mixin

  produce :stream, atomic: true do
    consumers :consumer1
    adapter :adapter
  end
end

record = TransactionalProducer.new(data)
record.producer.transaction do
  record.save!

  sync :create, record.attributes
end
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
