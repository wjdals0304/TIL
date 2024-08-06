## @State
- SwiftUI 에서 상태를 처리하는 방법 
- 뷰의 상태를 저장하는 프로퍼티로 상태 관리 주체는 해당 뷰 
- 기본적으로 Private 선언이기에 다른 뷰와 값을 소통하려면 Binding 을 이용 
- 값이 변경될 때마다 UI 업데이트 

```
import SwiftUI

struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
            
            Button("Increment") {
                count += 1
            }
            
            Button("Decrement") {
                count -= 1
            }
        }
    }
}
@State private var count = 0로 카운트 상태를 선언합니다.
Text 뷰에서 현재 카운트 값을 표시합니다.
"Increment" 버튼을 누르면 카운트가 증가하고, "Decrement" 버튼을 누르면 감소합니다.
카운트 값이 변경될 때마다 SwiftUI가 자동으로 뷰를 업데이트합니다.

@State는 간단한 데이터 타입(Int, String, Bool 등)에 주로 사용되며, 
복잡한 데이터 모델이나 여러 뷰에서 공유해야 하는 데이터의 경우 다른 상태 관리 방식(@ObservedObject, @EnvironmentObject 등)을 고려해야 합니다.
```
## @Binding
- 뷰와 상태를 바인딩하는 방법
- 상위 @State 변수를 전달 받아 하위 뷰에서 캐치해 변화 감지 및 연결
- Binding 은 다른 뷰가 소유한 속성을 연결하기에 소유권 및 저장 공간이 없음 
```
import SwiftUI

struct ParentView: View {
    @State private var isOn = false
    
    var body: some View {
        VStack {
            Text(isOn ? "On" : "Off")
            ToggleView(isOn: $isOn)
        }
    }
}

struct ToggleView: View {
    @Binding var isOn: Bool
    
    var body: some View {
        Toggle("Toggle", isState: $isOn)
    }
}
ParentView는 @State로 isOn 상태를 관리합니다.
ToggleView는 @Binding을 사용하여 isOn 상태에 대한 참조를 받습니다.
ParentView에서 ToggleView로 상태를 전달할 때 $isOn과 같이 달러 기호를 사용하여 바인딩을 생성합니다.
ToggleView 내의 Toggle은 이 바인딩을 사용하여 상태를 읽고 수정합니다.
ToggleView에서 상태를 변경하면 ParentView의 상태도 자동으로 업데이트됩니다.
```
## ObservableObject 
- 클래스 프로토콜로 관찰하는 어떠한 값이 변경되면 변경사항을 알려줌
- 뷰에서 인스턴스 변화를 감시하기 위해 뷰모델 객체로 생성할 때 사용할 수 있음 
- 주로 viewmodel 에서 사용
- 클래스에 적용되는 프로토콜입니다.
- @Published 프로퍼티 래퍼를 사용하여 변경 사항을 자동으로 발행합니다.
- 뷰에서 @ObservedObject 또는 @StateObject로 사용될 수 있습니다.

```
import SwiftUI

class UserSettings: ObservableObject {
    @Published var username: String = ""
    @Published var isLoggedIn: Bool = false
    
    func login() {
        // 로그인 로직
        isLoggedIn = true
    }
    
    func logout() {
        // 로그아웃 로직
        isLoggedIn = false
        username = ""
    }
}

struct ContentView: View {
    @StateObject private var userSettings = UserSettings()
    
    var body: some View {
        VStack {
            if userSettings.isLoggedIn {
                Text("Welcome, \(userSettings.username)!")
                Button("Logout") {
                    userSettings.logout()
                }
            } else {
                TextField("Username", text: $userSettings.username)
                Button("Login") {
                    userSettings.login()
                }
            }
        }
    }
}
UserSettings 클래스는 ObservableObject 프로토콜을 채택합니다.
@Published 프로퍼티 래퍼를 사용하여 username과 isLoggedIn 상태를 선언합니다.
login()과 logout() 메서드를 통해 상태를 변경합니다.
ContentView에서 @StateObject를 사용하여 UserSettings 인스턴스를 생성하고 관리합니다.
뷰는 userSettings의 상태 변화에 따라 자동으로 업데이트됩니다.
```
## @Published 
- ObservableObject 를 구현한 클래스 내에서 프로퍼티 선언 시 사용 
- @Published로 선언된 프로퍼티를 뷰에서 관찰할 수 있음 
- ObservableObject의 objectWillChange.send() 기능을 @Published 프로퍼티가 변경되면 자동으로 호출 

