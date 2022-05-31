# RxSwift-raywenderlich
[Getting Started With RxSwift and RxCocoa](https://www.raywenderlich.com/1228891-getting-started-with-rxswift-and-rxcocoa)

![RocketSim_Recording_iPhone_12_2022-05-30_23 22 37](https://user-images.githubusercontent.com/47273077/171011889-755d93be-e440-40e5-960a-41f4bf7b7ed7.gif)

## RxSwift: Making the Cart Count Reactive
### [RxSwift](https://github.com/YamamotoDesu/RxSwift-raywenderlich/compare/main...RxSwift)


## RxCocoa: Making the TableView Reactive
### [RxCocoa](https://github.com/YamamotoDesu/RxSwift-raywenderlich/compare/RxSwift...RxCocoa)

## RxSwift: Direct Text Input
### [RxSwift](https://github.com/YamamotoDesu/RxSwift-raywenderlich/compare/RxCocoa...RxSwift-Direct-Text-Input)
```swift
private extension BillingInfoViewController {
  func setupCardImageDisplay() {
    cardType
      .asObservable()
      .subscribe(onNext: { [unowned self] cardType in
        self.creditCardImageView.image = cardType.image
      })
      .disposed(by: disposeBag)
  }
  
  func setupTextChangeHandling() {
    let creditCardValid = creditCardNumberTextField
      .rx
      .text //1
      .observeOn(MainScheduler.asyncInstance)
      .distinctUntilChanged()
      .throttle(.milliseconds(throttleIntervalInMilliseconds), scheduler: MainScheduler.instance) //2
      .map { [unowned self] in
        self.validate(cardText: $0) //3
    }
      
    creditCardValid
      .subscribe(onNext: { [unowned self] in
        self.creditCardNumberTextField.valid = $0 //4
      })
      .disposed(by: disposeBag) //5
    
    let expirationValid = expirationDateTextField
      .rx
      .text
      .observeOn(MainScheduler.asyncInstance)
      .distinctUntilChanged()
      .throttle(.milliseconds(throttleIntervalInMilliseconds), scheduler: MainScheduler.instance)
      .map { [unowned self] in
        self.validate(expirationDateText: $0)
    }
        
    expirationValid
      .subscribe(onNext: { [unowned self] in
        self.expirationDateTextField.valid = $0
      })
      .disposed(by: disposeBag)
        
    let cvvValid = cvvTextField
      .rx
      .text
      .observeOn(MainScheduler.asyncInstance)
      .distinctUntilChanged()
      .map { [unowned self] in
        self.validate(cvvText: $0)
    }
        
    cvvValid
      .subscribe(onNext: { [unowned self] in
        self.cvvTextField.valid = $0
      })
      .disposed(by: disposeBag)
    
    let everythingValid = Observable
      .combineLatest(creditCardValid, expirationValid, cvvValid) {
        $0 && $1 && $2 //All must be true
    }
        
    everythingValid
      .bind(to: purchaseButton.rx.isEnabled)
      .disposed(by: disposeBag)
  }
}
```
