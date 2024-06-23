---
description: tabindex 속성과 :focus-within을 이용한 <div> 태그에 포커스 주기
---

# HTML tabindex 속성으로 \<div>에 포커스 주기

기본적으로 `<div>` 태그는 포커스가 되지 않으나 input 컴포넌트를 `<div>` 태그로 한 번 감싸서 사용하게 되면서 `<div>`에 focus를 줘야만 되었다.



## tabindex 속성

사용자 인터랙션을 할 수 없는 요소에 포커스를 주고 싶으면 `tabindex` 속성을 부여하면 된다.

<table><thead><tr><th width="199">tabindex</th><th></th></tr></thead><tbody><tr><td>tabindex = 0</td><td>포커스가 불가한 태그에 포커스 부여</td></tr><tr><td>tabindex = -1</td><td>포커스가 원래 가능한 태그에 포커스 해제</td></tr><tr><td>tabindex = 양수</td><td>포커스 요소 간의 순서 제어</td></tr></tbody></table>

```tsx
return (
    <div className={s.TextField}>
        <div
            className={s.inputContainer}
            tabIndex={0}
        >
            {icon && <div className={s.icon}>{icon}</div>}
            <input
                value={value}
                onChange={onChangeHandler}
            />
        </div>
    </div>
);
```



## focus-within

focus-within은 본인이 포커스를 받았거나, 자식 중 하나가 포커스를 받았을 때 작동한다.&#x20;

컨테이너에  `:focus`를 지정할 경우 내부 자손을 선택했을 때는 포커스 스타일링이 안 먹힐 때가 있어 `:focus-within`으로 CSS를 지정해주었다.

```scss
.inputContainer {
    ...
    &:focus-within {
        border: 2px solid colors.$PURPLE_500;
        outline: none;
    }
}
```







#### 참고

* [https://www.daleseo.com/html-tabindex/](https://www.daleseo.com/html-tabindex/)
* [https://velog.io/@rlaugs15/div%EB%8A%94-focus%EA%B0%80-%EC%A0%81%EC%9A%A9%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94%EB%8B%A4](https://velog.io/@rlaugs15/div%EB%8A%94-focus%EA%B0%80-%EC%A0%81%EC%9A%A9%EB%90%98%EC%A7%80-%EC%95%8A%EB%8A%94%EB%8B%A4)
* [https://developer.mozilla.org/ko/docs/Web/CSS/:focus-within](https://developer.mozilla.org/ko/docs/Web/CSS/:focus-within)



