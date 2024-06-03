# webpack에서 SVG를 리액트 컴포넌트로 사용하기 (feat. @svgr/webpack)

라이브러리를 사용 안 하고 webpack에서 기존에 CRA 프로젝트에서 사용하던 것처럼 svg 파일을 리액트 컴포넌트로 불러오려고 했는데 잘 되지가 않는다.

```javascript
import { ReactComponent as AsteriskIcon } from '@/assets/icons/asterisk.svg';
```



당연히 webpack은 설정해준게 없으니 module not found를 내뱉는다. 이래저래 해봤지만 잘 되지 않아 svgr을 설치하였는데 원하는 형태로 불러오기에 성공하여 여러모로 라이브러리 쓰는게 좋긴 좋구나 느낀다.&#x20;

docs도 아주 잘 되어있어 하라는대로 하면 아주 잘 된다.

{% hint style="info" %}
설정 환경 Webpack5 + React + Typescript
{% endhint %}



## svgr 라이브러리로 svg를 리액트 컴포넌트로 불러오기

### @svgr/webpack 설치

[https://react-svgr.com/docs/webpack/](https://react-svgr.com/docs/webpack/)

```powershell
yarn add --dev @svgr/webpack
```



### webpack.config 파일 설정

````typescript
module: {
        rules: [
            {
                test: /\.svg$/,
                use: {
                    loader: '@svgr/webpack',
                    options: {
                        exportType: 'named',
                    },
                },
            },
        ],
    },
```
````

svgr은 기본적으로 svg를 리액트 컴포넌트로 불러오기 때문에 `import { ReactComponent as ~ }` 구문으로 svg 파일을 불러올 필요 없다. **오히려 넣으면 에러가 난다.**

추가 로더를 사용할 경우에만 `import { ReactComponent as ~ }` 구문을 쓸 수 있는데, 그렇지 않고도 위 형태로 가져오기 위해 `exportType` 옵션을 추가로 설정해준다.

만약 해당 옵션을 넣지 않는다면 svg를다음과 같이 불러와야한다. 해당 svg도 리액트 컴포넌트로 불러온 거긴 하지만 컴포넌트에 className 등을 부여할 수 없다.

```typescript
import AsteriskIcon from '@/assets/icons/asterisk.svg';
```



### svg 타입 지정

```typescript
// global.d.ts
declare module '*.svg' {
    import React = require('react');

    export const ReactComponent: React.FC<React.SVGProps<SVGSVGElement>>;
    const src: string;
    export default src;
}
```

타입 스크립트를 사용하니까 svg 파일 타입을 선언해준다. `global.d.ts` , `common.d.ts` 등 본인이 관리하고 있는 `d.ts` 파일이면 된다.

또한 많은 블로그들에서 해당 타입을 지정하고 `tsconfig.json` 파일의 include에 해당 파일을 추가하라고 하는데, 설정한 `d.ts` 파일이 include로 지정한 src 폴더 내부에 존재하면 굳이 추가할 필요 없다.

```json
{
    "compilerOptions": {
        ...
    },
    "include": ["src"], <-- 이 부분에.
}
```

나의 경우 `src/global.d.ts` 에 위치하므로 굳이 추가하지 않았다.



### svg 파일  width, height 수정

{% code overflow="wrap" %}
```svg
<svg width="100%" height="100%" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M10.2258 23H13.7742V15.0105L21.2258 19.1789L23 16.2263L15.5484 12L23 7.77368L21.2258 4.82105L13.7742 8.98947V1H10.2258V8.98947L2.77419 4.82105L1 7.77368L8.45161 12L1 16.2263L2.77419 19.1789L10.2258 15.0105V23Z" fill="currentColor"/>
</svg>
```
{% endcode %}

위 설정대로 해도 잘 불러와지지만 width나 height를 수정하려고 하면 이상하게 줄어드는 것을 볼 수 있다.

초반에 설정된 svg width, height 크기대로 사용하려고 하면 상관 없지만, css의 개입이 들어갈 여지가 있는 경우 \<svg/> 태그내에 지정된 `width`와 `height`를 100%로 수정해준다.

color도 수정할 여지가 있다면 `fill="currentColor"`로 수정해준다.

100%로 지정했으니 처음에 불러오면 크기가 엄청 크게 나오는데 그건 css 값을 주어 조절해주자.



### svg import

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```typescript
import { ReactComponent as AsteriskIcon } from '@/assets/icons/asterisk.svg';

function Label({
    name,
    isRequired = false,
    styleProps = undefined,
}: LabelProps) {

    return (
        <div className={`${s.container}`}>
            <label htmlFor={name} style={{ ...styleProps }}>
                {name}
                {isRequired && (
                    <div className={s.labelIconBox}>
                        <AsteriskIcon className={s.asterisk} />
                    </div>
                )}
            </label>
        </div>
    );
}
```

요로콤 이쁘게 asterisk 단 Label 컴포넌트 완성!



## svg 리액트 컴포넌트와 일반 svg 파일 불러오기 같이 쓰기

svg 파일을 굳이 리액트 컴포넌트가 아닌 svg 그 자체로 \<img/> 태그와 같이 사용하고 싶을 수도 있을터! 이 역시 공식 문서에 잘 설명이 되어있다.

{% tabs %}
{% tab title="webpack.config" %}
```
module: {
        rules: [
            {
                test: /\.svg$/i,
                type: 'asset',
                resourceQuery: /url/,
            },
            {
                test: /\.svg$/,
                resourceQuery: { not: [/url/] },
                use: {
                    loader: '@svgr/webpack',
                    options: {
                        exportType: 'named',
                    },
                },
            },
        ],
    },
```
{% endtab %}

{% tab title="global.d.ts" %}
```typescript
declare module '*.svg?url' {
    const content: any;
    export default content;
}

declare module '*.svg' {
    import React = require('react');

    export const ReactComponent: React.FC<React.SVGProps<SVGSVGElement>>;
    const src: string;
    export default src;
}
```
{% endtab %}

{% tab title="example" %}
{% code overflow="wrap" %}
```typescript
import AsteriskIcon1 from '@/assets/icons/asterisk.svg?url';
import { ReactComponent as AsteriskIcon2 } from '@/assets/icons/asterisk.svg';

<img src={AsteriskIcon1} />
<AsteriskIcon2 className={s.icon} />
```
{% endcode %}
{% endtab %}
{% endtabs %}

wepback에 추가 설정을 주어 `resourceQuery` 옵션으로  resoure 경로에 url 문자가 포함 되어있는지 아닌지에 따라 구분한다. 일반 svg 파일 사용을 위해 `global.d.ts`에도  `.svg?url` 에  대한 추가 정의를 해주면 완료!



공식 문서 감사해요! 💗



#### 참고

* [https://react-svgr.com/docs/webpack/](https://react-svgr.com/docs/webpack/)
* [https://stackoverflow.com/questions/66451188/how-can-i-use-classname-on-svg-imported-as-a-component-in-typescript-react-fc](https://stackoverflow.com/questions/66451188/how-can-i-use-classname-on-svg-imported-as-a-component-in-typescript-react-fc)
* [https://velog.io/@song961003/webpack5-tw.macro%EC%97%90%EC%84%9C-svg-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@song961003/webpack5-tw.macro%EC%97%90%EC%84%9C-svg-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)





