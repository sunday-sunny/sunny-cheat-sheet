# 특정 컴포넌트 타입으로 props 타입 지정하기

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

```typescript
<MenuButton
    content='Account'
    dropdown={<Dropdown listItem={dropdownList()} />}
/>
```

`<MenuButton/>` 컴포넌트는 클릭하면 Dropdown이 뜨게 되어있는데 여기에 Dropdown 컴포넌트 외에는 다른 컴포넌트나 HTML 태그가 오지 않게 지정하고 싶다.



### typeof로 컴포넌트 타입 정의해주기

`typeof`로 해당 컴포넌트를 type으로 만들어주고, 이 타입을 `ReactElement`에 넘겨준다.

```typescript
import Dropdown from '../Dropdown/Dropdown';

type DropdownType = typeof Dropdown;

interface MenuButtonProps {
    content?: string;
    disabled?: boolean;
    dropdown: ReactElement<DropdownType>;
}
```

dropdown prop에 마우스를 올려보면 Dropdown을 반환하는 JSX.Element가 정의된 것을 볼 수 있다.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>





#### 참고

* [https://stackoverflow.com/a/57628053](https://stackoverflow.com/a/57628053)
* [https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-keyof-typeof-%EC%82%AC%EC%9A%A9%EB%B2%95](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-keyof-typeof-%EC%82%AC%EC%9A%A9%EB%B2%95)

