# SOLID Uses for Child View Controllers

---

# Genesis

> [Many Controllers Make Light Work](http://khanlou.com/2016/02/many-controllers/) -- @khanlou

^ https://twitter.com/AndrewCopp/status/719531984423411712

---

# Agenda

1. Identify the Problem
2. [Refactor the Mega Controller](https://academy.realm.io/posts/andy-matuschak-refactor-mega-controller/)
3. Back to SOLID
4. How does this work with my existing codebase?

---

# Technical Debt

Diagram a massive view controller. Attach models and singletons (networking and persistence).

^ Massive View Controller

---

# Horizontal vs Vertical

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vR4IcwVhKbXz_R9CaES0HU2ZYC6mPRicBdUDKuwindUrDwDTV1GMYHvy0jg8wiHZTqXIJ-h0FfbQ86o/pub?w=1217&h=406)

^ Horizontal and vertical are made up terms. Vertical is moving anything out of a view controller and into associated objects. Horizontal is moving things out into new view controllers.

---

# Horizontal vs Vertical

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQU53Ht8lBcsBM6StpWsr-lqmwgDWZCZFjeCwSrO29uTfM4K8_kiqE-lGzQ99blTc2CkXltXW_WaPYy/pub?w=1217&h=406)

---

# Lessons from VIPER

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQrCKk9SoBHmFnLl2FoHW0hq5id58evW8fnqpYbJaOuSGzfBppmS2mZ8_jrCWN7XDo_SUJcE4dhiCMa/pub?w=1440&h=1080)

^ PONSOs (Plain Old NSObjects) are easier to test than view controllers. SOLID. DRY. Consistency. Dependency Injection

---

# Lessons from VIPER

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSCnroIhVZ7b4XwYmEprca8ghsWm34i0D4qfMDKAdKiuvbhnjcVpIF5MwnuOolpDNqnIq-yy2i6f43C/pub?w=1440&h=1080)

^ Look at all that surface area.

---

# Basics

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

---

#[fit] Example

---

# Map

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQ38kq5nVRLvxI7pYuEC7ACrEirllZn4S0ZUrz1LOXXFoIYaaUAL9_trmyEg-wN38SL4wg3PQhnr_To/pub?w=296&h=529)

---

### Use Case #1
# Traditional Container

---

# Tally Bar

![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSQM4_qwO7Os-BSKCs4fFWsFYyh0aWIZSG47su-2SzGeSjxdI-or6b3JYMflpZqRvis2oGTXaNEBPo-/pub?w=1158&h=526)

---

```Swift
class TallyBarController {

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

---

### Use Case #2
# Layout

---

# Split Controller
![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vSeUVo5kGjJyuxqroNEpuJPZUKAypUPZ-YzVvgOs9vPu9x0KabSBBljo8w05sJuGhXoHxsSPKHmvcnv/pub?w=1161&h=573)

^ Could make the argument for a stack view but you can build a stack from view from compound view controllers.

---

```Swift
class VerticalSplitController {

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

^ Whole bunch of layout code I don't have to worry about elsewhere.

---

### Use Case #3
# Separation of Concerns

---

# Empty View Controller
![inline, fit](https://docs.google.com/drawings/d/e/2PACX-1vQqc57Bokl6mrre6wL-7jDGnsKlQ5xx_OGYYIhfcmheMOwVnIM5Rsyd25nEt_IQ3lJu9oi1qPyt7XJI/pub?w=1314&h=662)

^ Sync and Observe. This is where reactive is really nice. You update your database in one view controller and the observing view controller updates.

---

```Swift
class EmptyViewController {

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
class FilteredTagsQuery: Action {

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
class UnfilteredTagsQuery: Action {

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
class ToastController {

    init() {
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

# SOLID

1. Single Responsibility
2. Open-Closed
3. Liskov Substitution
4. Interface Segregation
5. Dependency Inversion

---

# Horizontal with Vertical

^ I'm guessing a lot of you and your teams already talk about vertical architecture but now you can start talking about horizontal architecture.

---

Grid

 | View | Presenter | Interactor
---|---|---|---
1 | List | List Stock Requests Presenter | Query
2 | List | View Products Presenter | Query
3 | Tally Bar | View Product Presenter | Read
4 | Tally Bar | Toast Presenter | Query

^ Here is an example of actual flows in our application.

---

## (Possible)
# Drawbacks

---

# More Boilerplate

^ I've spun up screens that would have previously taken me a day in thirty minutes. 

---

# Memory Constraints

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

# Hiring
## (Soon)

^ Use a background photo

---

# Questions?
