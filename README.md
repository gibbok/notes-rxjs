# Notes on RxJS 6.6.7

- Observable: represent a collection of future values or events. 
Subscribing to an Observable is analogous to calling a Function.
- Observer: a callback that knows how to listen to values delivered by the Observables
- Subscription: execute of the Observable and its canceling of execution
- Operators: functions to deal with collection
- Subject: multicast a value or event to multiple Observers
- Schedulers: concurrent coordinate computation

- Flow: range of operator to control how events flow through your Observables, like delay, filter, take, debounceTime
- Values: transform values passed through Observables

Pull versus Push
- Pull system: the Consumer determines when it received the data from the Producer. The producer is unaware of when data will be delivered to the Consumer. Every JavaScript function is a Pull system, the function is a Producer of Data, and the code which uses the function consumes it by "pulling" out a single returned value.
Producer, passive produce data when requested. Consumer, active decides when data is requested.

- Push system: the Producer determines when to send data to the Consumer. The Consumer I unaware of when it will receive the data.
Promises are a common type of Push system in JavaScript. A Promise (the Producer) delivers a resolved value to a registered callback (the Consumer), if the is Promise deices when that value is "pushed" to the callbacks.
Push, actively produces data at is one pace. Passive reacts to recede data.

- A Function is a lazily evaluated computation that synchronously returns a single value on invocation
- A generator is a lazily evaluated computation that synchronously returns zero to (potentially) infinite values on iteration
- A Promise is a computation that may (or may not) eventually return a single value
- An Observable is a lazily evaluated computation that can synchronously or asynchronously return zero to (potentially) infinite values from the time it's invoked onwards.

## Observable

- Observable are lazy Push collections of multiple values.
- An Observable is a Producer of multiple values, "pushing" them to the Observers (Consumers). Observables have no shared execution.
- Observables can deliver values either synchronously or asynchronously.
- Observables can "return" multiple values over time.
- func.call() means "give me one value synchronously"
- observable.subscribe() means "give me any amount of values, either synchronously or asynchronously"
- Observables can be created with `new Observable`. Most commonly, observables are created using creation functions, like of, from, interval, etc.
- Subscribing to an Observable is like calling a function, providing callbacks where the data will be delivered to.
- Subscribe calls are not shared among multiple Observers of the same Observable
- The Observable does not even maintain a list of attached Observers. The Observer is not registered as a listener in the Observable.
- In an Observable Execution, zero to infinite Next notifications may be delivered. If either an Error or Complete notification is delivered, then nothing else can be delivered afterward.
- It is a good idea to wrap any code in subscribing with try/catch block that will deliver an Error notification if it catches an exception
- Observable Executions may be infinite, if an Observer want to abort in a finite time it must abort its subscription
- When you subscribe, you get back a Subscription, which represents the ongoing execution. Just call unsubscribe() to cancel the execution.
- Each Observable must define how to dispose of resources of that execution when we create the Observable using create(), this is done provided an unsubscribe callback in the Observable constructor

## Observer

An Observer is a consumer of values delivered by an Observable. Observers are just objects with three callbacks, one for each type of notification that an Observable may deliver.
To use the Observer, provide it to the subscribe of an Observable: `observable.subscribe(observer);`
Observers in RxJS may also be partial. If you don't provide one of the callbacks, the execution of the Observable will still happen normally.

## Operators
Allow complex asynchronous code to be easily composed in a declarative manner. There are two types:

- Pipeable Operators are the kind that can be piped to Observables using the syntax `observableInstance.pipe(operator())`. When called, they do not change the exiting Observable instead they return a new Observable, whose subscription logic is based on the first observable.
A Pipeable Operator is a function that takes an Observable as its input and returns another Observable. It is a pure operation: the previous Observable stays unmodified.

- Creation Operators, called standalone functions to create a new Observable, For example: of(1, 2, 3) creates an observable that will emit 1, 2, and 3, one right after another.
Are functions that can be used to create an Observable with some common predefined behavior or by joining other Observables

- Piping
Pipeable operators are functions,

- Higher-order Observables
Observables most commonly emit ordinary values, but surprisingly often, it is necessary to handle Observables of Observables, so-called higher-order Observables. You work with a higher-order Observable by flattening the high-order Observable into a regular Observable.

## Subscription
A Subscription is an object that represents a disposable resource, usually the execution of an Observable. A Subscription essentially just has an unsubscribe() function to release resources or cancel Observable executions.

## Subject
It is a special type of Observable that allows values to be multicasted to many Observers, they maintain a registry of many listeners. Every Subject is an Observer.
While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.
Subjects are the only way of making any Observable execution be shared to multiple Observers.

- Unicast, each subscribed Observer owns an independent execution of the Observable
- Multicast, values to be multicasted to many Observers

