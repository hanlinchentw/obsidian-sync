
```
button.tapHandler = { [weak self] in
  guard let self else { return }
  dismiss()
}

button.tapHandler = { [weak self] in
  guard let self = self else { return }
  dismiss()
}

button.tapHandler = { [weak self] in
  if let self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  if let self = self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  while let self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  while let self = self {
    dismiss()
  }
}
```

- Proposal: [SE-0365](https://github.com/apple/swift-evolution/blob/main/proposals/0365-implicit-self-weak-capture.md)
- Author: [Cal Stephens](https://github.com/calda)
- Review Manager: [Saleem Abdulrasool](https://github.com/compnerd)
- Status: **Implemented (Swift 5.8)**
- Implementation: [apple/swift#40702](https://github.com/apple/swift/pull/40702), [apple/swift#61520](https://github.com/apple/swift/pull/61520)

**Introduction**

As of [SE-0269](https://github.com/apple/swift-evolution/blob/main/proposals/0269-implicit-self-explicit-capture.md), implicit `self` is permitted in closures when `self` is written explicitly in the capture list. We should extend this support to `weak self` captures, and permit implicit `self` as long as `self` has been unwrapped.

```
class ViewController {
    let button: Button

    func setup() {
        button.tapHandler = { [weak self] in
            guard let self else { return }
            dismiss() // don't need to explictly add self for dismiss()
        }
    }

    func dismiss() { ... }
}
```

Swift-evolution thread: [Allow implicit `self` for `weak self` captures, after `self` is unwrapped](https://forums.swift.org/t/allow-implicit-self-for-weak-self-captures-after-self-is-unwrapped/54262)

Detail example

```swift
button.tapHandler = { [weak self] in
  guard let self else { return }
  dismiss()
}

button.tapHandler = { [weak self] in
  guard let self = self else { return }
  dismiss()
}

button.tapHandler = { [weak self] in
  if let self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  if let self = self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  while let self {
    dismiss()
  }
}

button.tapHandler = { [weak self] in
  while let self = self {
    dismiss()
  }
}
```

**Nested closures**

Nested closures can be a source of subtle retain cycles, so have to be handled more carefully. For example, if the following code was allowed to compile, then the implicit `self.bar()` call could introduce a hidden retain cycle:

```swift
couldCauseRetainCycle { [weak self] in
  guard let self else { return }
  foo()

  couldCauseRetainCycle {
    // error: call to method 'method' in closure requires 
    // explicit use of 'self' to make capture semantics explicit
    bar()
  }
}

// Allowed:
couldCauseRetainCycle { [weak self] in
  guard let self else { return }
  foo()

  couldCauseRetainCycle { [weak self] in
    guard let self else { return }
    bar()
  }
}

// Also allowed:
couldCauseRetainCycle { [weak self] in
  guard let self else { return }
  foo()

  couldCauseRetainCycle {
    self.bar()
  }
}

// Also allowed:
couldCauseRetainCycle { [weak self] in
  guard let self else { return }
  foo()

  couldCauseRetainCycle { [self] in
    bar()
  }
}
```