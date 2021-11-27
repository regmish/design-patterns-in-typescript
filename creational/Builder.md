*Builder Pattern helps us construct a complex object with varying configuration step by step using the same construction code*

### Problem

Let us suppose, we are creating a complex pricing library, that takes into consideration of a lot of parameters while calculating the final price of a product like

- Buyer type (Premium vs regular buyer)
- Buyer's location
- Shipping method
- Return policy

The solution can be modeled something like

```typescript
class Pricing {
  private _price: number;
  constructor({
    price: number;
    buyerType: string
    buyerLocation: string;
    shippingMethod: string;
    returnPolicy: boolean;
  }) {
    this._price = price;
    if(buyerType !== PREMIUM) {
      this._price += SHIPPING_CHARGE
    }

    if(helper.locationInExtraCharge(buyerLocation)) {
      this._price += EXTRA_LOCATION_SURCHARGE;
    }

    if(shippingMethod === DHL) {
      this._price += DHL_PACKAGING_CHARGE;
    }

    if(returnPolicy) {
      this._price += RETURN_CHARGE;
    }
  }

  get price():number {
    return this._price;
  }
}
```

Sample Usage would be

```typescript
const price = new Pricing({
  price: 100,
  buyerType: "regular",
  buyerLocation: "Germany",
  shippingMethod: "Hermes",
  returnPolicy: false,
}).price;
```

This solution might work pretty well. But once we start adding more and more options/criteria to the pricing logic. Our constructor become bulky with unnecessary constructor params that might not be relevant for a lot of the product we ship.

### Solution

Let's see how we can overcome this situation using a Builder Pattern to construct our pricing object

```typescript
class PriceBuilder {
  private _price: number;

  constructor(price: number) {
    this._price = price;
  }

  applyPremiumShipping(): PriceBuilder {
    this._price += SHIPPING_CHARGE;
  }

  applyLocationCharge(): PriceBuilder {
    this._price += helper.locationInExtraCharge(buyerLocation);
  }

  applyShippingExtra(location: string): PriceBuilder {
    this._price += DHL_PACKAGING_CHARGE;
  }

  applyRefundCharge(): PriceBuilder {
    this._price += RETURN_CHARGE;
  }

  get price(): number {
    return this._price;
  }
}
```

Now we have a thin constructor and a custom methods which can be called based on needs.

```typescript
// Let us apply only premium shipping and return charge
const price = new PriceBuilder(100)
  .applyPremiumShipping()
  .applyRefundCharge().price;

// Similarly we can apply only Shipping Surcharge
const price = new PriceBuilder(100)
  applyLocationCharge("Germany").price;
```
