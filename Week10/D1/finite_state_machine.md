# AASM
# Finite state Machines

## What is a Finite state machine

A finite-state machine (FSM) or finite-state automaton (plural: automata), or simply a state machine, is a mathematical model of computation used to design both computer programs and sequential logic circuits. It is conceived as an abstract machine that can be in one of a finite number of states. 

The machine is in only one state at a time; the state it is in at any given time is called the current state. It can change from one state to another when initiated by a triggering event or condition; this is called a transition. A particular FSM is defined by a list of its states, and the triggering condition for each transition.

The behavior of state machines can be observed in many devices in modern society which perform a predetermined sequence of actions depending on a sequence of events with which they are presented. Simple examples are vending machines which dispense products when the proper combination of coins is deposited, elevators which drop riders off at upper floors before going down, traffic lights which change sequence when cars are waiting, and combination locks which require the input of combination numbers in the proper order.

## The Door 

###States

- open 
- closed
- hitting the aircon

### Events 

- opening
- closing
- opening badly
- correcting your mistake


## Why are they useful

In addition to their use in modeling reactive systems presented here, finite state automata are significant in many different areas, including electrical engineering,linguistics, computer science, philosophy, biology, mathematics, and logic. Finite state machines are a class of automata studied in automata theory and the theory of computation. In computer science, finite state machines are widely used in modeling of application behavior, design of hardware digital systems, software engineering, compilers, network protocols, and the study of computation and languages.

## AASM

"AASM" is the "Acts As State Machine" gem, which is very frequently used as _the_ state machine in ruby.

Install the assm gem: `gem install aasm`

Create a new Job class:

    require 'pry'
    require 'aasm'

    class Job
      include AASM

      aasm do
        state :sleeping, :initial => true
        state :running
        state :cleaning

        event :run do
          transitions :from => :sleeping, :to => :running
        end

        event :clean do
          transitions :from => :running, :to => :cleaning
        end

        event :sleep do
          transitions :from => :running, :to => :sleeping, :guard => :cleaning_needed?
        end
      end

      def cleaning_needed?
        puts "checking if it needs cleaning..."
        false
      end

    end

    binding.pry


Then play in the console:

    j = Job.new

    j.aasm.current_state
    => :sleeping

    j.run
    => true

    j.running?
    => true

    j.aasm.current_state
    => :running

    j.run
    => AASM::InvalidTransition: Event 'run' cannot transition from 'running'

    j.sleep
    => AASM::InvalidTransition: Event 'sleep' cannot transition from 'running'
    # because the `cleaning_needed?` method is returning false


<https://github.com/aasm/aasm