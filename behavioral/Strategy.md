*Strategy pattern is a behavioral design pattern that enables selecting a suitable algorithm at runtime. Instead of implementing a single algorithm directly, code receives run-time instructions as to which in a family of algorithms to use*


Strategy problem can be thought of an idea where any task can be accomplished using multiple Strategies (Algorithms) and the client is responsible for choosing the Strategy(Algorithm) at runtime

### Problem

Let us suppose we have different Bonus Scheme an employee can have based on his years of experience/department/Heirarchy and so on.


Let us start by defining a `Scheme` Interface that every Strategy (Algorithm) has to satisfy in order to be injected into the Context during runtime.

Each concrete implementation of `Scheme` must contain an algorithm (method) that supports some behavior of the context Class.

```typescript
interface Scheme {
    applyBonus(salary: number): number;
}
```

Now we define a Context Class, that exposes an API to the client, 
where client
1. Set a strategy (via a constructor or `setStrategy` method during runtime)
2. Execute a method in the Strategy Class (sort of proxy method that delegates task to selected Strategy Class)

```typescript
class BonusContext {
    private scheme: Scheme // Bonus Scheme

    constructor(scheme: Scheme) { // Client will inject whatever scheme it wants to use during runtime
        this.scheme = scheme;
    }

    setScheme(scheme: Scheme) {
        this.scheme = scheme; // For changing it on runtime
    }

    // This function will simply delegate the task to appropriate scheme client selected
    // rather than doing bunch of if..else check and implementing on it's own
    public calculateBonus(emp: Employee): number {
        const { salary } = emp;
        return this.scheme.applyBonus(salary);
    }
}
```
Let's define some concrete implementations of `Scheme`

```typescript
// Default Bonus (As a 5th Anniversary of the company we want to give 100EUR to all employees)
class DefaultBonus implements Scheme {
    applyBonus(salary: number): number {
        return salary + 100;
    }
}
```

```typescript
// All those employees getting Equity bonus will get 20% of the salary(just an example :D)
class EquityBonus implements Scheme {
    applyBonus(salary: number): number {
        return (salary + 0.2 * salary)
    }
}
```


```typescript
// Employee of the month will get 1000 flat added 
class EmployeeOfTheMonthBonus implements Scheme {
    applyBonus(salary: number): number {
        return salary + 1000;
    }
}
```

```typescript
// Exceptionally performing employee will get 100% bonus, that's crazy :D
class ExceptionalPerformanceBonus implements Scheme {
    applyBonus(salary: number): number {
        return salary * 2;
    }
}
```

### Usage
Now Let's give some bonus

```typescript
const employees = Employee.find();
let bonusContext = new BonusContext(new DefaultBonus());
for(employee of employees) {
    // based on some condition during runtime we apply different bonus to different employee
    if(employee.isExceptional) {
        bonusContext.setScheme(new ExceptionalPerformanceBonus());
    }

    if(employee.isCLevel) {
        bonusContext.setScheme(new EquityBonus());
    }

    employee.setTotalSalary(employee.salary, bonusContext.applyBonus(employee));
}
```



