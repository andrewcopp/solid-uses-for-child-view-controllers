# SOLID Uses for Child View Controllers

### by Andrew Copp

^ Greetings. Introduction. Title

^ Evening, everyone. Thank you so much for coming. My name is Andrew Copp and I'll be sharing my talk on Child View Controllers with you today.

---

# Genesis

> [Many Controllers Make Light Work](http://khanlou.com/2016/02/many-controllers/) -- @khanlou

^ Soroush Khanlou. Composition vs Decorator Pattern

^ Before we start, I just want to give a quick shoutout to Soroush Khanlou. He had a blog post a couple years ago that started me down this path. Now, his articles talks more about using view controllers as a way to accomplish composition and I will be talking more about a decorator pattern but I still through it was worth mentioning. You can find my original tweet here: https://twitter.com/AndrewCopp/status/719531984423411712.

---

# Agenda

1. SOLID Principles Refresher
2. Identify the Problem
3. [Refactor the Mega Controller](https://academy.realm.io/posts/andy-matuschak-refactor-mega-controller/)
4. Back to SOLID
5. Is this for me?

^ Read through agenda

---

# SOLID Principles

1. **S**ingle Responsibility
2. **O**pen-Closed
3. **L**iskov Substitution
4. **I**nterface Segregation
5. **D**ependency Inversion

^ Refresh SOLID Principles

---

# Technical Debt

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSiyCzky-4jY4AlYJkqitT0KnJLrG_nejNLfx0XKHM5jzQ3-NbCrIWqe-AllR9_gIuchrnsK9NU7eOJ/pub?w=629&h=519)

^ Massive View Controller. Responsibility. Fat models. Lifecycle.

^ Hopefully this isn't a problem for you but it is all too common. Lots of time, view controllers become bloated with various responsibilities such as networking and persistence. There are lots of ways to fight this such as trying to make fatter models but the fact of the matter is that at some point you are reliant on the lifecycle of a view controller.

---

# Horizontal vs Vertical

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vR4IcwVhKbXz_R9CaES0HU2ZYC6mPRicBdUDKuwindUrDwDTV1GMYHvy0jg8wiHZTqXIJ-h0FfbQ86o/pub?w=1217&h=406)

^ Vertical. Horizontal. Combined.

^ And that's where the conversation switches to either vertical or horizontal solutions. These are made up terms but I think they reflect what is happening fairly accurately. So-called Vertical Architecture is moving things out of the view controller and into other objects such as presenters, view models, and interactors. Horizontal Architecture is moving things out of view controllers and into other view controllers. Separately each of these are useful but they really shine when mixed together.

---

# Horizontal vs Vertical

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQU53Ht8lBcsBM6StpWsr-lqmwgDWZCZFjeCwSrO29uTfM4K8_kiqE-lGzQ99blTc2CkXltXW_WaPYy/pub?w=1217&h=406)

^ Overlapping functionality. Better together

^ These color codings represent overlapping functionality. The point here being, as you you create more objects and give them each smaller and smaller amounts of responsibility, you start to see overlap. Overlap means redundant code and redundant code means things that you can delete. These examples are arbitrary but you can see how more objects gives you more chances to reuse your code.

---

# Lessons from VIPER

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQrCKk9SoBHmFnLl2FoHW0hq5id58evW8fnqpYbJaOuSGzfBppmS2mZ8_jrCWN7XDo_SUJcE4dhiCMa/pub?w=1440&h=1080)

^ Quick. Previous talk.

^ Dan just gave a great talk on what I would call vertical architecture so I won't go into much detail here. I did give a talk two years ago on what VIPER can teach us.

---

# Lessons from VIPER

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSCnroIhVZ7b4XwYmEprca8ghsWm34i0D4qfMDKAdKiuvbhnjcVpIF5MwnuOolpDNqnIq-yy2i6f43C/pub?w=1440&h=1080)

^ Testability.

^ One of many lessons was that by moving code into Plain Old NSObjects (PONSOs) we increase the surface area of our codebase and make things easier to test. This code is also SOLID, DRY, consistent, and lends itself to dependency injection.

