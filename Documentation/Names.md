#  Resolver: Named Instances

## Why name a registration?

Because named registrations and resolutions let you change the behavior of the app.

## Quick Example #1

Consider the following set of registrations.

```
register { resolve(name: Bundle.main.infoDictionary!["mode"] as! String) as XYZServicing }
register(name: "data") { XYXService() as XYZServicing }
register(name: "mock") { XYXMockService() as XYZServicing }
```

We've registered XYZServicing three times, one without a name, one with the name "data", and the other with the name "mock".

The nameless registration, however, gets a string from the app's info.plist and asks Resolver to resolve an instance with the proper type and with the proper name.

Let's see it in use by a client.

```
let service: XYZServicing = resolver.resolve()
```

The client just asks Resolver for a service.

Behind the scenes, however, and depending upon how the app is compiled and the "mode" value is set in the app's plist, one build will get actual data, while the other build will get mock data.

And as long as XYXMockService complies with the XYZServicing protocol, the client doesn't care.

Nor should it.

## Quick Example #2

Now, consider the next pair of registrations:

```
register(name: "add") { XYZViewModelAdding() as XYZViewModelProtocol }
register(name: "edit") { XYZViewModelEditing() as XYZViewModelProtocol }
```

Here we're registering two instances of the same protocol, `XYZViewModelProtocol`.

But one view model appears to be specific to adding things, while the other's behavior leans more towards editing.


```
class ViewController: UIViewController, Resolving {
var editMode: Bool = true // set, perhaps, by calling segue
lazy var viewModel: XYZViewModelProtocol = resolver.resolve(name: editMode ? "edit" : "add")!
}
```

Now the view controller gets the proper view model for the job. The `lazy var` ensures that the viewModel resolution doesn't occur until after the viewController is instantiated and `prepareForSegue` has had a change to correctly set `editMode`.
