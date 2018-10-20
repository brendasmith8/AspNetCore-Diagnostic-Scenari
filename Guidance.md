# Common Pitfalls writing scalable services in ASP.NET Core

This document serves as a guide for writing scalable services in ASP.NET Core. Some of the guidance is general purpose but will be explained through the lens of writing 
web services. The examples shown here are based on experiences with customer applications and issues found on Github and Stack Overflow. Besides general guidance,
this repository will have guides on how to diagnose common issue in the various tools available (WinDbg, lldb, sos, Visual Studio, PerfView etc).

## Asynchronous Programming

Asynchronous programming has been around for several years on the .NET platform but has historically been very difficult to do well. Since the introduction of async/await
in C# 5 asynchronous programming has become mainstream. Modern frameworks (like ASP.NET Core) are fully asynchronous and it's very hard to avoid the async keyword when writing
web services. As a result, there's been lots of confusion on the best practices for async and how to use it properly. Lets start with some of the basic rules:

### Asynchrony is viral 

Once you go async, all of your callers **MUST** be async, there's no good way gradually migrate callers to be async. It's all or nothing (very much like generics).

✔️**GOOD**

```C#
public async Task<int> DoSomethingAsync()
{
    var result = await CallDependencyAsync();
    return result + 1;
}
```

This example uses the await keyword to get the result from `CallDependencyAsync`.

❌ **BAD**

```C#
public async int DoSomethingAsync()
{
    var result = CallDependencyAsync().Result
    return result + 1;
}
```

This example uses the `Task.Result` and as a result blocks the current thread to wait for the result. This is an example of [sync over async](#sync-over-async) (more on this later).

### Async void

### Always await methods that return tasks

### Prefer await over ContinueWith

### Always create TaskCompletionSource with TaskCreationOptions.RunContinuationsAsynchronously

