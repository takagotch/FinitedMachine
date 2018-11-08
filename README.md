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
  events {
    event :init, :none => :green
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
end
fm.current
fm.init
fm.current

fm = FiniteMachine.define do
  initial :green
  events {
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
end
fm.current

fm = FiniteMachine.define initial: :green do
end

fm = FiniteMachine.define do
  initial :green, defer: true
  events{
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
end
fm.current
fm.init
fm.current

fm = FiniteMachine.define do
  initial :green, event: start, defer: true
  events {
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
end
fm.current
fm.start
fm.current

fm = FiniteMachine.define do
  initial :green
  terminal :red
  events{
    event :slow, :green => :yellow
    event :stop, :yellow => :red
    event :go, :red => :green
  }
end

fm = FiniteMachine.define do
  initial :green
  terminal :red
  events {
    event :slow, :green => :yellow
    event :stop, :yellow => :red
    event :go, :red => :green
  }
end

fm.terminated?
fm.slow
fm.teminated?
fm.stop
fm.terminated?

fm = FiniteMachine.define do
  initial :open
  terminal :close, :canceled
  events {
    event :resolve, :open => :close
    event :decline, :open => :canceled
  }
end

fm.decline
fm.terminated?

fm.is?(:red)
fm.is?(:yellow)

fm.red?
fm.yellow?

fm = FiniteMachine.define do
  initial :green
  events {
    event :slow, :green => :yellow
    event :stop, :yellow => :red, if: proc { |_, param| :breaks == param }
  }
end
fm.can?(:slow)
fm.can?(:stop)
fm.slow
fm.can?(:stop, :breaks)
fm.can?(:stop, :no_breaks)

class Car
  def initialize
    @engine_on = false
  end
  def turn_engine_on
    @engine_on = true
  end
  def turn_engine_off
    @engine_on = false
  end
  def engine_on?
    @engine_on
  end
end

car = Car.new

fm = FiniteMacine.define(car) do
  initial :neutral
  events {
    event :start, :neutral => :one, if: "engine_on?"
    event :stop, :one => :neutral
  }
  callbacks {
    on_enter_start do |event| target.turn_engine_on end
    on_exit_start do |event| target.turn_engine_off end
  }
end

car = Car.new
fm = FiniteMachine.define(car, alias_target: :car) do
  initial :neutral
  events {
    event :start, :neutral => :one, if: "engine_on?"
  }
  callbacks {
    on_enter_start do |event| car.turn_engine_on end
    on_exit_start do |event| car.turn_engine_off end
  }
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
  initial :neutral
  events {
    event :start, :neutral => :one
    event :shift, :one => :two
    event :shift, :two => :three
    event :shift, :three => :four
    event :slow, [:one, :two, :three] => :one
  }
end

event :run, from: any_state, to: :green
event :run, any_state => :green

event :run, to: :green

fm = FiniteMachine.define do
  initail :initial
  events {
    event :bump, :initial => :low,
                 :low => :medium,
                 :medium => :high
  }
end

fm = FiniteMachine.define do
  initial :initial
  events {
    event :bump, :initial => :low
    event :bump, :low => :medium
    event :bump, :medium => :high
  }
end

fm = FiniteMachine.define do
  initial :yellow
  events {
    event :go :yellow => :green, slient: true
    evnet :stop, :green => :red
  }
end
fsm.go
fms.stop

fm = FiniteMachine.define do
  initial :green
  events {
    event :slow, :green => :yellow, if: -> { return false }
  }
end
fm.slow
fm.current

fsm = FiniteMachine.define do
  initial :red
  events {
    event :go, :red => :green,
          if: -> (context, a){ context.current == a }
  }
end
fm.go(:yellow)
fm.go

if: -> (context, *args) { ... }

class Car
  attr_accessor :engine_on
  def turn_engine_on
    @engine_on = true
  end
  def turn_engine_off
    @engine_on = false
  end
  def engine_on?
    @engine_on
  end
end
car = Car.new
car.turn_engine_on
fm = FiniteMachine.define do
  initial :neutral
  target car
  events {
    event :start, :neutral => :one, if: -> (target, state) {
      target.engine_on = state
      target.engine_on?
    }
  }
end
fm.start(false)
fm.current
fm.start(true)
fm.current

fsm = FiniteMachine.define do
  initial :neutral
  target car
  events {
    event :start, :neutral => :one, if: :engine_on?
  }
end

fsm = FiniteMachine.define do
  initial :neutral
  target car
  events {
    event :start, :neutral => :one, if: "engine_on?"
  }
end

fsm = FiniteMacine.define do
  initial :green
  events {
    event :slow, :green => :yellow,
      if: [ -> { return true }, -> { return true} ],
      unless: -> { return true }
    event :stop, :yellow => :red
  }
end

fsm = FiniteMachine.define do
  initial :green
  events {
    event :next, :green => :yellow, if: -> { false }
    event :next, :green => :red, if: -> { true }
  }
end
fsm.current
fsm.next
fsm.current

fsm = FiniteMachine.define do
  initial :green
  events {
    event :next, from: :green do
      choice :yellow, if: -> { false }
      choice :red, if: -> { true }
    end
  }
end
fsm.current
fsm.next
fsm.current

fsm = FiniteMacine.define do
  initial :green
  events {
    event :next, from: :green do
      choice :yellow, if: -> (context, a) { a < 1 }
      choice :red, if: -> (context, a) { a > 1 }
      default :red
    end
  }
end
fsm.current
fsm.next(0)
fsm.current

FiniteMachine.define do
 initial :red
 events {
   event :next, from: [:yellow, :red] do
     choice :pink, if: -> { false }
     choice :green
   end
 }
end

FiniteMachine.define do
  initial :red
  events {
    event :next, from: :any do
      choice :pink, if: -> { false }
      choice :green
    end
  }
end

on_enter :green { |enent, a, b, c| ... }

fm = FiniteMachine.define do
  initial :red
  events {
    event :ready, :red => :yellow
    event :go, :yellow => :green
    event :stop, :green => :red
  }
  callbacks {
    on_before :ready { |event, time1, time2, time3| puts "#{time1} #{time2} #{time3} Go!" }
    on_before :go { |event, name| puts "Going fase #{name}" }
    on_before :stop { |event| ... }
  }
end
fm.ready(1, 2, 3)
fm.go('Piotr!')

event :go, :red => :yellow

fm = FiniteMachine.defined do
  initial :red
  evnets {
    event :ready, :red => :yellow
  }
  callbacks {
    on_enter_ready { |event, time|
      puts "lights switching from #{event.from} to #{evnet.to} in #{time} seconds"
    }
  }
end
fm.ready(3)

fm = FiniteMachine.define do
  initial :green
  events {
    event :slow, :green => :yellow
  }
  callbacks {
    on_enter(:yellow) { this_is_run_first }
    on_enter(:yellow) { then_this }
  }
end
fm.slow

fm = FiniteMachine.define do
  initial :red
  events {
    event :ready, :red => :yellow
    event :go, :yellow => :green
    event :stop, :green => :red
  }
  callbacks {
    on_before_ready { |event| ... }
    on_before_go { |event| ... }
    on_before_stop { |event| ... }
  }
end

class Car
  attr_accessor :reverse_lights
  def turn_reverse_lights_off
    @reverse_lights = false
  end
  def turn_reverse_lights_on
    @reverse_lights = true
  end
end

car = Car.new

fm = FiniteMachine.define(car) do
  initial :neutral
  events{
    event :forward, [:reverse, :neutral] => :one
    event :back, [:neutral, :one] => :reverse
  }
  callbacks{
    on_enter_reverse { |event| target.turn_reverse_lights)on }
    on_enter_reverse { |event| target.turn_reverse_lights_off }
  }
end

fm = FinieteMachine.define do
  initial :neutral
  events {}
  callbacks {
    on_enter_reverse { |event| forward('Piotr!') }
    on_exit_reverse { |event, name| puts "Go #{name}" }
  }
end
fm.back

fm = FiniteMachine.define do
  initial :red
  events {
    event :ready, :red => :yellow
    event :go, :yellow => :green
    event :stop, :green => :red
  }
  callbacks {
    on_exit :red do |event|
      cancel_event
    end
  }
end
fm.ready
fm.current

on_enter :green, :async do |event| ... end

on_enter_green(:async) { |event| }

fm = FiniteMachine.define do
  initial :red
  events{
    event :ready, :red => :yellow
    event :go, :yellow => :green
    event :stop, :green => :red
  }
end
fm.on_enter_yellow do |event|
end

fm = FiniteMachine.define do
  initial :green, event: :start
  events {
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
  handlers {
    handle FiniteMachine::InvalidStateError do |exception|
      raise exception
    end
    handle FiniteMachine::TransitionError, with: proc { |exception| ... }
  }
end

class Logger
  def log_error(exception)
    puts "Exception : #{exception.message}"
  end
end
fm = FiniteMachine.define(logger) do
  initial :green
  evnets {
    event :slow, :green => :yellow
    event :stop, :yellow => :red
  }
  handlers {
    handle 'InvalidStateError', with: :log_error
  }
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


