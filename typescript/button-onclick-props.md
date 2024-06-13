# button에 onClick props 타입 정의하기

자바스크립트에서 타입스크립트 마이그레이션 중 Button 컴포넌트에 onClick를 넘기려는 이런저런 분투기.



### 에러.  Type 'void' is not assignable to type 'MouseEventHandler | undefined'.

{% hint style="danger" %}
<mark style="color:red;">Type 'void' is not assignable to type 'MouseEventHandler | undefined'.</mark>
{% endhint %}



커스텀 `<Button/>` 컴포넌트는 `onClick` 함수를 받아 내부의 `onClick` 속성에넣어주게 되어있다.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: () => void;
    disabled?: boolean;
}

function Button({
    content = 'button',
    onClick = undefined,
    disabled = false,
}: ButtonProps) {
    return (
        <div>
            <button
                className={`${s.button}`}
                disabled={disabled}
                onClick={onClick}
            >
                <span>{content}</span>
            </button>
        </div>
    );
}
```

```typescript
<Button
    content='이름 변경'
    onClick={onClickRename(asset.name)}
/>
```



자바스크립트에서 하던 것처럼 함수 이름만 넣었는데 바로 에러가 나버린다.&#x20;

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

이유는 onClick props의 타입은 `()=> void` 인 함수인데 현재 넘기고 있는 건 `onClickRename(asset.name)`이 실행된 return 값이기 때문이다. (void)

```typescript
<Button
    content='이름 변경'
    onClick={() => onClickRename(asset.name)}
/>
```

정의한 타입인 `()=>void` 에 맞게 익명 함수로만들어 넘겨준다.



### onClick의 event를 인자로받을 경우

`<Button/>` 컴포넌트는 `e.target.value` 등으로 value 값을 받기 보다는 `onClick` 이후에 다른 함수를 부르거나 액션을 취할 경우가 많아 인자로 event를 정의하지는 않았다. 하지만 클릭했을 때 event를 받고 싶다면 인자로  event를 주고 타입을 정의해주자.

```typescript
interface ButtonProps {
    content?: string;
    onClick?: (e: MouseEvent<HTMLButtonElement>) => void;
    disabled?: boolean;
}
```

<pre class="language-typescript"><code class="lang-typescript">const onClickRename = (e: MouseEvent&#x3C;HTMLButtonElement>, assetType: string) => {
    console.log({ e });
    console.log({ assetType });
};
<strong>
</strong><strong>&#x3C;Button
</strong>    content='이름 변경'
    onClick={(e) => onClickRename(e, asset.name)}
/>
</code></pre>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>





#### 참고

* [https://stackoverflow.com/questions/73581794/how-to-solve-type-void-is-not-assignable-to-type-mouseeventhandlerhtmlelemen](https://stackoverflow.com/questions/73581794/how-to-solve-type-void-is-not-assignable-to-type-mouseeventhandlerhtmlelemen)



