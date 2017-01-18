#What's new in 2.3

###New API
* Asynchronous transaction support with [Db.TransactAsync](\guides\transactions\transactions.md#TransactAsync)

###Changed API
* Db.Transact() family is redesigned to be a thin wrapper around Db.TransactAsync(), that effectively just calls Db.TransactAsync() and synchronously waits for returned Task. Thus Db.Transact() becomes a blocking call that waits for IO on write transactions. Take a look at the corresponding section at [Synchronous vs Asynchronous transactions](\guides\transactions\more-on-transactions.md#sync_vs_async) for reasoning and possible performance implications.

###Obsoleted API
* Overloads of Db.Transact that accept additional arguments for user delegate are marked obsolete. Consider using capturing of local variables instead.

```cs
[Obsolete] Db.Transact<T>(Action<T> action, T arg, ...);
[Obsolete] TResult Db.Transact<T, TResult>(Func<T, TResult> func, T arg, ...);
``` 
