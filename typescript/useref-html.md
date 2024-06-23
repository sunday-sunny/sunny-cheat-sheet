# useRef에 제네릭으로 원하는 HTML 타입 넣기



Dropdown, Selectbox 등에서 컴포넌트의 바깥 부분을 클릭하면 자동으로 컴포넌트를 닫히게 해주는 `useClickAway`라는 훅을 사용 중이었다.

내부에서 `isClicked`, `onToggle`, `clickRef`를 만들어서 반환해주던 훅인데 JS에서는 어떤 ref를 넘겨주던 상관 없었지만 타입 스크립트로 전환하면서 반환받는 ref 타입이 맞지 않아 오류가 났다.



### 오류1. 임의의 한 타입으로 생성하여 사용

가령 `<div>` 태그를 사용하는 Selectbox 컴포넌트에서 사용하기 위해 훅 내의 useRef를 `HTMLDivElement` 타입으로 생성하여 사용하면 에러가 나지 않지만, 이 훅을 `<button>` 태그를 사용하는 Button 컴포넌트에 사용한다면 에러가 난다.&#x20;

{% hint style="danger" %}
Type 'RefObject\<HTMLDivElement>' is not assignable to type 'LegacyRef\<HTMLButtonElement> | undefined'.
{% endhint %}

`HTMLDivElement` 타입 ref에는 \<button> 태그에 있는 disabled, form, formAction 등의 property들이 없기 때문에 할당 할 수 없다는 에러가 난다.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% tabs %}
{% tab title="useClickAway.tsx" %}
```typescript
export default function useClickAway() {
    const [isClicked, setIsClicked] = useState<boolean>(false);
    const clickRef = useRef<HTMLDivElement>(null);

    const handleClickAway = (e: MouseEvent) => {
        if (!clickRef.current?.contains(e.target as HTMLElement)) {
            setIsClicked(false);
        }
    };

    const onToggle = () => {
        setIsClicked((prev) => !prev);
    };

    useEffect(() => {
        if (isClicked) {
            document.addEventListener('click', handleClickAway);
        } else {
            document.removeEventListener('click', handleClickAway);
        }
        return () => {
            document.removeEventListener('click', handleClickAway);
        };
    }, [isClicked]);

    return { isClicked, clickRef, onToggle };
}
```
{% endtab %}

{% tab title="SelectBox.tsx" %}
```tsx
const {
    isClicked: isSelectClicked,
    clickRef: selectRef,
    onToggle: onSelectToggle,
} = useClickAway();

return (
    <div className={s.selectContainer} ref={selectRef}>
        <button
            onClick={onClickTrigger}
            className={`${s.trigger} ${s[size]}`}
            type='button'
            style={inputStyle}
        >
            <p className={s.text} style={textStyle}>
                {value}
            </p>
            {isSelectClicked ? (
                <KeyboardArrowUpIcon className={s.icon} />
            ) : (
                <KeyboardArrowDownIcon className={s.icon} />
            )}
        </button>
        {isSelectClicked && (
            <SelectDropdown
                items={data}
                onClickItem={onClickItem}
                size={size}
            />
        )}
    </div>
);
```
{% endtab %}
{% endtabs %}



### 오류2. \<HTMLElement> 타입 사용

그렇다면 그냥 `HTMLElement` 타입으로 생성하여 반환하면 모든 태그에서 사용할 수 있지 않을까 생각했다. 하지만 여전히 동일하게 `HTMLElement` 타입에는 \<button> 태그에 존재하는 property들이 없기 때문에 불가능하다.

{% hint style="danger" %}
Type 'RefObject\<HTMLElement>' is not assignable to type 'LegacyRef\<HTMLButtonElement> | undefined'.
{% endhint %}

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```typescript
export default function useClickAway() {
    const [isClicked, setIsClicked] = useState<boolean>(false);
    const clickRef = useRef<HTMLElement>(null);

    const handleClickAway = (e: MouseEvent) => {
        if (!clickRef.current?.contains(e.target as HTMLElement)) {
            setIsClicked(false);
        }
    };

    const onToggle = () => {
        setIsClicked((prev) => !prev);
    };

    useEffect(() => {
        if (isClicked) {
            document.addEventListener('click', handleClickAway);
        } else {
            document.removeEventListener('click', handleClickAway);
        }
        return () => {
            document.removeEventListener('click', handleClickAway);
        };
    }, [isClicked]);

    return { isClicked, clickRef, onToggle };
}
```



### 해결. useClickAway 훅을 제네릭으로 설정

원하는 타입을 넘겨 해당 타입의 ref를 만들고 싶은데 어떡하지, 사용하는 쪽에서는 ref를 만들어 파라미터로 넘겨야 하나 싶었는데 딱 발견한  제네릭.

훅에 `<T extemds HTMLElement>` 로 HTML 타입으로 타입을 제한하고, 사용하는 쪽에서는 원하는 HTML 타입을 넘기도록 하였다. 에러도 나지 않고 코드도 깔끔하다!

{% tabs %}
{% tab title="useClickAway.tsx" %}
```typescript
export default function useClickAway<T extends HTMLElement>() {
    const [isClicked, setIsClicked] = useState<boolean>(false);
    const clickRef = useRef<T>(null);

    const handleClickAway = (e: MouseEvent) => {
        if (!clickRef.current?.contains(e.target as HTMLElement)) {
            setIsClicked(false);
        }
    };

    const onToggle = () => {
        setIsClicked((prev) => !prev);
    };

    useEffect(() => {
        if (isClicked) {
            document.addEventListener('click', handleClickAway);
        } else {
            document.removeEventListener('click', handleClickAway);
        }
        return () => {
            document.removeEventListener('click', handleClickAway);
        };
    }, [isClicked]);

    return { isClicked, clickRef, onToggle };
}
```
{% endtab %}

{% tab title="사용 컴포넌트" %}
```typescript
const {
    isClicked,
    clickRef: buttonRef,
    onToggle,
} = useClickAway<HTMLButtonElement>();
```

```typescript
const {
    isClicked: isSelectClicked,
    clickRef: selectRef,
    onToggle: onSelectToggle,
} = useClickAway<HTMLDivElement>();
```
{% endtab %}
{% endtabs %}



#### 참고

* [https://stackoverflow.com/questions/61102101/cannot-assign-refobjecthtmldivelement-to-refobjecthtmlelement-instance/64151312#64151312](https://stackoverflow.com/questions/61102101/cannot-assign-refobjecthtmldivelement-to-refobjecthtmlelement-instance/64151312#64151312)