Multicasted Observables
A "multicasted Observable" passes notifications through a Subject that may have many subscribers, whereas a plain "unicast Observable" only sends notifications to a single Observer.

BehaviorSubject
A subject which has a motion of the latest value emitted to its consumers, when the Observer subscribes it will immediately receive the current value. BehaviorSubjects are useful for representing "values over time".

ReplaySubject
Similar to BehaviorSubject it records multiple values from the Observable execution and replays them to new subscribers. A window time in milliseconds.

AsyncSubject
Only the last value of the Observable execution is sent to its observers and only when the execution completes. It is similar to the last() operator, in that it waits for the complete notification to deliver a single value.

## Scheduler
A scheduler controls when a subscription starts and when notifications are delivered.
A Scheduler lets you define in what execution context will an Observable deliver notifications to its Observer.
If you do not provide the scheduler, RxJS will pick a default scheduler by using the principle of least concurrency, this means that the scheduler which introduces the least amount of concurrency that satisfies the needs of the operator is chosen.
By default, a subscribe() call on an Observable will happen synchronously and immediately. However, you may delay or schedule the actual subscription to happen on a given Scheduler, using the instance operator subscribeOn(scheduler), where the scheduler is an argument you provide.

Read again:
https://rxjs-dev.firebaseapp.com/guide/operators

## Operators:

Creation Operators
- ajax
It creates an observable for an Ajax request with either a request object with url, headers, etc, or a string for a URL.

- bindCallback
Converts a callback API to a function that returns an Observable. It is not an operator because its input and output are not Observables.

- bindNodeCallback
Converts a Node.js-style callback API to a function that returns an Observable. A function that returns the Observable that delivers the same values the Node.js callback would deliver. It is not also an operator 

- defer
Creates an Observable that, on subscribe, calls an Observable factory to make an Observable for each new Observer. Creates the Observable lazily, that is, only when it is subscribed. defer allows you to create the Observable only when the Observer subscribes, and create a fresh Observable for each Observer, although each subscriber may think it is subscribing to the same Observable, in fact, each subscriber gets its individual Observable.

- empty (deprecated)
Creates an Observable that emits no items to the Observer and immediately emits a complete notification.

- from
Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object. Converts almost anything to an Observable.

- fromEvent
Creates an Observable that emits events of a specific type coming from the given event target. Creates an Observable from DOM events, or Node.js EventEmitter events, or others. The event is checked via duck typing, it means that no matter what kind of object you have and no matter what environment you work in, you can safely use fromEvent on that object if it exposes described methods (provided of course they behave as was described above)

- fromEventPattern
Creates an Observable from an arbitrary API for registering event handlers. fromEventPattern allows you to convert into an Observable any API that supports registering handler functions for events. It is similar to fromEvent, but far more flexible.

- generate
generate allows you to create a stream of values generated with a loop very similar to the traditional for loop. Generates an observable sequence by running a state-driven loop producing the sequence's elements.

- interval
Creates an Observable that emits sequential numbers every specified interval of time, on a specified SchedulerLike.

- of
Converts the arguments to an observable sequence. Each argument becomes the next notification.

- range
Creates an Observable that emits a sequence of numbers within a specified range.

- throwError
Creates an Observable that emits no items to the Observer and immediately emits an error notification. Just emits 'error', and nothing else.

- timer
Creates an Observable that starts emitting after an dueTime and emits ever increasing numbers after each period of time thereafter. Its like interval, but you can specify when should the emissions start.

- iif
Decides at subscription time which Observable will actually be subscribed. If statement for Observables. iif accepts a condition function and two Observables. When an Observable returned by the operator is subscribed, the condition function will be called. Based on what boolean it returns at that moment, the consumer will subscribe either to the first Observable (if the condition was true) or to the second.


### Join Operators

- combineAll
Flattens an Observable-of-Observables by applying combineLatest when the Observable-of-Observables completes.

- concatAll
Flattens an Observable-of-Observables by putting one inner Observable after the other.

- exhaust
Converts a higher-order Observable into a first-order Observable by dropping inner Observables while the previous inner Observable has not yet been completed.
An Observable that takes a source of Observables and propagates the first observable exclusively until it completes before subscribing to the next.

- mergeAll
Converts a higher-order Observable into a first-order Observable which concurrently delivers all values that are emitted on the inner Observables.
Flattens an Observable-of-Observables.

- startWith
Returns an Observable that emits the items you specify as arguments before it begins to emit items emitted by the source Observable.
First emits its arguments in order, and then any emissions from the source.

- withLatestFrom
Combines the source Observable with other Observables to create an Observable whose values are calculated from the latest values of each, only when the source emits.
Whenever the source Observable emits a value, it computes a formula using that value plus the latest values from other input Observables, then emits the output of that formula.

### Multicasting Operators

