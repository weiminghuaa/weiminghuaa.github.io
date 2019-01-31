# RxExample_GithubSearch

一个综合的搜索的例子

## GitHubSearchRepositoriesState

这是搜索结果的模型，看看模型的设计：

```swift
// GitHubSearchRepositories.swift
var searchText: String
var shouldLoadNextPage: Bool
var repositories: Version<[Repository]> // Version is an optimization. When something unrelated changes,we don't want to reload table view.
var nextURL: URL?
var failure: GitHubServiceError?
```

## failure属性 -> 导航栏颜色

1. failure属性用于记录请求Error

```swift
// GitHubSearchRepositoriesAPI.swift
enum GitHubServiceError: Error {
    case offline
    case githubLimitReached
    case networkError
}
```

2. GitHubSearchRepositoriesState有一个扩展属性，通过failure，来获取网络是否在线。而网络是否在线，则会绑定到导航栏背景色

```swift
// GitHubSearchRepositories.swift
extension GitHubSearchRepositoriesState {
    var isOffline: Bool {
        guard let failure = self.failure else {
            return false
        }

        if case .offline = failure {
            return true
        }
        else {
            return false
        }
    }
}
```

```swift
// GitHubSearchRepositoriesViewController.swift
state
    .map { $0.isOffline }
    .drive(navigationController!.rx.isOffline)
    .disposed(by: disposeBag)
```

```swift
// UINavigationController+Extensions.swift
extension Reactive where Base: UINavigationController {
    var isOffline: Binder<Bool> {
        return Binder(base) { navigationController, isOffline in
            navigationController.navigationBar.barTintColor = isOffline
                ? Colors.offlineColor
                : Colors.onlineColor
        }
    }
}
```

3. 通过这个流程，可以学习一下drive和Binder：

- drive: drive本来就是为了方便UI与数据绑定，可以查看官方文档的Traits。githubSearchRepositories返回的就是Driver，GitHubSearchRepositoriesViewController通篇都是使用这个state

```swift
// GitHubSearchRepositories.swift
func githubSearchRepositories(
        searchText: Signal<String>,
        loadNextPageTrigger: @escaping (Driver<GitHubSearchRepositoriesState>) -> Signal<()>,
        performSearch: @escaping (URL) -> Observable<SearchRepositoriesResponse>
    ) -> Driver<GitHubSearchRepositoriesState> {

    }

// GitHubSearchRepositoriesViewController.swift
override func viewDidLoad() {
    super.viewDidLoad()

    let state = githubSearchRepositories(
        searchText: searchBar.rx.text.orEmpty.changed.asSignal().throttle(0.3),
        loadNextPageTrigger: loadNextPageTrigger,
        performSearch: { URL in
            GitHubSearchRepositoriesAPI.sharedAPI.loadSearchURL(URL)
                .trackActivity(activityIndicator)
        })
    
    ...
}
```

- Binder: 就是数据与UI的绑定，官方文档示例：

```swift
results
    .map { "\($0.count)" }
    .bind(to: resultCount.rx.text)
    .disposed(by: disposeBag)
```

## 