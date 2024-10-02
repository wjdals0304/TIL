## iOS 앱 상태 

1. Not Running (실행되지 않음):
   - 앱이 실행되지 않았거나 시스템에 의해 종료된 상태
2. Inactive (비활성화):
   - 앱이 foreground 에서 실행 중이지만 이벤트를 받지 않는 상태
   ex) 전화가 오거나 시스템 알림이 표시될 때
3. Active(활성):
   - 앱이 foreground 에서 실행 중이며 이벤트를 받을 수 있는 상태
   - 사용자가 앱과 상호작용할 수 있는 정상적인 실행 상태
4. Background(백그라운드):
   - 앱이 background 에서 실행 중인 상태
   - 제한된 시간 동안 코드를 실행할 수 있음
   - 특정 백그라운드 모드(음악 재생, 위치 업데이트)를 사용하면 더 오래 실행될 수 
5. Suspended (일시 중단):
   - 앱이 backgroud에 있지만 코드를 실행하지 않는 상태
   - 시스템이 메모리 압박을 받으면 이 상태의 앱을 종료할 수 있음

### 앱 상태 전환
1. 앱 실행 시: Not Running -> Inactive -> Active
2. 홈 버튼을 누르거나 다른 앱으로 전환 시: Active -> Inactive -> Background -> (시간 경과) -> Suspended
3. 앱으로 다시 돌아올 때 : Background -> Inactive -> Active
4. 앱 종료 시: Any State -> Not Running


- `AppDelegate`와 `SceneDelegate`의 메서드들은 이러한 상태 변화를 감지하고 대응하는 데 사용됩니다.
- 각 상태에 따라 적절한 작업을 수행해야 합니다. 예를 들어, background 상태에서는 중요한 데이터를 저장하고, active 상태에서는 UI를 새로고침할 수 있습니다.
- iOS 13 이후로는 SceneDelegate를 통해 멀티 윈도우 지원이 가능해져, 동일한 앱의 여러 인스턴스가 서로 다른 상태를 가질 수 있게 되었습니다.