- multicast
Returns an Observable that emits the results of invoking a specified selector on items emitted by a ConnectableObservable that shares a single subscription to the underlying stream.

- publish
Returns a ConnectableObservable, which is a variety of Observable that waits until its connect method is called before it begins emitting items to those Observers that have subscribed to it. Makes a cold Observable hot.

- publishLast
Returns a connectable observable sequence that shares a single subscription to the underlying sequence containing only the last notification.

- share
Returns a new Observable that multicasts (shares) the original Observable. As long as there is at least one Subscriber this Observable will be subscribed and emitting data. When all subscribers have unsubscribed it will unsubscribe from the source Observable. Because the Observable is multicasting it makes the stream hot. This is an alias for multicast(() => new Subject()), refCount().

### Error Handling Operators

- catchError
Catches errors on the observable to be handled by returning a new observable or throwing an error.

- retry
Returns an Observable that mirrors the source Observable with the exception of an error. If the source Observable calls error, this method will resubscribe to the source Observable for a maximum of count resubscriptions (given as a number parameter) rather than propagating the error call.

- retryWhen
Returns an Observable that mirrors the source Observable except for an error. If the source Observable calls error, this method will emit the Throwable that caused the error to the Observable returned from the notifier. If that Observable calls complete or error then this method will call complete or error on the child subscription. Otherwise, this method will resubscribe to the source Observable.

### Utility Operators

- tap
Perform a side effect for every emission on the source Observable, but return an Observable that is identical to the source. Intercepts each emission on the source and runs a function, but returns an output that is identical to the source as long as errors don't occur. tap, therefore, spies on existing execution, it does not trigger an execution to happen like subscribe does.
This operator is useful for debugging your Observables for the correct values or performing other side effects. <<<<<<<<<<<<<<<<

- delay
Delays the emission of items from the source Observable by a given timeout or until a given Date. Time shifts each item by some specified amount of milliseconds.

- delayWhen
Delays the emission of items from the source Observable by a given time span determined by the emissions of another Observable.
It's like delay, but the time span of the delay duration is determined by a second Observable.

- dematerialize
Converts an Observable of Notification objects into the emissions that they represent. Unwraps Notification objects as actual next, error and complete emissions. The opposite of materialize.

- materialize
Represents all of the notifications from the source Observable as next emissions marked with their original types within Notification objects.
Wraps next, error and complete emissions in Notification objects, emitted as next on the output Observable.

- observeOn
Re-emits all notifications from source Observable with specified scheduler. Ensure a specific scheduler is used, from outside of an Observable.
It might be useful if you do not have control over the internal scheduler of a given Observable, but want to control when its values are emitted nevertheless.

- subscribeOn
Asynchronously subscribes Observers to this Observable on the specified SchedulerLike.

- timeInterval
Emits an object containing the current value, and the time that has passed between emitting the current value and the previous value, which is calculated by using the provided scheduler's now() method to retrieve the current time at each emission, then calculating the difference. The scheduler defaults to async, so by default, the interval will be in milliseconds.

- timestamp
Attaches a timestamp to each item emitted by an observable indicating when it was emitted

- timeout
Errors if Observable does not emit a value in given time span. Timeouts on Observable that doesn't emit values fast enough.

- timeoutWith
Errors if Observable does not emit a value in given time span, in case of which subscribes to the second Observable. It's a version of timeout operator that lets you specify fallback Observable.

- toArray
Collects all source emissions and emits them as an array when the source completes. Get all values inside an array when the source completes

### Conditional and Boolean Operators

- defaultIfEmpty
Emits a given value if the source Observable completes without emitting any next value, otherwise mirrors the source Observable.
If the source Observable turns out to be empty, then this operator will emit a default value.

- every
Returns an Observable that emits whether or not every item of the source satisfies the condition specified.
A simple example emitting true if all elements are less than 5, false otherwise

- find
Emits only the first value emitted by the source Observable that meets some condition.
Finds the first value that passes some test and emits that.

- findIndex
Emits only the index of the first value emitted by the source Observable that meets some condition.
It's like find, but emits the index of the found value, not the value itself.

- isEmpty
Emits false if the input observable emits any values, or emits true if the input observable completes without emitting any values.
Tells whether any values are emitted by an observable

Mathematical and Aggregate Operators

- count
Counts the number of emissions on the source and emits that number when the source completes.

- max
The Max operator operates on an Observable that emits numbers (or items that can be compared with a provided function), and when source Observable completes it emits a single item: the item with the largest value.

- min
The Min operator operates on an Observable that emits numbers (or items that can be compared with a provided function), and when source Observable completes it emits a single item: the item with the smallest value.

- reduce
Applies an accumulator function over the source Observable, and returns the accumulated result when the source completes, given an optional seed value.
Combines together all values emitted on the source, using an accumulator function that knows how to join a new source value into the accumulation from the past.
