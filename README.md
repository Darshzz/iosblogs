## iosblogs

> Darshan Mothreja - 2 min read

# Some and Any keyword in Swift

> Hey everyone I am Darshan Mothreja!! Been coding since ‘15. Some can say it was love at first byte. I engineer apps with such finesse, they practically dance through your device. And if you’re a dev feeling lost in the code jungle, fear not! I’m here to be your coding compass, guiding you through the wilderness of curly braces and semicolons.


Swift 5.7 introduced 2 new keywords:  some  and  any. Because prior to Swift 5.7, We had to go through with error if we do not specify a specify type to the associatedType defined in Protocol

_Error: **Protocol ‘Type’ can only be used as a generic constraint because it has Self or associated type requirements**_


We could have got rid of that error without the use of  some  and  any  using generics: For e.g

```ruby
  protocol Identifiable {
      associatedtype ID
      var id: ID { get set }
  }
```
Give a specific type to avoid error.

```ruby
  struct Person: Identifiable {
      var id: String
  }
```

But as you deep dive and want to continue generics with protocols here some and any will play a key role.

```ruby
  protocol StorageMechanism {
        associatedtype TypeOfStorage
    }
    
    func implementingStorageFirst(_ mechanism: StorageMechanism) {
        // ❌ Will not compile since StorageMechanism
        //    has associatedtype defined inside it
    }
    
    func implementingStorageSecond(_ mechanism: T) {
        // ✅ Will Compile since we are now using generics
    }
    
    func implementingStorageThird(_ mechanism: some StorageMechanism) {
        // ✅ Will Compile since we are now using some keyword
    }
  ```

The main focus of this article is to explain both these keywords through similarities and differences between them. I will be using one of SwiftUI’s protocol View whose declaration primarily looks like:

```ruby
    public protocol View {

        associatedtype Body : View
    }
 ```

Both  some  and  any  can be used with Protocol that have associatedtype in it (also called as generic protocols)
Consider the following example:

```ruby
  import SwiftUI

  // 1. ❌ Will not compile
  var var1: View = Text("Sample Text")

  // 2. ✅ Will compile
  var var2: some View = Text("Sample Text")

  // 3. ✅ Will Compile
  var var3: any View = Text("Sample Text")
```

Reasons for the above:
1. Compilation failed since  View  has  associatedtype Body: View  inside it
2. Compilation succeeds since now we have declared the type with  some  keyword which acts as opaque type
3. Compilation succeeds since now we have declared the type with  any  keyword which acts as existential type

# Differences
The real fun starts when you look at the actual differences between the two of them since above code does not clearly states which should be used when

# Homogeneous vs Heterogeneous collection behaviour

some  only allows homogeneous collection, meaning only similar conforming type are allowed inside the collection. Meanwhile  any  allows heterogeneous collection meaning any conforming type is allowed inside the collection
Consider the following example:

```ruby
import SwiftUI

    // 1. ✅ Will compile
    var var1: [some View] = [ Text("Sample Text"), Text("Sample Text") ]
    
    // 2. ❌ Will not compile
    var var2: [some View] = [ Text("Sample Text"), Label("Sample Text", systemImage: "bolt.fill") ]
    
    // 3. ✅ Will Compile
    var var3: [any View] = [ Text("Sample Text"), Label("Sample Text", systemImage: "bolt.fill") ]
  ```

Lets check one more example 

# Similar return type

```ruby
import SwiftUI

    // 1. ✅ Will compile
    var var1: some View {
        let random = Int.random(in: 1...5)
        
        if random % 2 == 0 {
            return Text("Even")
        }
        else {
            return Text("Odd")
        }
    }
    
    // 2. ❌ Will not compile
    var var2: some View {
        let random = Int.random(in: 1...5)
        
        if random % 2 == 0 {
            return Text("Even")
        }
        else {
            return Label("Sample Text", systemImage: "bolt.fill")
        }
    }
    
    // ✅ Will compile
    var var3: any View {
        let random = Int.random(in: 1...5)
        
        if random % 2 == 0 {
            return Text("Even")
        }
        else {
            return Label("Sample Text", systemImage: "bolt.fill")
        }
    }
```

Reasons for the above:
1. Compilation succeeds since we’ll always be returning the same conforming type irrespective of the value of random at runtime;  some  approves of it 👍
2. Compilation failed since now different conforming types are being returned;  some  rejects it 👎
3. Compilation succeeds since  any  is unbothered whether or not we’re returning same or different conforming types; typical  any  🥱

Now that we are done with the similarities and differences, it’s time for the verdict:

>You should always start with some keyword first and if that does not fit your generics use case, switch to  any  keyword

That is because  any  relies on a mechanism in programming language called **Type Erasure** which essentially hides specific type information from code that doesn’t need to concern itself with those types. 

Happy Coding!
