# map 함수에서 현재 배열을 인자로 받기

### Array.prototype.map()

* [https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global\_Objects/Array/map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global\_Objects/Array/map)

```javascript
 arr.map(callback(currentValue[, index[, array]])[, thisArg])
```



map 함수는 리액트에서 컴포넌트를 렌더링하느라 가장 많이 사용하는 함수 중 하나일 것이다. 보통 사용하면 콜백 함수의 인자로 currentValue, index 정도만 추가해서 사용할 것이다.

```typescript
<ul>
    {category.lists.map((item, index) => (
        <LNBMenuCollapse
            key={item.name}
            type='text'
            item={item}
        />
    ))}
</ul>
```



### map 함수의 세  번째 인자 array 사용하기

하지만 현재 호출된 배열 자체가 필요하다면 세 번째 인자로 호출한 배열을 받아볼 수 있다.&#x20;

다음은 filter 함수와 map 함수를 체이닝 하여 사용했을 때 필터링된 배열의 length가 필요하여 세 번째 인자로 받아 사용한 경우이다.

```typescript
{fileTypeList
    .filter((file) => file.category === 'IMAGE')
    .map((file, index, filteredArr) => (
        <tr>
            {index === 0 && (
                <td rowSpan={filteredArr.length}>
                    <Label
                        name={`이미지 파일 목록 (${filteredArr.length})`}
                    />
                </td>
            )}
            <td>{file.name}</td>
            <td>{file.count} 개</td>
            <td></td>
        </tr>
    ))}

```



호출한 배열을 받아 배열의 length로 확장자 목록 표시와 table 태그 row 병합에 사용하였다.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>







