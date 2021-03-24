Notes RxJS

- Observable: represent a collection of future values or events. 
Subscribing to an Observable is analogous to calling a Function.
- Observer: callback that know how to listen to values delivered by the Observables
- Subscription: execute of the Observable and its cancelling of execution
- Operators: functions to deal with collection
- Subject: multicast a value or event to multiple Observers
- Schedulers: concurrent coordinate computation

- Flow: range of operator to control how events flow through your Observables, as delay, filter, take, debounceTime
- Values: transform values passed through Observables

Pull versus Push
- Pull system: the Consumer determines when it received the data from the Producer. Producer is unaware of when data will be delivered to the Consumer. Every JavaScript function is a Pull system, the function is a Producer of Data, and the code which use the function consumes it by "pulling" out a single returned value.
Producer, passive produce data when requested. Consumer, active decides when data is requested.

- Push system: the Producer determines when to send data to the Consumer. The Consumer I unaware of when it will receive the data.
Promises are a common type of Push system in JavaScript. A Promise (the Producer) delivers a resolved value to a registered callback (the Consumer), if the is Promise deices when that value is "pushed" to the callbacks.
Push, active produces data at is one pace. Passive, reacts to recede data.

- A Function is a lazily evaluated computation that synchronously returns a single value on invocation
- A generator is a lazily evaluated computation that synchronously returns zero to (potentially) infinite values on iteration
- A Promise is a computation that may (or may not) eventually return a single value
- An Observable is a lazily evaluated computation that can synchronously or asynchronously return zero to (potentially) infinite values from the time it's invoked onwards.

Observable:
- Observable are lazy Push collections of multiple values.
- An Observable is a Producer of multiple values, "pushing" them to the Observers (Consumers). Observables have no shared execution.
- Observables are able to deliver values either synchronously or asynchronously.
- Observables can "return" multiple values over time.
- func.call() means "give me one value synchronously"
- observable.subscribe() means "give me any amount of values, either synchronously or asynchronously"
- Observables can be created with `new Observable`. Most commonly, observables are created using creation functions, like of, from, interval, etc.
- Subscribing to an Observable is like calling a function, providing callbacks where the data will be delivered to.
- Subscribe calls are not shared among multiple Observers of the same Observable
- The Observable does not even maintain a list of attached Observers. The Observer is not registered as a listener in the Observable.
- In an Observable Execution, zero to infinite Next notifications may be delivered. If either an Error or Complete notification is delivered, then nothing else can be delivered afterwards.
- It is a good idea to wrap any code in subscribe with try/catch block that will deliver an Error notification if it catches an exception
- Observable Executions may be infinite, if an Observer want to abort in a finite time it must abort the its subscibtion
- When you subscribe, you get back a Subscription, which represents the ongoing execution. Just call unsubscribe() to cancel the execution.
- Each Observable must define how to dispose resources of that execution when we create the Observable using create(), this is done provided an unsubscribe callback in the Observable constructor

Observer
An Observer is a consumer of values delivered by an Observable. Observers are just objects with three callbacks, one for each type of notification that an Observable may deliver.
To use the Observer, provide it to the subscribe of an Observable: `observable.subscribe(observer);`
Observers in RxJS may also be partial. If you don't provide one of the callbacks, the execution of the Observable will still happen normal.

Operators
Allow complex asynchronous code to be easily composed in a declarative manner. There are of two types:

- Pipeable Operators, are the kind that can be piped to Observables using the syntax `observableInstance.pipe(operator())`. When called, they do not change the exiting Observable instead they return a new Observable, whose subscription logic is based on the first observable.
A Pipeable Operator is a function that takes an Observable as its input and returns another Observable. It is a pure operation: the previous Observable stays unmodified.

- Creation Operators, called as standalone functions to create a new Observable, For example: of(1, 2, 3) creates an observable that will emit 1, 2, and 3, one right after another.
Are functions that can be used to create an Observable with some common predefined behavior or by joining other Observables

- Piping
Pipeable operators are functions,

- Higher-order Observables
Observables most commonly emit ordinary values, but surprisingly often, it is necessary to handle Observables of Observables, so-called higher-order Observables. You work with a higher-order Observable by flattening the high order Observable into an regular Observable.

