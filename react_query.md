## ReactQuery 
React 애플리케이션에서 서버 상태를 효율적으로 관리할 수 있도록 도와주는 라이브러리 
비동기 데이터를 간편하게 다룰 수 있으며, API 요청을 최적화하여 UX를 개선할 수 있습니다.


### 주요기능
1. 데이터 패칭
- useQuery 를 사용하면 간단하게 데이터를 가져올 수 있음
- API 호출 후 데이터를 캐시에 저장하여 불필요한 중복 요청을 방지

2. 자동캐싱 & 리페칭
- 자동으로 캐시를 관리하여 데이터 요청을 최적화
- 설정한 staleTime 이 지나면 자동으로 최신 데이터로 업데이트

3. 데이터 변이
- useMutation 을 사용하면 데이터를 변경하는 요청을 쉽게 처리할 수 있음

```javascript
  const mutation = useMutation({
    mutationFn: updateUser,
    onSuccess: () => {
      queryClient.invalidateQueries(['user']); // 성공하면 캐시 업데이트
    },
  });
```

4. 데이터 프리페칭
- prefetchQuery를 사용하여 페이지 이동 전에 데이터를 미리 가져올 수 있음


### fetchQuery vs prefetchQuery 차이점 
1. fetchQuery
- 즉시 데이터를 가져오고 반환
- try-catch 에서 오류를 잡고 직접 데이터 활용 가능
- 특정 시점에 데이터를 필요할 때 사용

2. prefetchQuery
- 비동기적으로 데이터를 캐시에만 저장하고 반환값이 없음
- 이후 useQuery를 사용할 때 캐시된 데이터를 즉시 반환할 수 있도록 사전 로드
- 페이지 이동 전에 미리 데이터 불러오기할 때 사용

