# MessageBox Service Abstraction
Abstracting the `MessageBox` class into an interface primarily for aiding dependency injection and improving testability.

### Getting Started
1. Download and extract the files.
2. Copy the files or add the contents of the files into your project.
3. Rename the `namespace` to match your project's namespace.

## Code Example
The `IMessageBoxService` interface is an abstraction for the `MessageBox` class, you should no longer call the `MessageBox` 
class to display dialogs, instead, create an instance of `MessageBoxService` and call the `Show` methods. 

Here is a very simple example:

```cs
IMessageBoxService service = new MessageBoxService();

service.Show("Hello World!");
```

## Dependency Injection
The purpose of the abstraction is to aid dependency injection for classes which require a `IMessageBoxService`.

Here is an example of using dependency injection with `Unity`:

```cs
using Microsoft.Practices.Unity;
...

UnityContainer container = new UnityContainer();

container.RegisterType<IMessageBoxService, MessageBoxService>();
container.RegisterType<MyAwesomeClass>();

MyAwesomeClass awesome = container.Resolve<MyAwesomeClass>();
awesome.Say("It Worked!");
```

Where `MyAwesomeClass` looks like this:

```cs
public class MyAwesomeClass
{
    readonly IMessageBoxService _MessageBoxService;

    public MyAwesomeClass(IMessageBoxService messageBoxService)
    {
        _MessageBoxService = messageBoxService;
    }

    public void Say(string message)
    {
        _MessageBoxService.Show(message);
    }
}
```

## What about testing?
When testing your classes, you should inject a **fake** `IMessageBoxService` into your class' constructors. This will ensure that no
*real* message box dialogs are displayed when testing your classes.

Here is an example using `NSubstitite`:

```cs
using NSubstitute;
using System.Windows;
...

string hello = "Hello World!";

//Set up the fake message box service.
IMessageBoxService fakeService = Substitute.For<IMessageBoxService>();
fakeService.Show(hello).Returns(MessageBoxResult.OK);

//Inject the fake message box service into the class' constructor.
MyAwesomeClass awesome = new MyAwesomeClass(fakeService);
awesome.Say(hello);
```

## License
This project is under the MIT license.
