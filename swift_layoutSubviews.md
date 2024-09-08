## layoutSubViews() 
- UIView 클래스의 메소드로 뷰와 그 서브뷰들의 위치와 크기를 조정하는데 사용

1. 서브뷰의 크기나 위치를 동적으로 조정할때
   - 부모 뷰의 크기 변경에 따라 서브뷰의 크기나 위치를 조정해야할 때
   - 콘텐츠의 크기에 따라 뷰의 레이아웃을 동적으로 조정해야할 때
2. Auto Layout으로 해결하기 어려운 복잡한 레이아웃 구현
   - 특별한 수학적 계산이 필요한 레이아웃
   - 매우 동적이거나 복잡한 레이아웃 로직
3. 성능 최적화가 필요한 경우
   - 많은 수의 서브뷰를 다룰 때 Auto Layout 보다 더 호율적인 수동 레이아웃이 필요한 경우
4. 레이아웃 관련 애니메이션 구현
   - 특정 레이아웃 변경을 애니메이션화할 때
5. 기기 회전이나 크기 클래스 변경에 대응
   - 기기 방향이나 크기 클래스가 변경될 때 레이아웃을 조정해야하는 경우
6. 서브클래싱한 UIkit 컴포넌트의 기본 동작 수정
   - UIKit 컴포넌트의 기본 레이아웃 동작을 수정하거나 확장할 때
7. 특정 뷰의 내부 레이아웃 로직 캡슐화
   - 복잡한 뷰의 내부 레이아웃 로직을 캡슐화하여 관리하고자 할 때
     
```swift
class CustomView: UIView {
    override func layoutSubviews() {
        super.layoutSubviews()
        
        // 부모 뷰의 크기에 따라 서브뷰 크기 조정
        let size = bounds.size
        subview1.frame = CGRect(x: 0, y: 0, width: size.width / 2, height: size.height)
        subview2.frame = CGRect(x: size.width / 2, y: 0, width: size.width / 2, height: size.height)
        
        // 복잡한 레이아웃 로직
        let centerX = bounds.midX
        let centerY = bounds.midY
        circularView.center = CGPoint(x: centerX + cos(angle) * radius, y: centerY + sin(angle) * radius)
    }
}

```
### 주의사항 
- layoutSubviews() 는 레이아웃 주기 동안 여러 번 호출될 수 있으므로, 이 메서드 내에서 무거운 계산이나 비용이 많이 드는 작업을 수행하지 않도록 주의해야한다. 
- 가능한 한 Auto Layout을 사용하고 , layoutSubviews() 는 Auto Layout으로 해결하기 어려운 특별한 경우에만 사용하는 것이 좋다.
- layoutSubviews() 내에서 직접적으로 제약 조건을 변경하거나 setNeedsLayout()을 호출하지 않도록 주의해야 합니다. 이는 레이아웃 루프를 발생시킬 수 있습니다.
- layoutSubviews()를 적절히 활용하면 복잡한 레이아웃 요구사항을 효과적으로 처리할 수 있지만, 과도한 사용은 성능 문제를 일으킬 수 있으므로 신중하게 사용해야 합니다.
