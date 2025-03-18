## Dehydration
#### 서버(Next.js) 에서 React Query의 데이터를 미리 가져와 JSON 형태로 직렬화하는 과정
- 서버(Next.js) 에서 React Query를 사용하여 데이터를 미리 가져옴.
- 데이터를 JSON 형태로 변환하여 클라이언트에 전달
- Next.js 의 getServerSideProps 에서 사용

```javascript
export const getServerSideProps: GetServerSideProps = async (context) => {
  const queryClient = new QueryClient();
  try {
    // 1. 서버에서 장바구니 데이터 미리 가져오기
    await queryClient.prefetchQuery({
      queryKey: queryKeyCart,
      queryFn: () =>
        getRequest<CartResponse>({
          url: API_ENDPOINTS.CART,
          config: {
            headers: {
              Authorization: `Bearer ${token}`,
              Cookie: context.req.headers.cookie,
            },
          },
        }).then(res => res.data),
    });

    // 2. 가져온 데이터를 JSON 형태로 직렬화 (Dehydrate)
    return {
      props: {
        dehydratedState: dehydrate(queryClient), // JSON 형태로 변환하여 클라이언트로 전달
      },
    };
  } catch (error) {
    return {
      redirect: {
        destination: '/500',
        permanent: false,
      },
    };
  }
};
```

## Hydration 
#### 서버에서 전달된 JSON 데이터를 클라이언트에서 React Query 상태로 복원하는 과정 
- 클라이언트는 dehydratedState 를 받아서, HydrationBoundary를 사용하여 데이터를 복원함
- 불필요한 API 호출 방지 가능

```javascript
import { HydrationBoundary } from '@tanstack/react-query';

interface CartProps {
  dehydratedState: any; // 서버에서 받은 dehydrated 데이터
}

export default function CartPage({ dehydratedState }: CartProps) {
  return (
    // 1. 클라이언트에서 하이드레이션 적용
    <HydrationBoundary state={dehydratedState}>
      <CartContent />
    </HydrationBoundary>
  );
}
```

## 이점 
1) 클라이언트에서 불필요한 API 요청을 방지
- 서버에서 이미 데이터를 가져왔기 때문에, 클라이언트가 동일한 데이터를 다시 요청할 필요가 없음
- Next.js 페이지가 로드될 때 장바구니 데이터를 서버에서 가져오고, 클라이언트에서 이를 재사용

2) SSR(서버사이드 렌더링) 성능 향상
- SEO 에 유리하며, 초기 페이지 로딩 속도를 향상시킴
- 사용작 경험(UX)이 좋아짐 -> 서버에서 미리 데이터를 가져오므로 로딩이 짧아짐.

3) Hydration 오류 방지
- 서버와 클라이언트가 동일한 데이터를 가지도록 유지하여 Hydration Mismatch 오류 방지.



