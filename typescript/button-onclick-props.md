# button에 onClick props 타입 정의하기

자바스크립트에서 타입스크립트 마이그레이션 중 Button 컴포넌트에 onClick를 넘기려고 이런저런 분투를 하다가 정리한 글.

타입스크립트로 바꾸면서 onClick에 뭘 줘야할지 너무 헷갈려서 정리를 하게됐다. `MouseEventHandler<HTMLButtonElement>` 를 주면 된다고 해서 했는데 왜 onClick을 넘길 때 자꾸 에러가 날까.. 자바스크립트 때는 그냥 마구마구 넘겼던 거 같은데 왜 자꾸 에러가 나지 했다.&#x20;

이렇게 보니 애초에 잘 모르고 막 사용하고 있던 거 같기도 하다. 이런게 바로 기본기 부족인걸까. 겸사겸사 여러가지 형태를 비교해보았다.



## \<button>의  onClick에 타입을 정의하는방법

### 방법1. MouseEventHandler\<HTMLButtonElement>

HTML button 태그의 `onClick` 프로퍼티의 값은 `MouseEventHandler<HTMLButtonElement>`이다. `onClick` 프로퍼티에 마우스를 올려놓으면 쉽게 확인할 수 있다.&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

이 타입을 그대로 props에 타입 정의에 넣어준다.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: MouseEventHandler<HTMLButtonElement>;
    disabled?: boolean;
}
```



#### 기본 사용

`MouseEventHandler<HTMLButtonElement>`는 아래와 같이 `(e)=>void` 형태이며 다른 매개변수가 없는 경우 아래처럼 사용이 가능하다.

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

```typescript
const onClickRename = (e: MouseEvent<HTMLButtonElement>) => {
    console.log({ e });
};

<Button
    content='이름 변경'
    onClick={onClickRename}
/>
```



물론 굳이 event 매개변수를 사용하지 않아도 된다.

```typescript
const onClickRename = () => {
    console.log('onClick');
};

<Button
    content='이름 변경'
    onClick={onClickRename}
/>
```



#### event 없이 다른 매개변수 사용

만약 event 인자는 사용 안하고 다른 매개변수를 사용해 함수를 정의하고 그대로 사용한다면 에러가 발생한다.

{% hint style="danger" %}
<mark style="color:red;">TS2322: Type 'void' is not assignable to type 'MouseEventHandler | undefined'.</mark>
{% endhint %}

아래와 같은 경우는 함수를 그대로 넘겨준게 아니라 함수가 실행되어 그 결과(void)를 넘기고 있기 때문이다.

```typescript
const onClickRename = (assetType: string) => {
    console.log({ assetType });
};

<Button
    content='이름 변경'
    onClick={onClickRename(asset.name)} // error !
/>
```

이럴 경우에는 익명 함수로 만들어 넘겨준다.

```typescript
const onClickRename = (assetType: string) => {
    console.log({ assetType });
};

<Button
    content='이름 변경'
    onClick={()=> onClickRename(asset.name)}
/>
```



#### event도 사용, 다른 매개변수도 사용

만약 event 인자도 사용하고, 그 외에 다른 인자도 사용한다면 익명 함수로 event를 받아 정의한 함수에 넘겨준다.

```typescript
const onClickRename = (e: MouseEvent<HTMLButtonElement>, assetType: string) => {
    console.log({ e });
    console.log({ assetType });
};

<Button
    content='이름 변경'
    onClick={(e) => onClickRename(e, asset.name)}
/>
```



### 방법2. 콜백 함수로 정의하여 사용

그냥 onClick을 했을 때 아무것도 하지 않는 일반 콜백 함수로 정의할 수도 있다. 보통 버튼 클릭은 `e.target.value` 등으로 value 값을 받기보다는 다른 함수를 실행하도록 하는 역할이 많으니까.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: () => void;
    disabled?: boolean;
}
```

이럴 경우 그대로 바인딩 가능.

```typescript
const onClickRename = () => {
    console.log('onClick');
};

<Button
    content='이름 변경'
    onClick={onClickRename}
/>
```



#### event 매개 변수 사용

event 매개변수를 받아서 사용해야겠다 하면 `MouseEventHandler<HTMLButtonElement>` 가 정의한대로 `(e)=>void` 로 사용하면 된다.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: (e: MouseEvent<HTMLButtonElement>) => void;
    disabled?: boolean;
}
```

역시나 그대로 바인딩하여 사용 가능.

```typescript
const onClickRename = (e: MouseEvent<HTMLButtonElement>) => {
    console.log({ e });
};

<Button
    content='이름 변경'
    onClick={onClickRename}
/>
```



#### 다른매개 변수 사용

다른 매개 변수를 사용한다면? `onClickRename(asset.name)`로 넘기면  `(e)=>void` 형태가 아니라 함수가 실행된 결과의 타입(void)이 넘어가므로 정의했던 `(e) => void` 형태로 익명 함수를 만들어서 넘겨준다. `onClickRename` 함수에서 e는 굳이 사용을 안하므로 익명 함수의 인자에서 제외하긴 했다.

```typescript
const onClickRename = (assetType: string) => {
    console.log({ assetType });
};

<Button
    content='이름 변경'
    onClick={()=> onClickRename(asset.name)}
/>
```





## 에러.  Type 'void' is not assignable to type 'MouseEventHandler | undefined'.

{% hint style="danger" %}
<mark style="color:red;">Type 'void' is not assignable to type 'MouseEventHandler | undefined'.</mark>
{% endhint %}

하면서 정말 시도 때도 없이 났던 이 에러. 이 문서를 정리하게 된 이유이기도 하다. 자바스크립트에서 하던 것처럼 함수 이름만 넣었는데도 에러가 나서 왜 그럴까 싶었다.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: (e: MouseEvent<HTMLButtonElement>) => void;
    disabled?: boolean;
}
```

```typescript
<Button
    content='이름 변경'
    onClick={onClickRename(asset.name)}
/>
```

이유는 위에서 설명했듯이  onClick props의 타입은 `(e)=> void` 인 함수인데 현재 넘기고 있는 건 `onClickRename(asset.name)`이 실행된 return 값이기 때문이다. (void)

```typescript
<Button
    content='이름 변경'
    onClick={() => onClickRename(asset.name)}
/>
```

정의한 타입인 `()=>void` 에 맞게 익명 함수로 만들어 넘겨준다.





#### 참고

* [https://stackoverflow.com/questions/73581794/how-to-solve-type-void-is-not-assignable-to-type-mouseeventhandlerhtmlelemen](https://stackoverflow.com/questions/73581794/how-to-solve-type-void-is-not-assignable-to-type-mouseeventhandlerhtmlelemen)
* [https://velog.io/@yukimiau/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%8B%A4%EC%A0%84-Prop](https://velog.io/@yukimiau/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%8B%A4%EC%A0%84-Prop)

