---
title: "Application Data Flow: Part One"
layout: post
date: 2018-03-28 22:48
image: /11w5lxGBI8zAAIgSD1vJAEw.png
headerImage: true
tag:
- iOS
- swift
- application data
category: blog
author: chriswebb
description: New blog
---

Before we get stated, if you saw store in the title and thought this was something to do with a commerce app, unfortunately it does not. In this instance, Store is in regards to a particular way of organizing storage and retrieval of data for the application.

Okay, now that we have that out of the way, today we are going to be talking about application data flow. While the topic may not sound super interesting, having a good handle on the river the river of information flowing through your application will in the long run save you a lot time.

### Order From Chaos
How often do you sit down and rough sketch out an application before you start building? Regularly? Not at all? If it is regularly, how much of that time is spent mapping the data and application state and how much of it is on planning UI?

The truth is not nearly as many people spend time planning the data as on the UI. And why not, there are tons of really great tools like sketch for planning UI, and there tons of blogs and other resources for learning more about planning your UI. Planning your data isn’t nearly half the fun. But, if you do it properly, spending the time to do it will save you double the trouble.

#### Chaos From Organization

If you think about it, someone starting without much experience and using MVC in iOS as their guide could end up with code that structured in any number of ways. There are very few constraints or even signposts for them to follow. The sign post that is clearly visible is generally held onto like a life raft. This is why so much beginner code ends up in the ViewController and in particular viewDidLoad. Without much else to reference, this becomes their organizing principle.

#### An Open Canvas

Beyond the implementation of the base UI classes, there are almost no decisions made for you you as a programmer. This open canvas is often the source of frustration when you start out, because at that stage you really want to know where you should go.But as you feel more comfortable having this free space opens up new possibilities, it is a great place to explore.

#### MVC

For this example we will be working with something close to plain vanilla MVC. In the past, I must admit that I’ve had an unnecessarily harsh opinion of the the standard MVC that iOS uses. Mainly this was due to my own impatience. Given a little time, and the proper implementation MVC can work really well.

![](https://cdn-images-1.medium.com/max/300/1*4_Fy-a0HhPp2_1qWi97KeA.gif)

While there are view models in the demo project, there’s nothing particularly fancy about the code, no RxSwift, no coordinators. For all intents and purposes it’s vanilla MVC, the tableview cell models are structured like this:

{% highlight swift %}
import UIKit

class CellModel: NSObject {
    @objc dynamic var titleText: String? = ""

    init(title: String) {
        self.titleText = title
    }
}
{% endhighlight %}

### Data Flow

Before we go any further I just want to clarify a term: data flow. When I refer to data flow in an application, I’m referring to how data is passed along through the application from the source to display and how that is structured. It encompasses everything from API response all the way to the data model and ultimately to the rendering of the data.

For the level of importance it has, it doesn’t get nearly the amount that it deserves. I’d be willing to bet that a lot of my own hangups when I first learning networking calls and started building more complex applications came from the fact that I didn’t really think through the path that my data would take through the application. Hopefully it’ll become more clear just how important it is go forward with the demo code.

#### The Synchronizer

The Synchronizer is a class bound protocol. When implemented it allows your controller to subscribe to object that is updating the data model. That way it can act accordingly. In the example that I’ve given. When the data model adds a new item, the controller is notified through its subscription with the Synchronizer. It can then synchronize the data and the view.

{% highlight swift %}

import Foundation

protocol Synchronizer: class {
    func willUpdate()
    func didUpdate()
}

{% endhighlight %}

#### The Sync

The Sync, maintains the connection between the controller and object that updates the data. The sync message is sent when you feel that your data has completed updating. This could be at the end of an API call or a CoreData fetch.

{% highlight swift %}

import Foundation

class Sync {

    weak var notification: Synchronizer?

    init(notify: Synchronizer) {
        self.notification = notify
    }
}

{% endhighlight %}

#### Store

The store coordinates the flow and holds the data. In the form it just a protocol that defines certain characteristics. It becomes useful when you implement it for specific data and tasks.

{% highlight swift %}

import Foundation

protocol Store: class {

    associatedtype Model: Codable, Equatable

    var notifications: [Sync] { get set }

    var items: [Model] { get set }

    var database: Database<Data> { get }
}

extension Store {

    func remove(_ value: Model) {
        guard let offset = items.index(of: value) else { return }
        items.remove(at: items.startIndex.distance(to: offset))
        save()
    }

    func clear() {
        items.removeAll()
        save()
    }

    func contains(_ value: Model) -> Bool {
        return items.contains(value)
    }

    func add(notification: Synchronizer) {
        let notify = Sync(notify: notification)
        notifications.append(notify)
    }

    func add(_ value: Model) {
        guard !items.contains(value) else { return }
        items.append(value)
        save()
    }

    func save() {
        guard let data = try? database.encoder.encode(items) else { return }
        notifications.forEach { $0.notification?.willUpdate() }
        database.write(value: data)
        notifications.forEach { $0.notification?.didUpdate() }
    }
}

{% endhighlight %}

#### ItemStore

This class conforms to the Store protocol and is specialized to handle the applications main data model, which I’ve ceremoniously named Item.

{% highlight swift %}

import Foundation

class ItemStore: Store {

    var notifications: [Sync] = []

    typealias Model = Item

    var items: [Item] = []

    var database: Database<Data>

    init(database: Database<Data>) {
        self.database = database
    }

    func value(for key: String) -> Item? {
        return items.filter { $0.name == key }.first
    }

    func getCellModels() -> [CellModel] {
        return items.map { CellModel(title: $0.name) }
    }
}

{% endhighlight %}

#### TableViewDataSource

The TableViewDataSource in this implementation does double duty as a datasource and as a view model. It serves as intermediary between the data, and the controller. If I had one reservation about this class it is that it is starting edge close to the line of having multiple purposes. It might be better to separate out the interface between the data and the view modeling into separate classes.

#### ViewController

This is the controller! It’s all business not much extra.

{% highlight swift %}


import UIKit

class TableViewDataSource: NSObject {

    private var store: ItemStore

    private var cellModels: [CellModel] {
        return self.store.getCellModels()
    }

    private var itemCount: Int {
        return cellModels.count
    }

    init(store: ItemStore) {
        self.store = store
    }

    func updateItem() {
        addItem(item: Item(name: "Item \(itemCount + 1)", uuid: UUID.init()))
    }

    func addItem(item: Item) {
        store.add(item)
    }

    func clearAll() {
        store.clear()
    }

    func getItem(from model: CellModel) -> Item? {
        guard let text = model.titleText, let value = store.value(for: text) else { return nil }
        return value
    }

    func remove(value: Item?) {
        if let item = value {
            store.remove(item)
        }
    }

    func removeValue(for indexPath: IndexPath) {
        let model = cellModels[indexPath.row]
        let item = getItem(from: model)
        remove(value: item)
    }
}

extension TableViewDataSource: UITableViewDataSource {

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return itemCount
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "example", for: indexPath) as UITableViewCell
        cell.textLabel?.text = cellModels[indexPath.row].titleText
        return cell
    }
}

