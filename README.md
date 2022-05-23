# tiny-typed-eventemitter2

This is a fork of [`tiny-typed-emitter`](https://github.com/binier/tiny-typed-emitter) for [`EventEmitter2`](https://github.com/EventEmitter2/EventEmitter2).

Have your events and their listeners type-checked with [no overhead](#no-overhead).

[![npm version](https://badge.fury.io/js/tiny-typed-emitter2.svg)](https://badge.fury.io/js/tiny-typed-emitter2)

## Install
  Simply add the dependency using **npm**:
```console
$ npm i tiny-typed-emitter2
```
  or using **yarn**:
```console
$ yarn add tiny-typed-emitter2
```

## Usage

1. import **tiny-typed-emitter2** library:

  ```ts
  import { TypedEmitter } from 'tiny-typed-emitter2';
  ```

2. define events and their listener signatures (**note:** quotes around event names are not mandatory):
  ```ts
  interface MyClassEvents {
    'added': (el: string, wasNew: boolean) => void;
    'deleted': (deletedCount: number) => void;
  }
  ```

3. on this step depending on your use case, you can:
  - define your custom class extending `EventEmitter`:
    ```ts
    class MyClass extends TypedEmitter<MyClassEvents> {
      constructor() {
        super();
      }
    }
    ```
  - create new event emitter instance:
    ```ts
    const emitter = new TypedEmitter<MyClassEvent>();
    ```

## Generic events interface
To use with generic events interface:

```ts
interface MyClassEvents<T> {
  'added': (el: T, wasNew: boolean) => void;
}

class MyClass<T> extends TypedEmitter<MyClassEvents<T>> {

}
```

## Compatible subclasses with different events

The type of `eventNames()` is a superset of the actual event names to make
subclasses of a `TypedEmitter` that introduce different events type
compatible. For example the following is possible:

```ts
class Animal<E extends ListenerSignature<E>=ListenerSignature<unknown>> extends TypedEmitter<{spawn: () => void} & E> {
  constructor() {
    super();
  }
}

class Frog<E extends ListenerSignature<E>> extends Animal<{jump: () => void} & E> {
}

class Bird<E extends ListenerSignature<E>> extends Animal<{fly: () => void} & E> {
}

const animals: Animal[] = [new Frog(), new Bird()];
```

## Compatible with NestJS
This library is compatible with [`@nestjs/event-emitter`](https://github.com/nestjs/event-emitter) which uses [`EventEmitter2`](https://github.com/EventEmitter2/EventEmitter2) under the hood. To use follow the instructions from:

https://docs.nestjs.com/techniques/events

With the following changes:

1. Use the more specific event type instead of `EventEmitter2`. For example:

```
constructor(private eventEmitter: EventEmitter2) {}
```

becomes:

```
constructor(private eventEmitter: TypedEmitter<MyEvents>) {}
```

2. Don't use the `@OnEvent` method decorator

Instead of doing this:

```
@OnEvent('some.event')
myHandler(event: MyCustomEventType) {}
```

You'll need to do this:

```
constructor(events: TypedEmitter<MyEvents>) {
  events.on('some.event', (event) => {
    // correct event type will automatically be available
  });
}
```

## No Overhead
Library adds no overhead. All it does is it simply reexports renamed `EventEmitter2`
with customized typings.
You can check **lib/index.js** to see the exported code.
