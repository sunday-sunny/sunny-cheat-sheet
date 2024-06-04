# react-complex-tree 조사

## react-complex-tree

리액트 tree view 라이브러리 중 하나. [Get Started](https://rct.lukasbach.com/docs/getstarted) 페이지에서  필요한 부분만 간략 요약한다.

* [https://rct.lukasbach.com](https://rct.lukasbach.com/docs/guides/styling)



## Install

```sh
yarn add react-complex-tree
```



## Importing

{% code overflow="wrap" %}
```javascript
import { UncontrolledTreeEnvironment, Tree, StaticTreeDataProvider } from 'react-complex-tree';
```
{% endcode %}

하나의 트리를 구축하기 위해 import 해야하는 모듈



## CSS

react-complex-tree에서 제공하는 기본 CSS를 사용하려면 CSS 파일을 불러와야한다.

* 자바스크립트 혹은 타입스크립트 파일 내에서 import

```typescript
import 'react-complex-tree/lib/style-modern.css';
```

* CSS 파일 내에서 import&#x20;

```css
@import '~react-complex-tree/lib/style-modern.css';
```



## 프로젝트 내에서 트리  세팅

tree view를 사용하려면 Tree 인스턴스가 포함된 새로운 Tree environment를 생성해야한다.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

```javascript
import {
    StaticTreeDataProvider,
    Tree,
    UncontrolledTreeEnvironment,
} from 'react-complex-tree';
import 'react-complex-tree/lib/style-modern.css';

const sampleItem = {
    root: {
        index: 'root',
        isFolder: true,
        children: ['child1', 'child2'],
        data: 'Root item',
    },
    child1: {
        index: 'child1',
        isFolder: true,
        children: ['child3'],
        data: 'Child item 1',
    },
    child2: {
        index: 'child2',
        children: [],
        data: 'Child item 2',
    },
    child3: {
        index: 'child3',
        children: [],
        data: 'Child item 3',
    },
};


function TreePage() {
    return (
        <UncontrolledTreeEnvironment
            dataProvider={
                new StaticTreeDataProvider(sampleItem, (item, data) => ({
                    ...item,
                    data,
                }))
            }
            getItemTitle={(item) => item.data}
            viewState={{}}
        >
            <Tree
                treeId='tree-1'
                rootItem='root'
                treeLabel='Tree Example'
            />
        </UncontrolledTreeEnvironment>
    );
}
```

`<UncontrolledTreeEnvironment>` 내에서 tree view state를 유지할 수 있다. 환경 내부에서 트리 자체가 DOM 구조에 추가된다. 하나의 환경 내에서 멀티 트리를 구축하는 것도 가능하며, 이 트리들은 공통된 state를 공유하게 된다. (아마 UncontrolledTreeEnvironment에 설정해놓은 옵션들 아닐지 추측)

각 트리들은 아래와 같은 것들을 제공해야한다.

* treeId
  * 각 트리의 고유한 tree id
* rootItem&#x20;
  * 해당 트리에서 사용하게 될 root item. 서로 다른 트리들은 root를 통해 서로 다른 아이템을 보여줄 수 있다.
*   treeLabel (Optional)

    * 화면에 렌더링 될 때 해당 트리에 대한 접근성 레이블로 사용되는 레이블&#x20;
    * (DOM에 표현되는 label 아닐까 추측)

    <figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

각각의 개별 트리는 자신의 state를 제공하지는 않지만, 환경에서 제공하는 state는 공유한다.



트리 환경을 구성하는 방법은 세 가지가 있다.

* UncontrolledTreeEnvironment 와 StaticTreeDataProvider를 사용하는 방법
  * 가장 사용하기 쉬운 방법이다.
* UncontrolledTreeEnvironment 와 사용자 정의 TreeDataProvider를 구현하는 방법
  * Change Event에 직접 대응할 수 있는 기능을 제공
  * lazy item-loading 전략을 정의할 수 있다.
  * 해당 접근 방식이 현실적인 시나리오에서 React Complex Tree를 구축하는 가장 좋은 방법일 가능성이 높다.
* ControlledTreeEnvironment 사용
  * view state를 관리하기 위핸 자체 로직을 정의해야한다.
  * 따라서 가장  많은 노력이 필요하지만 가장 많은 사용자 정의 기능도 제공



## 트리에 data 넣기

```javascript
const items = {
  root: {
    index: 'root',
    canMove: true,
    isFolder: true,
    children: ['child1', 'child2'],
    data: 'Root item',
    canRename: true,
  },
  child1: {
    index: 'child1',
    canMove: true,
    isFolder: false,
    children: [],
    data: 'Child item 1',
    canRename: true,
  },
  child2: {
    index: 'child2',
    canMove: true,
    isFolder: false,
    children: [],
    data: 'Child item 2',
    canRename: true,
  },
};
```

트리에 제공할 데이터의 각 item은 TreeItem 인터페이스를 구현해야한다. 위와 같은 index, canMove, isFolder, children 등으로 정의되어있는 interface 대로 데이터구조를 맞춰야 한다.

익숙한 계층 구조가 아니라 플랫한구조라 직관적이진 않다고 생각한다.

```javascript
const dataProvider = new StaticTreeDataProvider(items)
```

이를 StaticTreeDataProvider를 이용하여 dataProvider를 생성하고 Environment 내의 property로 제공하면 된다.&#x20;

{% code overflow="wrap" %}
```javascript
const dataProvider = new StaticTreeDataProvider(items, (item, newName) => ({ ...item, data: newName }));
```
{% endcode %}

item의 이름 변경 방법을 정의할 수 있는 콜백함수를 넘길 수도 있다. 사용자가 item의 이름을 바꿀 때 호출된다. 이는 react-complex-tree가  어느 부분에 이름이 포함되어 있는지 알지 못하기 때문에 필요한 콜백이다.

콜백  함수의 첫번째 인자는 각 item 항목, 두 번째는 새로운 데이터 이름이다.&#x20;

```jsx
<UncontrolledTreeEnvironment
    dataProvider={dataProvider}
    getItemTitle={(item) => item.data}
    viewState={{}}
>
    <Tree
        treeId='tree-1'
        rootItem='root'
        treeLabel='Tree Example'
    />
</UncontrolledTreeEnvironment>
```

트리 항목의 이름을 나타내기 위해서는 `getItemTitle` property가 필수적으로 필요하다. Tree 인스턴스가 제공하는 항목 중에서 하나를 뽑아서 사용하면 된다.&#x20;





***



## 주요 조사 내용

* 라이브러리 내에서 필요한 기능 몇 가지를 조사한다.

1. icon 지원 여부
2. node 수 많아졌을 때 성능 체크
3. hover시 우측에 더보기 아이콘 사용 가능 여부



### 1. icon 지원 여부

[https://rct.lukasbach.com/docs/guides/rendering](https://rct.lukasbach.com/docs/guides/rendering)

tree 항목의 앞에 아이콘을 넣어야 한다.

renderItem props로 항목을 커스텀하여 렌더링하면 가능

```jsx
 renderItem={({
    title,
    arrow,
    depth,
    context,
    children,
    item,
}) => {
    return (
        <li
            {...context.itemContainerWithChildrenProps}
            className={s.listItem}
        >
            <button
                {...context.itemContainerWithoutChildrenProps}
                {...context.interactiveElementProps}
            >
                {arrow}
                <div className={s.title_wrapper}>
                    {!item.isFolder && (
                        <RequestedIcon className={s.fileIcon} />
                    )}
                    {title}
                </div>
                <AddIcon className={s.moreIcon} />
            </button>
            {children}
        </li>
    );
}}
```



### 2. node 수 많아졌을 때 성능 체크

100 \* 20 \* 10 \* 100 = 2,000,000 (200만)건의 파일을 렌더링할 때 걸린 시간 2770ms. 이는 사실 상 데이터를 만드는데 드는 시간이었고, 50만건의 만들어진 데이터를 불러와 렌더링하는데 걸렸던 시간은 7.5ms.

그 이상의 데이터는 JSON 파일이 어마무시하게 커져 heap memory 초과로 불가능했다.

<figure><img src=".gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





### 3. hover시 우측에 더보기 아이콘 사용 가능 여부

이 역시 renderItem props로 항목을 커스텀하여 렌더링하여 css를 손 보면 가능하다.

개인적으로 아이템 항목을 구성하는 게 복잡하다.

index, isFolder, children 항목, date 기타 등등 커스텀 하기 편하다면 편하고 어렵다고 하면 어려움. 서버와 협의하여  목록 GET response를 어떻게 줄 것인지 정하고 한 번 더 구조를 세팅해야할 거 같다.