---

#[fit] Example

^ That's enough of an introduction. Let's jump in.

---

# Map

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQ38kq5nVRLvxI7pYuEC7ACrEirllZn4S0ZUrz1LOXXFoIYaaUAL9_trmyEg-wN38SL4wg3PQhnr_To/pub?w=296&h=529)

^ Overview of Radar. Item screen. Original complexity. Reimplemented.

^ Here is a screen from my company's app. I haven't talked about my company yet because I wanted to get into this talk. For context, Radar helps locate objects in real time around a store and let's you use that information in a few ways. On this particular screen, we give some information about an item and then display where instances of that item is in the various areas of the store. When we wrote the first iteration of the app, this view controller was hundreds, if not thousands, of lines long. We had to retrieve and display information about a product, we had to layout and update the TallyBar, and we had to display the map with a constant stream of information. All three of these sections were also found in other areas of the app where they were reimplemented mostly from scratch. Let's take a step-by-step walkthrough of how this was improved.

---

### Use Case #1
# Traditional Container

^ Navigation and TabBar

^ The first example I have for you today, is that you can use child view controllers to create custom containers. Apple provides us with NavigationControllers and TabBarControllers but they encourage us to write our own as needed. I feel like most apps I use have some unique element that could be encapsulated into a container controller.

---

# Tally Bar

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSQM4_qwO7Os-BSKCs4fFWsFYyh0aWIZSG47su-2SzGeSjxdI-or6b3JYMflpZqRvis2oGTXaNEBPo-/pub?w=1158&h=526)

^ Similar to TabBar. Reused.

^ With this particular case, the TallyBar is prime candidate to become a container. It's already very similar to a TabBarController. 

---

```Swift
class TallyBarController: UIViewController {

    let viewControllers: [UIViewController]

    init(viewControllers: [UIViewController]) {
        self.viewControllers = viewControllers
        super.init(nibName: nil, bundle: nil)
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        viewControllers.forEach() { viewController in
            // ADD {viewController}
        }
    }

    func displayViewController(at index: Int) {
        // SWITCH DISPLAYED VIEW CONTROLLER
    }

}
```

^ Simple

---

### Use Case #2
# Layout

^ Favorite. Three Types: Single, Compound, Mixture.

---

# Split Controller
![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSeUVo5kGjJyuxqroNEpuJPZUKAypUPZ-YzVvgOs9vPu9x0KabSBBljo8w05sJuGhXoHxsSPKHmvcnv/pub?w=1161&h=573)

^ Responsible for Positioning. StackView.

---

```Swift
class VerticalSplitController: UIViewController {

    enum Behavior {
        case fixedTop(let CGFloat)
        case intrinsic
    }

    let topViewController: UIViewController
    let bottomViewController: UIViewController

    let behavior: VerticalSplitController.Behavior

    init(topViewController: UIViewController, bottomViewController: UIViewController, behavior: VerticalSplitController.Behavior) {
        self.topViewController = topViewController
        self.bottomViewController = bottomViewController
        self.behavior = behavior
        super.init(nibName: nil, bundle: nil)
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        // ADD self.topViewController
        // ADD self.bottomViewController
        // ADD CONSTRAINTS TO self.topViewController AND self.bottomViewController
    }

}
```

^ Simple

^ Whole bunch of layout code I don't have to worry about elsewhere.

---

### Use Case #3
# Separation of Concerns

^ Invisible ViewControllers.

---

# Empty View Controller
![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQqc57Bokl6mrre6wL-7jDGnsKlQ5xx_OGYYIhfcmheMOwVnIM5Rsyd25nEt_IQ3lJu9oi1qPyt7XJI/pub?w=1314&h=662)

^ Sync and Observe.

^ This is where reactive is really nice. You update your database in one view controller and the observing view controller updates.

---

```Swift

protocol Action {

    func start()
    func stop()

}

```

^ Let's start by defining this protocol.

---

