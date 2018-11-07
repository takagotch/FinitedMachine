### FinitedMachine
---
https://github.com/piotrmurach/finite_machine

```
gem 'finite_machine'
bundle
gem install finite_machine

```

```ruby
fm = FiniteMachine.define do
  initial :red
  events {
    event :ready, :red => :yellow
    event :go, :yellow => :green
    event :stop, :green => :red
  }
  callbacks {
    on_before(:ready) { |event| ... }
    on_after(:go) { |event| ... }
    on_before(:stop) { |event| ... }
  }
end

fm.current
fm.trigger(:ready)

fm = FiniteMachine.new(initial: :red)
fm.event(:ready, :red => :yellow)
fm.event(:go, :yellow => :green)
fm.event(:stop, :green => :red)
fm.on_before(:ready) { |event| ... }
fm.on_after(:go) { |event| ... }
fm.on_before(:stop) { |event| ... }

fm.current

car = Car.new
engine = Engine.new(car)
car.reverse_lights?
engine.back
car.reverse_lights?


fm.current

fm = FiniteMachine.define do
end
fm.current
fm.init
fm.current

fm = FiniteMachine.define do
end
fm.current

fm = FiniteMachine.define initial: :green do
end

fm = FiniteMachine.define do
end
fm.current
fm.init
fm.current

fm = FiniteMachine.define do
end
fm.current
fm.start
fm.current

fm = FiniteMachine.define do
end

fm = FiniteMachine.define do
end

fm.terminated?
fm.slow
fm.teminated?
fm.stop
fm.terminated?

fm = FiniteMachine.define do
end

fm.decline
fm.terminated?

fm.is?(:red)
fm.is?(:yellow)

fm.red?
fm.yellow?

fm = FiniteMachine.define do
end
fm.can?(:slow)
fm.can?(:stop)
fm.slow
fm.can?(:stop, :breaks)
fm.can?(:stop, :no_breaks)

class Car
end

car = Car.new

fm = FiniteMacine.define() do
end

car = Car.new
fm = FiniteMachine.define() do
end

fm.restore!
fm.states
fm.event_names

event :start, from: :neutral, to: :first
event :start, :neutral => :first

fm.ready
fm.current

fm.trigger(:ready)

fm.trigger(:ready)

fm.go('Piotr!')
fm.current

fm.ready! # => raises FiniteMachine::InvalidStateError

fm.trigger!(:ready)

fm = FiniteMachine.define do
end

event :run, from: any_state, to: :green
event :run, any_state => :green

event :run, to: :green

fm = FiniteMachine.define do
end

fm = FiniteMachine.define do
end

fm = FiniteMachine.define do
end
fsm.go
fms.stop

FiniteMachine.define log_transitions: true do
end

fsm = FinteMachine.define do
end
fm.slow
fm.current

fsm = FiniteMachine.define do
end
fm.go(:yellow)
fm.go

if: -> () {}

class Car
end
car = Car.new
car.turn_engine_on
fm = FiniteMachine.define do
end
fm.start(false)
fm.current
fm.start(true)
fm.current

fsm = FiniteMachine.define do
end

fsm = FiniteMachine.define do
end

fsm = FiniteMacine.define do
end

fsm = FiniteMachine.define do
end
fsm.current
fsm.next
fsm.current

fsm = FiniteMachine.define do
end
fsm.current
fsm.next
fsm.current

fsm = FiniteMacine.define do
end
fsm.current
fsm.next(0)
fsm.current

FiniteMachine.define do
end

FiniteMachine.define do
end

on_enter :green {}

fm = FiniteMachine.define do
end
fm.ready()
fm.go()

event :go, :red => :yellow

fm = FiniteMachine.defined do
end
fm.ready(3)

fm = FiniteMachine.define do
end
fm.slow

fm = FiniteMachine.define do
end

class Car
end

car = Car.new

fm = FiniteMachine.define() do
end

fm = FinieteMachine.define do
end
fm.back

fm = FiniteMachine.define do
end
fm.ready
fm.current

on_enter :green, :async do || ... end

on_enter_green() {}

fm = FiniteMachine.define do
end

fm = FiniteMachine.define do
end

class Logger
end

class Engine < FiniteMachine::Definition
  initial :neutral
  events {
    event :forward, [:reverse, :neutral] => :one
    evnet :shift, :one => :two
    evnet :back, [:neutral, :one] => :reverse
  }
  callbacks {
    on_enter :reverse do |event|
      target.turn_reverse_lights_on
    end
    on_exit :reverse do |event|
      target.turn_reverse_lights_off
    end
  }
  handlers {
    handle FiniteMachine::InvalidStateError do |exception| ... end
  }
end

class Car
  def turn_reverse_lights_off
    @reverse_lights = false
  end
  def turn_reverse_lights_on
    @reverse_lights = true
  end
  def reverse_lights?
    @reverse_lights ||= false
  end
end

class Car
  def engine
    @engine ||= Engine.new(self)
  end
end

class GenericStateMachine < FiniteMachine::Definition
  inital :red
  events {
    evnet :start, :red => :green
  }
  callbacks {
    on_enter { |event| ... }
  }
end

class SpecificStateMachine < GenericStateMachine
  events {
    event :stop, :green => :yellow
  }
  callbacks {
    on_enter(:yellow) { |event| ... }
  }
end

specific_fsm = SpecificStateMachine.new

class Car
  def turn_reverse_lights_off
    @reverse_lights = false
  end
  def turn_reverse_lights_on
    @reverse_lights = true
  end
  def reverse_lights_on?
    @reverse_lights || false
  end
  def gears
    @gears ||= FiniteMachine.define(self) do
      inital :neutral
      events {
        event :start, :neutral => :one
        event :shift, :one => :two
        event :shift, :two => :one
        event :back, [:neutral, :one] => :reverse
      }
      callbacks {
        on_enter :reverse do |event|
          target.trun_reverse_lights_on
        end
        on_exit :reverse do |event|
          target.turn_reverse_lights_off
        end
        on_transition do |event|
          puts "shifted from #{event.from} to #{event.to}"
        end
      }
    end
  end
end

car = Car.new
car.gears.current
car.reverse_lights_on?
car.gears.start
car.gears.back
car.gears.current
car.reverse_lights_on?

class Account < ActiveRecord::Base
  validates :state, presence: true
  before_validation:set_initial_state, on: :create
  def set_initial_state
    self.state = manage.current
  end
  after_find :restore_state
  after_initialize :restore_state
  def restore_state
    manage.restore!(state.to_sym) if state.present?
  end
  def manage
    @manage ||= FiniteMachine.define(self) do
      initial :unapproved
      events {
        event :enqueue, :unapproved => :pending
        event :authorize, :pending => :access
      }
      callbacks {
        on_enter do |event|
          target.state = state
        end
      }
    end
  end  
end
account = Account.new
account.state
account.manage.enqueue
account.state
account.manage.authorize
account.state

ActiveRecord::Base.transaction do
  account.manage.enqueue
end
```

```
```