{% endhighlight %}

#### Database

Finally, I’ll just append the data storage parts to the end. These are the components that handle the storage and retrieval of the data to the disk directly. They’ve been split into the reading and writing functionalities as protocols which are implemented in the database class.

### Writer

{% highlight swift %}

import Foundation

protocol DataWriter: class {
    associatedtype T: Codable
    var encoder: JSONEncoder { get }
    var defaults: UserDefaults { get }
    var key: String { get }
    func write(value: T)
}

{% endhighlight %}

### Reader

{% highlight swift %}

import Foundation

protocol DataReader: class {
    associatedtype T: Codable
    var decoder: JSONDecoder { get }
    var defaults: UserDefaults { get }
    var key: String { get }
    func read() -> T?
}

{% endhighlight %}

### Database:

In the database class we bring both the DataWriter and DataReader protocols together in an implementation using generic T that conforms to Codeable

{% highlight swift %}

import Foundation

class Database<T: Codable>: DataWriter, DataReader {

    var decoder: JSONDecoder
    var encoder: JSONEncoder
    var key: String

    var defaults: UserDefaults = .standard

    init(key: String, encoder: JSONEncoder, decoder: JSONDecoder) {
        self.key = key
        self.encoder = encoder
        self.decoder = decoder
    }

    convenience init(key: String) {
        self.init(key: key, encoder: JSONEncoder.init(), decoder: JSONDecoder.init())
    }

    convenience init() {
        self.init(key: UUID().uuidString, encoder: JSONEncoder.init(), decoder: JSONDecoder.init())
    }

    func write(value: T) {
        defaults.set(value, forKey: key)
    }

    func read() -> T? {
        return nil
    }
}

{% endhighlight %}

Resources:
- [Unidirectional Data Flow Architecture (Redux) in Swift](https://medium.com/seyhunakyurek/unidirectional-data-flow-architecture-redux-in-swift-6fa2ed5c3c76)
- [Blog · objc.io](https://www.objc.io/blog/)
- [GitHawk Blog](https://blog.githawk.com/)
- [Cocoa with Love](https://www.cocoawithlove.com/)
