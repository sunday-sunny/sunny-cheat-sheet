# JSX에서 \<br/> 을 이용한 개행 처리

항상 까먹는 JSX에서 개행 처리.

일반 자바스크립트 구문에서라면 문장 중간에 `\n` 을 사용하거나, 템플릿 리터럴을 사용하라고 검색하면 나오지만, JSX 내에서는 통하지가 않는다.



<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

```typescript
const dialogContent = (
    <div className={s.dialogContent}>
        <p className={s.description}>
            안녕하세요, 첫번째 문장입니다. \n두번째 문장입니다.
        </p>
    </div>
);
```





### \<br/> 태그를 이용한 개행

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

줄바꿈을 해주는 `<br/>` 태그를 중간에 넣어 개행해준다.

```typescript
const dialogContent = (
    <div className={s.dialogContent}>
        <p className={s.description}>
            안녕하세요, 첫번째 문장입니다. <br />
            두번째 문장입니다.
        </p>
    </div>
);
```



### white-space: pre-wrap 설정  (\<br/> 태그가 안 될 때)

나는 처음에 `<br/>` 태그만 했을 때는 개행이 안됐었는데,  `<br/>` 태그를  준 상태에서 CSS에 추가로 `white-space: pre-wrap`을 주니 그제서야 적용이 됐었다.

근데 또 이제는 또 빼도 잘 개행이 된다.

```scss
.dialogContent {
    .description {
        white-space: pre-wrap;
    }
}
```





#### 참고

* [https://velog.io/@euji42/React-n%EC%9D%B4-%EC%A4%84%EB%B0%94%EA%BF%88%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%84-%EB%95%8C](https://velog.io/@euji42/React-n%EC%9D%B4-%EC%A4%84%EB%B0%94%EA%BF%88%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%84-%EB%95%8C)



