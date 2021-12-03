*Observer pattern is a behavioral design pattern in which an object, named the Subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.*

Not to be confused with Pub/sub pattern

```
| ----------------------------------------------------------------------------------------------------------- |
|                  Observer Pattern                    |               Pub/Sub Pattern                        |
| ----------------------------------------------------------------------------------------------------------- |
|                                                                                                             |
|                  Mostly Synchronous                  |               Mostly ASynchronous                    |
| ----------------------------------------------------------------------------------------------------------- |
|                                                      |                                                      |
|            Observers are aware of Observable         |               Publisher and subscriber               |
|                                                      |               don't need to know each other          |
| ----------------------------------------------------------------------------------------------------------- |
```

Components of Observer pattern
1. Subject/Observable
2. Objservers

### Problem
Suppose we have a payment system (Subject) and after the payment is successfull, we want to perform certain actions which are in different services (Observers).

For e.g After a successfull payment
* Send Email to customer (**Email Service**)
* Create a invoice (**Invoie Service**)
* Confirm the Order (**Order Service**)
* Manage Inventory (**Inventory Service**)


### Solution

Let us start by defining an interface for Observer and  Observable
```typescript
interface IObserver {
    notify(data: any): void;
}

interface IObjservable {
    attach(observer: IObserver): void;
    detach(observer: IObserver): void;
    notify(data: any): void;
}
```

Once we have the interface we can define our Payments Observable, that notifies Email, Order and other observer that our attached to it.

```typescript
class PaymentObservable implementa IObjservable {
    private observers: Map<IObserver, IObserver> = new Map();

    attach(observer: IObserver) {
        if(!this.observers.has(observer)) {
            this.observers.set(observer, observer);
        } else {
            console.log('Alreay attached');
        }
    }

    detach(observer: IObserver) {
        if(this.observers.has(observer)) {
            this.observers.delete(observer, observer);
        } else {
            console.log('Alreay attached');
        }
    }

    async notify(data: any) {
        for(observer of this.observers.values()) {
            observer.notify(data);
        }
    }


    // let us suppose we have a charge method that calls our notify on success
    async charge(someChargeData: any) {
        const resutl = await somePaymentService.createCharge(someCharge);

        if(result.status === 200) {
            this.notify(result);
        }
    }
}
```


Now we can create as many observer as we want and observe the PaymentObservable to get notified on charge Success

```typescript
class EmailObserver implements IObserver {
    async notify(data: any) {
        if(data.success) {
            await this.sendSucessEmail(data);
        } else {
            await this.sendFailureEmail(data);
        }
    }

    async sendSucessEmail() {
        console.log('Sending success email');
    }

    async sendFailureEmail() {
        console.log('Sending failure email');
    }
}
```


### Usage


```typescript
function Main() {
    const paymentObservable = new PaymentObservable();
    cosnt emailObserver = new EmailObserver();

    paymentObservable.attach(emailObserver);


    // trigger a charge
    paymentObservable.charge({amount: 100}); // will trigger emailObserver.notify
}
```

