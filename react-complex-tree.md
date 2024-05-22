# react-complex-tree 조사



## 주요 조사 내용

1. icon 지원 여부
2. node 수 많아졌을 때 성능 체크
3. hover시 우측에 더보기 아이콘 사용 가능 여부



## react-complex-tree

[https://rct.lukasbach.com](https://rct.lukasbach.com/docs/guides/styling)







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

100 \* 20 \* 10 \* 100 = 2,000,000 건의 파일을 렌더링할 때 걸린 시간 2.7초&#x20;

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>





### 3. hover시 우측에 더보기 아이콘 사용 가능 여부

이 역시 renderItem props로 항목을 커스텀하여 렌더링하여 css를 손 보면 가능하다.

개인적으로 아이템 항목을 구성하는 게 복잡하다.

index, isFolder, children 항목, date 기타 등등 커스텀 하기 편하다면 편하고 어렵다고 하면 어려움. 서버와 협의하여  목록 GET response를 어떻게 줄 것인지 정하고 한 번 더 구조를 세팅해야할 거 같다.















