<!-- TOC -->
* [Result Monad](#result-monad)
  * [Usage](#usage)
    * [Creating a Result](#creating-a-result)
    * [Checking the Result](#checking-the-result)
    * [Mapping Values](#mapping-values)
    * [Using ifSuccess() and ifFailure()](#using-ifsuccess-and-iffailure)
    * [Flat Mapping](#flat-mapping)
    * [Getting a Value](#getting-a-value)
<!-- TOC -->

# Result Monad

`Result` is a monad class that represents the outcome of an operation, which can either be a success or a failure. It is designed to handle scenarios where an operation might either return a valid result or an error, allowing for functional error handling without exceptions.

## Usage

### Creating a Result

To create a `Result` instance, you can use the static factory methods `success()` and `failure()`:

```java
public Result<Ticket, Error> buyTicket() {
  if (allSlotsOccupied()) {
    return Result.failure(new Error("All slots occupied"));
  }
  return Result.success(new Ticket());
}
```

### Checking the Result
You can check whether a Result is a success or a failure:

```java
Result<Ticket, Error> result = buyTicket();
if(result.isSuccess()){
    logger.info("The ticket has been bought!");
}
```

### Mapping Values
You can map the success or failure values to new types using `mapSuccess()` and `mapFailure()`. The mapping is applied based on whether the Result is a success or failure:

```java
Result<Parcel, Notification> result = buyTicket()
        .mapSuccess(ticket -> createParcel(ticket))
        .mapFailure(error-> createNotificationMail(error));
```

### Using ifSuccess() and ifFailure()
You can execute actions based on whether the Result is a success or a failure:

```java
Result<Parcel, Notification> result = buyTicket()
        .mapSuccess(ticket -> createParcel(ticket))
        .mapFailure(error-> createNotificationMail(error))
        .ifSuccess(parcel -> logger.info("Parcel ready for delivery"))
        .ifFailure(notification -> logger.info("Notification ready for delivery"));
```

### Flat Mapping
Flat mapping allows you to chain operations that return `Result` instances:

```java
Result<Parcel, Error> createParcel(Ticket ticket) {
  if (isOutOfStamps()) {
    return Result.failure(new Error("There are no more stamps"));
  }
  return Result.success(new Parcel(ticket));
}

Result<Parcel, Error> result = buyTicket()
        .flat(ticket -> createParcel(ticket), error -> error);
```

### Getting a Value
You can extract a value from a Result by providing a mapper for both the success and failure cases:

```java
String message = successResult.get(success -> "Success: " + success, failure -> "Failure: " + failure);
```