## @ObservableObject 
- 뷰에서 ObservableObject 타입의 인스턴스 선언 시 사용
- ObservableObject의 값이 업데이트되면 뷰를 업데이트
```
import SwiftUI
import Combine

class UserModel: ObservableObject {
    @Published var name: String = ""
    @Published var age: Int = 0
    
    func updateUserInfo(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}
struct UserView: View {
    @ObservedObject var user: UserModel
    
    var body: some View {
        VStack {
            Text("Name: \(user.name)")
            Text("Age: \(user.age)")
            Button("Update User") {
                user.updateUserInfo(name: "John Doe", age: 30)
            }
        }
    }
}
struct ContentView: View {
    @StateObject private var userModel = UserModel()
    
    var body: some View {
        UserView(user: userModel)
    }
}

UserModel 클래스는 ObservableObject 프로토콜을 채택합니다.
@Published 프로퍼티 래퍼를 사용하여 name과 age 프로퍼티를 선언합니다.
updateUserInfo 메서드를 통해 사용자 정보를 업데이트합니다.
UserView에서 @ObservedObject를 사용하여 UserModel 인스턴스를 관찰합니다.
ContentView에서 @StateObject를 사용하여 UserModel 인스턴스를 생성하고 관리합니다.

ObservableObject는 주로 @ObservedObject나 @StateObject와 함께 사용됩니다:

@ObservedObject: 부모 뷰에서 전달받은 ObservableObject 인스턴스를 관찰할 때 사용합니다.
@StateObject: 뷰 내에서 ObservableObject 인스턴스를 직접 생성하고 관리할 때 사용합니다.

```

## @StateObject 
- 뷰에서 ObservableObject 타입의 인스턴스 선언 시 사용 
- 뷰마다 하나의 인스턴스를 생성하며, 뷰가 사라지기 전까지 같은 인스턴스 유지 
- @ObservedObject의 뷰 렌더링 시 인스턴스 초기화 이슈 해결을 위한 방법 
- 매번 인스턴스가 새롭게 생성되는것처럼 외부에서 주입 받는 경우가 아닌 
최초 생성 선언 시에 @StateObject를 사용하는것이 적절한 방법 
```
import SwiftUI

class CounterModel: ObservableObject {
    @Published var count = 0
    
    func increment() {
        count += 1
    }
}

struct CounterView: View {
    @StateObject private var counter = CounterModel()
    
    var body: some View {
        VStack {
            Text("Count: \(counter.count)")
            Button("Increment") {
                counter.increment()
            }
        }
    }
}

struct ContentView: View {
    var body: some View {
        CounterView()
    }
}
CounterModel은 ObservableObject 프로토콜을 준수합니다.
CounterView에서 @StateObject를 사용하여 CounterModel 인스턴스를 생성하고 관리합니다.
버튼을 탭하면 카운터가 증가하고, 뷰가 업데이트됩니다.
```
## @Environment 
- 미리 정의되어 있는 시스템 공유 데이터 
- 사용하려는 공유 데이터의 이름을 keyPath로 전달하여 사용 
- 시스템 공유 데이터는 가변하기에 var로 선언 필요 
- 뷰가 생성되는 시점에 값이 자동으로 초기화됨 

## @EnvironmentObject 
- ObservableObject를 통해 구현된 타입의 인스턴스를 전역적으로 공유하여 사용 
- 앱 전역에서 공통으로 사용할 데이터를 주입 및 사용 
EnvironmentObject를 사용하는 뷰는 반드시 해당 객체가 환경에 주입되어 있어야 합니다. 그렇지 않으면 런타임 에러가 발생합니다.
너무 많은 데이터를 EnvironmentObject로 관리하면 앱의 구조를 이해하기 어려워질 수 있습니다.


