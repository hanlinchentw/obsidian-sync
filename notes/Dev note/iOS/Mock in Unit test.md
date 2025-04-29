#unit_test #ios 

When writing unit tests, one key thing to watch out for is dependencies whether the networking, third-party libraries, etc. If your test relies on an actual network response, it's already at risk of being flaky --- slow, unpredictable, and brittle.
Let’s walk through a common example and how to fix it.

---

## The Problem
Consider the following code:
```swift
struct ViewModel {
	var processedData = [ProcessedData]()

	func loadData() async {
		let result = await NetworkManager.shared.sendRequest()
		// some business logic happens here
		processedData = result.map { ... }
	}
}
```
Now we want to test `loadData()` to ensure `processedData` is correctly set. So we write:
```swift
class ViewModelTests: XCTestCase {

	let sut: ViewModel!

	override func setup() {
		sut = ViewModel()
	}

	func testLoadDataSuccess() async {
		await sut.loadData()
		let expectedProcessData: [ProcessedData] = [...]
		XCTAssertEqual(processData, expectedProcessData)
	}
}
```

Looks fine, right?

**But wait...**

There’s a hidden dependency here:
NetworkManager.shared.sendRequest() is a **real network call**.

Can we guarantee it always returns the same response during testing?
**No**. And that’s a problem.

## The Fix: Dependency Injection

Let’s make `NetworkManager` injectable, so we can control its behavior in tests:
```swift
struct ViewModel {
	var processedData = [ProcessedData]()

	let networkManager: NetworkManager

	init(networkManager: NetworkManager) {
		self.networkManager = networkManager
	}

	func loadData() async {
		let result = await networkManager.sendRequest()
		// some business logic happens here
		processedData = result.map { ... }
	}
}
```
Now, we can provide a **mock** in tests. But we can go a step further.

## 🔄 Decoupling with Abstraction
ViewModel shouldn’t even need to know about NetworkManager. It only needs data.
Let’s apply the Dependency Inversion Principle:
> High-level modules (like ViewModel) should not depend on low-level modules (NetworkManager). Both should depend on abstractions.


#### Step 1: Define an abstraction
```swift
protocol DataProvider {
	func getData() -> Data
}
```
#### Step 2: Make `NetworkManager` conform
```

class NetworkManager: DataProvider {
	func getData() -> Data {
		return sendRequest()
	}
}

```
#### Step 3: Use the abstraction in `ViewModel`
```swift
struct ViewModel {
	var processedData = [ProcessedData]()
	let dataProvider: DataProvider

	init(dataProvider: DataProvider) {
		self.dataProvider = dataProvider 
	}
	func loadData() async {
		let data = await dataProvider.getData()
		processedData = // ...
	}
}
```

## 🧪 Unit Test with a Mock
```swift
struct MockDataProvider: DataProvider {
	var mockData: Data

	func getData() ->Data {
		assert(mockData != nil, "mockData hasn't been set.")
		mockData!
	}
}

class ViewModelTests: XCTestCase {
	let sut: ViewModel!
	let mockDataProvider: MockDataProvider!

	override func setup() {
		dataProvider = MockDataProvider()
		sut = ViewModel(dataProvider: dataProvider)
	}

	func testLoadDataSuccess() async {
	    dataProvider.mockData = /* mock ProcessedData values */
		await sut.loadData()
		let expectedProcessData: [ProcessedData] = []
		XCTAssertEqual(processData, expectedProcessData)
	}
}
```

## Result: Reliable Tests

Now `testLoadDataSuccess` will **always** give the same result, because we’ve:

- ✅ Eliminated real network calls
    
- ✅ Applied the Dependency Inversion Principle
    
- ✅ Used abstraction and mocking for full control

Flaky test avoided. 🎉  
`ViewModel.loadData()` is now **truly testable**.