Subscription
A Subscription is an object that represents a disposable resource, usually the execution of an Observable. A Subscription essentially just has an unsubscribe() function to release resources or cancel Observable executions.

Subject
It is a special type of Observable that allows values to be multicasted to many Observers, they maintain a registry of many listeners. Every Subject is an Observer.
While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.
Subjects are the only way of making any Observable execution be shared to multiple Observers.

- Unicast, each subscribed Observer owns an independent execution of the Observable
- Multicast, values to be multicasted to many Observers

Multicasted Observables
A "multicasted Observable" passes notifications through a Subject which may have many subscribers, whereas a plain "unicast Observable" only sends notifications to a single Observer.

BehaviorSubject
Subject which has a motion of the latest value emitted to its consumers, when the Observer subscribes it will immediately receive the current value. BehaviorSubjects are useful for representing "values over time".

ReplaySubject
Similar to BehaviorSubject it records multiple values from the Observable execution and replays them to new subscribers. A window time in milliseconds.

AsyncSubject
Only the last value of the Observable execution is sent to its observers and  and only when the execution completes. It is similar to the last() operator, in that it waits for the complete notification in order to deliver a single value.


Scheduler
A scheduler controls when a subscription starts and when notifications are delivered.
A Scheduler lets you define in what execution context will an Observable deliver notifications to its Observer.
If you do not provide the scheduler, RxJS will pick a default scheduler by using the principle of least concurrency, this means that the scheduler which introduces the least amount of concurrency that satisfies the needs of the operator is chosen.
By default, a subscribe() call on an Observable will happen synchronously and immediately. However, you may delay or schedule the actual subscription to happen on a given Scheduler, using the instance operator subscribeOn(scheduler), where scheduler is an argument you provide.

Read again:
https://rxjs-dev.firebaseapp.com/guide/operators

Operators:

Creation Operators
- ajax
It creates an observable for an Ajax request with either a request object with url, headers, etc or a string for a URL.

- bindCallback
Converts a callback API to a function that returns an Observable. It is not an operator because its input and output are not Observables.

- bindNodeCallback
Converts a Node.js-style callback API to a function that returns an Observable. A function which returns the Observable that delivers the same values the Node.js callback would deliver. It is not also an operator 

- defer
Creates an Observable that, on subscribe, calls an Observable factory to make an Observable for each new Observer. Creates the Observable lazily, that is, only when it is subscribed. defer allows you to create the Observable only when the Observer subscribes, and create a fresh Observable for each Observer, although each subscriber may think it is subscribing to the same Observable, in fact each subscriber gets its own individual Observable.

- empty (deprecated)
Creates an Observable that emits no items to the Observer and immediately emits a complete notification.

- from
Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object. Converts almost anything to an Observable.

- fromEvent
Creates an Observable that emits events of a specific type coming from the given event target. Creates an Observable from DOM events, or Node.js EventEmitter events or others. The event are checked via duck typing, it means that no matter what kind of object you have and no matter what environment you work in, you can safely use fromEvent on that object if it exposes described methods (provided of course they behave as was described above)

- fromEventPattern
Creates an Observable from an arbitrary API for registering event handlers. fromEventPattern allows you to convert into an Observable any API that supports registering handler functions for events. It is similar to fromEvent, but far more flexible.

- generate
generate allows you to create stream of values generated with a loop very similar to traditional for loop. Generates an observable sequence by running a state-driven loop producing the sequence's elements.

- interval
Creates an Observable that emits sequential numbers every specified interval of time, on a specified SchedulerLike.

- of
Converts the arguments to an observable sequence. Each argument becomes a next notification.

- range
Creates an Observable that emits a sequence of numbers within a specified range.

- throwError
Creates an Observable that emits no items to the Observer and immediately emits an error notification. Just emits 'error', and nothing else.

- timer
Creates an Observable that starts emitting after an dueTime and emits ever increasing numbers after each period of time thereafter. Its like interval, but you can specify when should the emissions start.

- iif
Decides at subscription time which Observable will actually be subscribed. If statement for Observables. iif accepts a condition function and two Observables. When an Observable returned by the operator is subscribed, condition function will be called. Based on what boolean it returns at that moment, consumer will subscribe either to the first Observable (if condition was true) or to the second.









 


















