_Adapter is a structural design pattern that allows objects with incompatible interfaces to collaborate._

### Problem

Let us suppose we have two database system. One is MongoDB, which supports document based data and another is MySQL which is more tabular.
Now we need to have a adapter system that takes the data from MySQL as input and outputs them into a format that mongodb understands.
So that we can closely work in two different system.

eg. MySQL

```
| id  | chargeAmount | commissionAmount | customerName | customerEmail | chargeDate | notifyDate |  status    |
| --- | ------------ | ---------------- | ------------ | ------------- | ---------- | ---------- | ---------- |
| 123 |   1000       |    100           | John Doe     | john@doe.com  | 2021-01-05 | 2021-01-01 | PENDING    |
```

Now we want to convert this into a JSON format that

```JSON
{
  "_id": "123",
  "amount": {
    "charge": 1000,
    "commission": 100
  },
  "customer": {
    "name": "John Doe",
    "email": "john@doe.com",
  },
  "date": {
    "charge": ISODate("2021-01-05T00:00:00Z"),
    "notify": ISODate("2021-01-01T00:00:00Z"),
  },
  "status": "PENDING"
}
```

```typescript
export interface ITarget {
  _id: string;
  amount: { charge: number; commission: number };
  customer: { name: string; email: string };
  date: { charge: Date; notify: Date };
  status: "PENDING" | "PROCESSING" | "DONE" | "FAILED"
}

export interface IAdapter {
  format(): ITarget
}
// This class is specific to MySQL which translates data from MYSQL to target JSON
class MySQLAdapter implements IAdapter {
  constructor(payment: MYSQLPayment) {
    this.payment = payment;
  }

  format(): ITarget {
    const target: ITarget = {
      _id: this.payment.id,
      amount: {
        charge: this.amount.chargeAmount,
        commission: this.amount.commissionAmount
      },
      date: {
        charge: this.payment.chargeDate,
        notify: this.payment.notifyDate
      }
      status: this.payment.status
    };

    return target;
  }
}
```
In a similar fashion, we can write adapter classes which implements `IAdapter` interface if we need to support multiple sources

Usage Example

```typescript
const payment: MYSQLPayment = mysqlService.getPayment(123); // get payment from MYSQL

const adapter = new MySQLAdapter(payment);
const target: ITarget = adapter.format();

// Now we can insert into Mongo 
mongoService.insert(target);

```
