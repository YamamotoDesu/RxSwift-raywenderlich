# RxSwift-raywenderlich
[Getting Started With RxSwift and RxCocoa](https://www.raywenderlich.com/1228891-getting-started-with-rxswift-and-rxcocoa)

![RocketSim_Recording_iPhone_12_2022-05-30_23 22 37](https://user-images.githubusercontent.com/47273077/171011889-755d93be-e440-40e5-960a-41f4bf7b7ed7.gif)

## RxSwift: Making the Cart Count Reactive
### [RxSwift](https://github.com/YamamotoDesu/RxSwift-raywenderlich/compare/main...RxSwift)


## RxCocoa: Making the TableView Reactive
### [RxCocoa](https://github.com/YamamotoDesu/RxSwift-raywenderlich/compare/RxSwift...RxCocoa)
```swift
  func setupCartObserver() {
    //1
    ShoppingCart.sharedCart.chocolates.asObservable()
      .subscribe(onNext: { //2
        [unowned self] chocolates in
        self.cartButton.title = "\(chocolates.count) 🍫"
      })
      .disposed(by: disposeBag) //3
  }
  
  func setupCellConfiguration() {
    //1
    europeanChocolates
      .bind(to: tableView
        .rx //2
        .items(cellIdentifier: ChocolateCell.Identifier,
               cellType: ChocolateCell.self)) { //3
                row, chocolate, cell in
                cell.configureWithChocolate(chocolate: chocolate) //4
      }
      .disposed(by: disposeBag) //5
  }
  
  func setupCellTapHandling() {
    tableView
      .rx
      .modelSelected(Chocolate.self) //1
      .subscribe(onNext: { [unowned self] chocolate in // 2
        let newValue =  ShoppingCart.sharedCart.chocolates.value + [chocolate]
        ShoppingCart.sharedCart.chocolates.accept(newValue) //3
          
        if let selectedRowIndexPath = self.tableView.indexPathForSelectedRow {
          self.tableView.deselectRow(at: selectedRowIndexPath, animated: true)
        } //4
      })
      .disposed(by: disposeBag) //5
  }

```

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
