---
layout: post
title: "Airpin Diary #1: Inversion of Control"
published: true
date: 2018-03-04 15:38:33
permalink: /blog/:title
author: Thomas Carey
---

For my app, [Airpin](https://github.com/suchtomwow/airpin/tree/develop), which is a [Pinboard](https://pinboard.in) client, I have a top level view controller which segregates a user's bookmarks into several obvious categories: All, unread, untagged, public, and private. When a user taps one of the categories, they are taken to another view controller that shows them the appropriate list of bookmarks for the category that they selected.

When I initially implemented this list of bookmarks (BookmarkListViewController), I created a view model that would take in a category, switch on it, and perform the a specific operation to return the correct list of bookmarks for the selected category, like so:

``` swift
class BookmarkListViewModel: BaseViewModel {
    init(category: CategoryViewModel.Category) {
        self.category = category
    }
        
    var title: String {
        switch category {
        case .all:
            return "All"
        case .unread:
            return "Unread"
        case .untagged:
            return "Untagged"
        case .public:
            return "Public"
        case .private:
            return "Private"
    }
    
    func fetchBookmarks(completion: @escaping () -> Void) {
        switch category {
        case .all:
            fetchAllBookmarks(completion: completion)
        case .unread:
            fetchUnreadBookmarks(completion: completion)
        case .untagged:
            fetchUntaggedBookmarks(completion: completion)
        case .public:
            fetchPublicBookmarks(completion: completion)
        case .private:
            fetchPrivateBookmarks(completion: completion)
        }
    }
    
    private func fetchAllBookmarks(completion: @escaping () -> Void) {
        /* Some code that fetches all bookmarks */
    }
    
    private func fetchUnreadBookmarks(completion: @escaping () -> Void) {
        /* Some code that fetches unread bookmarks */
    }
    
    private func fetchUntaggedBookmarks(completion: @escaping () -> Void) {
        /* Some code that fetches untagged bookmarks */
    }
    
    private func fetchPublicBookmarks(completion: @escaping () -> Void) {
        /* Some code that fetches public bookmarks */
    }
    
    private func fetchPrivateBookmarks(completion: @escaping () -> Void) {
        /* Some code that fetches private bookmarks */
    }
}
```

This works fine enough, but what happens when I eventually want to introduce a new category, say, I don't know, another user's public bookmarks? Well, then I'd have to add another enum case, `.public(userID: String)`, add the case to the `fetchBookmarks(completion:)` switch and add the case to the computed `title` property.

This might not seem like that big of a deal, but what if I had even more methods that switched on the enum and performed some work based on its value? Maybe I want to change the background color of the table view based on the category, or the text color, or perform an additional transform to the list. The possibilities are endless.

Or, what if I didn't have access to modify the `CategoryViewModel.Category` enum? Like if it was contained in a framework that I didn't have permission to modify? Then I wouldn't be able to add a new category whatsoever since it's impossible to add cases to an enum in an extension.

Lastly, it's just so much...code. I mean, look up there. All that...code. You most likely really only care about one category at a time. Say it's the `.unread` category. So you're only dealing with the `fetchUnreadBookmarks(completion:)`. All the rest is just...code. Noise.

Let's get it out of here.

### Allow me to introduce to you...Inversion of Control ↩️.

Inversion of control allows us to define an interface (in Swift, a _protocol_), and create several concrete implementations of it. If you look above, what do we really need this interface to do? We need to fetch a list of bookmarks and return a title.

``` swift
protocol BookmarkListViewModel {
    func fetchBookmarks()
    var title: String { get }
}
```

That seems like a good start. 

Now, let's create its implementation:

``` swift
class UnreadBookmarksViewModel: BookmarkListViewModel {
    var title: String {
        return "All"
    }

    func fetchBookmarks() {
        /* Some code that fetches unread bookmarks */
    }
}
```

And that's it! Now, if we have a bug in our unread bookmarks code, we can focus on _just_ the code that we need to without having to filter through all that other noise. Additionally, if we need to define new categories, we can create a new `BookmarkListViewModel` implementation, implement the required function and property getter, and we're done!

This also gives us much more flexibility when testing. Instead of needing to subclass the `BookmarkListViewModel` and override methods, we can simply create a `MockBookmarkListViewModel` implementation of the protocol and we're good to go.

So next time you find yourself with a long class that's doing mostly the same thing over and over again, think about whether you can _invert the control_ and instead program to an interface with lots of small, concrete implementations.

**Update**:
You can see the finished implementation in a [pull request](https://github.com/suchtomwow/Airpin/pull/1).