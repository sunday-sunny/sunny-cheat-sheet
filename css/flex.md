# flex 남는 공간 채우기

InputCombination을 사용할 때마다 까먹어서 적어놓는 flex 남는 공간 채우기

Label과 Input 관련 컴포넌트를 props로 받아 콤보로 사용하는 컴포넌트가 있는데 매번 저 input 컴포넌트가 상위에 지정해놓은 width만큼 안 넓어지고 짧둥하게 만들어진다.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

```typescript
return (
    <div className={`${s.container} ${s[align]}`}>
        {label}
        <div className={s.component}>
            {component}
            {helpMessage && <p className={s.helpMessage}>{helpMessage}</p>}
        </div>
    </div>
);
```



### 방법1. flex-grow 사용

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

상위 태그에 flex 지정 후, 남은 여백을 채우고 싶은 컴포넌트에 `flex-grow: 1` 속성을 부여한다.

```scss
.container {
    width: 100%;

    &.horizontal {
        display: flex;
        gap: 8px;
        
        // Label
        > :first-child {
            min-width: 184px;
        }
    }
    .component {
        flex-grow: 1;
        display: flex;
        flex-direction: column;
        gap: 4px;
        
        .helpMessage {
            color: colors.$NETURAL_LIGHT_500;
            @include typography.TYPO_HELPER_01_NORMAL;
        }
    }
}
```



### 방법2. gird-template-columns에서 1fr 사용

grid를 사용 후 `grid-template-columns`에 여백을 채우고 싶은 column에 `1fr`을 지정한다.

```scss
.container {
    width: 100%;

    &.horizontal {
        display: grid;
        grid-template-columns: 184px 1fr;
        gap: 8px;
    }
    .component {
        display: flex;
        flex-direction: column;
        gap: 4px;

        .helpMessage {
            color: colors.$NETURAL_LIGHT_500;
            @include typography.TYPO_HELPER_01_NORMAL;
        }
    }
}
```

개인적으로는 Label의 `min-width`가 지정되어야해서 grid로 가는게 깔끔하다.





#### 참고

* [https://apost.dev/863/](https://apost.dev/863/)









