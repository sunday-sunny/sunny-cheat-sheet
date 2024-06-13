# reduce 함수 not assignable to parameter of type 'never'. 에러

나는 reduce 함수를 보통원본 배열에서 필요한 항목만 빼서 새로운 배열을 구성하는데 사용한다.

일반 자바스크립트에서 사용하는 것처럼 하니 바로 뜨는 에러.

{% hint style="danger" %}
Argument of type '{ name: string; category: string; }' is not assignable to parameter of type 'never'.ts(2345)
{% endhint %}

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



### initialValue 타입 지정해주기

초기 값을 `[]` 배열로 지정하였는데, 이 배열의 타입이 지정 되어 있지 않아서 할당 할 수 없다고 에러가 났다.  `as`를 이용하여 타입 단언을 해주었다.&#x20;

```typescript
const dropdownList = fileTypeList.reduce((list, file) => {
    list.push({ name: file.name, category: file.category });
    return list;
}, [] as { name: string; category: string }[]);
```



#### 참고

* [https://velog.io/@lgj9172/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C%EC%9D%98-reduce-%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95](https://velog.io/@lgj9172/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C%EC%9D%98-reduce-%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95)