```Swift
class EmptyViewController: UIViewController {

    let contentViewController: UIViewController
    let action: Action

    init(contentViewController: UIViewController, action: Action) {
        self.contentViewController = contentViewController
        super.init(nibName: nil, bundle: nil)
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        // ADD self.contentViewController
        // ADD CONSTRAINTS TO self.contentViewController
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)

        self.action.start()
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)

        self.action.stop()
    }

}

```
---

```Swift
class FilteredProductLocationssQuery: Action {

    let areaIdentifier: Int

    init(areaIdentifier: Int) {
        self.areaIdentifier = areaIdentifier 
    }

    func start() {
        // START SCHEDULED TIMER
            // REQUEST TAGS FOR AREA WITH IDENTIFIER
    }

    func stop() {
       // STOP AND INVALIDATE TIMER
    }

}
```

---

### Case #4
# Consolidating Logic

---

# Empty View Controller (Continued)
![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vTPjQyigySzdvbRbjLTaGq7kNesjZ6moW_O5Crb8XeJx0lEneGgA8sYNjnCZwBQn_Q1yWLrEkHttNgn/pub?w=1570&h=574)

^ Single Point of Refresh. Bring up point about AJ wanting me to consolidate the network calls. I try and make sure that each network call only has one place where it is invoked and I don't want that to be a subclass of the TabBarController.

---

```Swift
class UnfilteredProductLocationsQuery: Action {

    func start() {
        // START SCHEDULED TIMER
            // REQUEST TAGS FOR ALL AREAS
    }

    func stop() {
       // STOP AND INVALIDATE TIMER
    }

}
```

^ Let respective view controllers do their own filtering later.

---

### Use Case #5
# Overlays

---

# In-App Notifications

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSz4SQevNVG3yJQuYeZ8alU8UwnfNnOHTtHEKU5uIVPq-8Ho3t5Sl72heCwzQzi6NIFClBVa0jQVR1g/pub?w=1977&h=573)

---

```Swift
class ToastController: UIViewController {

    let contentViewController: UIViewController

    init(contentViewController: UIViewController) {
        self.contentViewController = contentViewController
        super.init()
    }

    deinit {
        // REMOVE NOTIFICATION OBSERVER
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        // ADD NOTIFICATION OBSERVER
    }

    @objc applicationDidReceiveNotification(_ notification: Notification) {
        // PRESENT NOTIFICATION
        // FIRE TIMER
            // DISMISS NOTIFICATION
    }

}
```

---

# How is this SOLID?

---

# SOLID Principles

1. **S**ingle Responsibility
2. **O**pen-Closed
3. **L**iskov Substitution
4. **I**nterface Segregation
5. **D**ependency Inversion

---

# Horizontal meets Vertical

^ I'm guessing a lot of you and your teams already talk about vertical architecture but now you can start talking about horizontal architecture.

---

# Horizontal meets Vertical

View | Presenter | Interactor
---|---|---
Map Controller | View Tags Presenter | Query Interactor
List Controller | View Products Presenter | Query Interactor
Toast Controller | View Notifications Presenter | Query Interactor

^ Here is an example of actual flows in our application.

---

## (Possible)
# Drawbacks

---

# Lots to Manage

---

# Boilerplate

```Swift
func viewDidLoad() {
    super.viewDidLoad()

    let childViewController: ChildViewController = ChildViewController()
    let childView: UIView = childViewController.view
    childView.translateAutoResizingMaskIntoConstraints = false

    childViewController.willMoveToParentViewController(self)
    self.addChildViewController(childViewController)
    self.view.addSubview(childViewController.view)
    childViewController.didMoveToParentViewController(self)

    // Layout
    ...
}

```

^ I've spun up screens that would have previously taken me a day in thirty minutes. 

---

# Memory Constraints

---

# Difficult Debugging

^ Questionable

---

#[fit] What's Next?

--- 

# Smaller Components

---

# More Use Cases

^ Scrolling, Object Creation. I've started using this for lists. Successfully using it in places but need to figure out cell reuse.

---

# Separate Applications

^ Make actual separate applications. Smaller components that can be more easily tested. Can be delegated out to teammates.

---

# Hiring (Soon)

### Checkout goradar.com

^ Use a background photo

---

# Questions?

