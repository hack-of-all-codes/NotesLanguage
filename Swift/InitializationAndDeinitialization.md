# Initialization
nitialization is the process of preparing an instance of a class, structure, or enumeration for use. This process involves setting an initial value for each stored property on that instance and performing any other setup or initialization that is required before the new instance is ready for use
```swift
//default initializer for structs: MEMBER-WISE initializer
struct Fahrenheit {
    var temperature: Double
}
var f = Fahrenheit(temperature: 1.25)

//custom initializer
struct Fahrenheit {
    
    var temperature: Double
    
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
    
struct Fahrenheit {
    
    var temperature: Double = 69.0
    
    init() {
        temperature = 32.0
    }
    
    //with param, external name is default for all parameters
    //NOTE: default values are not allowed for initializer params
    init(temperature: Double) {
        self.temperature = temperature
    }
    
    //overloaded initializer with different method name
    init(fromKelvin kelvin: Double) {
        temperature = (kelvin * 9 / 5) - 459.67
    }
    
    init(fromCelsius celsius: Double) {
        temperature = ......
    }
    
    //initializer delegation
    init(multipliedBy multiplier: Int, temperature: Double) {
        self.init(temperature: temperature * Double(multiplier))
        //init should be called BEFORE any call to self
        self.temperature = 100.0
    }
}

//multiple initializers are allowed, as long as all properties
//are set to their allowed values before init ends
var f = Fahrenheit(temperature: 20)
f = Fahrenheit(fromKelvin: 2000)

//constants can only be initialize ONCE
//it follows that constants can only be initialize 
//in the declaring class
class Vehicle {
    
    let numberOfWheels: Int
    
    init() {
        numberOfWheels = 0
        numberOfWheels = 20 //ERROR
    }
}
```

## Convenience Initializer (Class-ONLY) and Designated Initializer
Designated Initializers Completely Initializes a Class:
- A designated initializer must call a designated initializer from its immediate superclass
- A convenience initializer must call another initializer from the same class
- A convenience initializer must ultimately call a designated initializer
```swift
class Transport {
    
    var name = "Name"
    
}

class Vehicle: Transport {
    
    var numberOfWheels: Int
    var isAwesome: Bool
    
    init(numberOfWheels: Int, andAwesome isAwesome: Bool) {
        self.numberOfWheels = 10
        self.isAwesome = true
    }
    
    //even the default initializer needs an override
    convenience override init() {
        self.init(numberOfWheels: 11, andAwesome: true)
    }
}

class Car: Vehicle {
    
    override init(numberOfWheels: Int, andAwesome isAwesome: Bool) {
        super.init(numberOfWheels: numberOfWheels, andAwesome: isAwesome)
        self.numberOfWheels = 5
        self.isAwesome = false
    }
    
    //convenience initializers are not inherited
    convenience init() {
        self.init(numberOfWheels: 1, andAwesome: false)
        self.numberOfWheels = 111
    }
}
```

## Automatic Initializer Inheritance
Assuming that you provide default values for any new properties you introduce in a subclass, the following two rules apply:

- Rule 1: If your subclass doesn’t define any designated initializers, it automatically inherits all of its superclass designated initializers.
- Rule 2: If your subclass provides an implementation of all of its superclass designated initializers—either by inheriting them as per rule 1, or by providing a custom implementation as part of its definition—then it automatically inherits all of the superclass convenience initializers.
```swift
class Food {
    
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    convenience init() {
        self.init(name: "Unnamed")
    }
}

var f1 = Food() //convenience
var f2 = Food(name: "Burger") //designated

class RecipeIngredient: Food {
    
    var quantity: Int
    
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}

var r1 = RecipeIngredient() //inherited: Rule#2
var r2 = RecipeIngredient(name: "Pasta") //convenience
var r3 = RecipeIngredient(name: "Pasta", quantity: 2) //designated

class ShoppingListItem: RecipeIngredient {
    
    var purchased = false
}

//inherited: Rule#1
var s1 = ShoppingListItem()
var s2 = ShoppingListItem(name: "Eggs")
var s3 = ShoppingListItem(name: "Bacon", quantity: 6)
```

## Failable Initializer
Initialization can fail. This failure might be triggered by invalid initialization parameter values, the absence of a required external resource, or some other condition that prevents initialization from succeeding
```swift
struct Animal {
    
    let species: String
    
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}

let piggy = Animal(species: "Piggy Piggy")
if let animal = piggy {
    print("created animal: \(animal.species)")
}

let nilly = Animal(species: "")
if nilly == nil {
    println("failed to initialize")
}

//raw value enums have an automatic failable initializer
enum TemperatureUnit: Character {
    
    case Celsius = "C", Kelvin = "K", Fahrenheit = "F"
}

var f = TemperatureUnit(rawValue: "F") //Fahrenheit
var x = TemperatureUnit(rawValue: "X") //nil

//class failable init
class Product {
    
    let name: String
    
    init?(name: String) {
        self.name = name
        //the difference between value types
        //and reference types: 
        //properties should be valid before 
        //returning nil, THIS IS SOMEHOW COUNTERINTUITIVE
        if name.isEmpty { return nil }
    }
}

var nilProduct = Product(name: "") //nil
var product = Product(name: "Shoe") //Shoe
```

Reference: [Apple Inc., Initialization](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html#//apple_ref/doc/uid/TP40014097-CH18-ID203)

# Deinitialization