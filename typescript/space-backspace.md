# 키보드 이벤트 (space/Backspace)

## keyboardEventHandler

value값을 변경할 때 쓰는 `onChange`와 다르게 키보드 이벤트 감지는 `onKeyDown`과 `keyboardEvnentHandler`를 사용한다.

```tsx
interface TextFieldProps {
    ...
    onChangeHandler?: React.ChangeEventHandler<HTMLInputElement>;
    onKeyDownHandler?: React.KeyboardEventHandler<HTMLInputElement>;
    ...
}

return (
    <input
        ...
        onChange={onChangeHandler}
        onKeyDown={onKeyDownHandler}
        ...
    />
);
```



## keyboardEvent\<HTMLInputElement>

받는 이벤트는 `keyboardEvent<HTMLInputElement>.`  e.key로 key 값을 감지한다 .

* 스페이스 : `e.key === ' '`
* 백 스페이스: `e.key === 'Backspace'`

```typescript
const onKeyDownHandler = (
    e: React.KeyboardEvent<HTMLInputElement>
) => {
    const { value } = e.currentTarget;

    // 스페이스 바 event
    if (e.key === ' ') {
        
    }
    
    // 백 스페이스 바 event
    if (e.key === 'Backspace') {
        
    }
};
```

참고로 `keyboardEvent`에서 value를 알고 싶으면 `e.target`이 아니라 `e.currentTarget`을 사용해야 한다.



## e.key는 string

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

어디서 본 `e.key === 32` 가 먹히지 않아 위 링크 ([https://www.w3.org/TR/uievents-key/#named-key-attribute-values](https://www.w3.org/TR/uievents-key/#named-key-attribute-values))를 타고 들어가서 보니 스페이스바는 그냥 `" "` 이다. &#x20;

key 값이 string이라 애초에 숫자 값은 들어가지 않는다. console.log로 찍어봐도 스페이스 바는 공백으로 출력된다.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>



#### 참고

* [https://www.w3.org/TR/uievents-key/#named-key-attribute-values](https://www.w3.org/TR/uievents-key/#named-key-attribute-values)







