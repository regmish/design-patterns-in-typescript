*Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.*


### Problem

Let us suppose we have one type of payment provider implemented in our system, ***Stripe***.
As our company is expanding in more regions and we need to support multiple payment methods, our company wants to use ***Paypal***.

```typescript
// This is tightly coupled code with business logic tighly tied up with Stripe as a payment service provider

class Payment {
    createPayment(amount: number) {
        stripe.charge(amount) // Tight coupling with stripe method
    }

    updatePayment(amount: number) {
        stripe.update(amount: number) // Tight coupling with stripe method
    }

    deletePayment(amount: number) {
        stripe.delete(amount) // Tight coupling with stripe method
    }

    notifyPayment(customer: Customer) {
        mandril.sendEmail(customer) // Another tight coupling with Emails Service Provider
    }
}
```

 Now let's add Paypal in our existing code
```typescript

class Payment {
    createPayment(amount: number, method = 'stripe') {
        if(method === 'stripe') {
            stripe.charge(amount)
        } else if(method === 'paypal') {
            paypal.charge(amount)
        }
    }

    updatePayment(amount: number, method = 'stripe') {
        if(method === 'stripe') {
            stripe.update(amount)
        } else if(method === 'paypal') {
            paypal.update(amount)
        }
    }

    deletePayment(amount: number, method = 'stripe') {
        if(method === 'stripe') {
            stripe.delete(amount)
        } else if(method === 'paypal') {
            paypal.delete(amount)
        }
    }

    notifyPayment(customer: Customer) {
        mandril.sendEmail(customer)
    }
}
```

Clearly, we can see that this solution is not scaling, as we keep on adding new payment method we will be making our code more mess by introducing more `if...else...` blocks, which will result in very hard to maintain code.

### Solution

```typescript
interface PaymentProvider {
    comissionRate: number;
    createPayment(amount: number): string;
    updatePayment(amount: number): string;
    deletePayment(amount: number): string;
    notifyPayment(customer: Customer): void
}

/** Paypal specific methods for handling payments */
class PaypalPaymentProvider implements PaymentProvider {
    comissionRate = 0.05; // This might be overwritten by the factory
    createPayment(amount: number): string {
        return `Payment of ${amount} created`;
    }
    updatePayment(amount: number): string {
        return `Payment of ${amount} updated`;
    }
    deletePayment(amount: number): string {
        return `Payment of ${amount} deleted`;
    }
    notifyPayment(customer: Customer): void {
        console.log(`Payment of ${customer.name} notified`);
    }
}

/** Stripe specific methods for handling payments */
class StripePaymentProvider implements PaymentProvider {
    comissionRate = 0.05; // This might be overwritten by the factory
    createPayment(amount: number): string {
        return `Payment of ${amount} created`;
    }
    updatePayment(amount: number): string {
        return `Payment of ${amount} updated`;
    }
    deletePayment(amount: number): string {
        return `Payment of ${amount} deleted`;
    }
    notifyPayment(customer: Customer): void {
        console.log(`Payment of ${customer.name} notified`);
    }
}


/**
 * The creator class declares the factory method that is supposed to return an object of a Product class
 */
abstract class PaymentFactory {
    /**
     * Subclass will override this method to return an object of a PaymentProvider class
     */
    public abstract factoryMethod(): PaymentProvider;

    /**
     * In addition to factoryMethod, this class might contains some core business logic
     * that operates on PaymentProvider objects
     */
    public updateCommission(): void {
        const provider = this.factoryMethod();
        provider.comissionRate = 0.05;
    }
}


// Concrete Classes
class PaypalPayment extends PaymentFactory {
    public factoryMethod(): PaymentProvider {
        return new PaypalPaymentProvider();
    }
}

class StripePayment extends PaymentFactory {
    public factoryMethod(): PaymentProvider {
        return new StripePaymentProvider();
    }
}



// Usage

function getPaymentProvider(method): instance of {
    switch method {
        case 'paypal':
            return new PaypalPayment();
        calse 'stripe':
            return new StripePayment()
    }
}