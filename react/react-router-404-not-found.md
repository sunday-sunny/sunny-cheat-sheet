# React Router 중첩 라우팅 404 Not Found



Webpack 환경에서 React Router로  중첩 라우팅을 구성하던 중 404 Not Found 에러가 났다.



### 환경

* Webpack5 + React + Typescript
* React Router

```json
"react-dom": "^18.2.0",
"react-router-dom": "^6.22.3",
```



구성하려던 내용은 Settings 페이지가 있고, Settings 하위에 여러가지설정 관련 컴포넌트를 따로 나누려고 했던 것.

```typescript
function Settings() {
    return (
        <div>
            <SettingsLNB />
            <Card
                content={<Outlet />}
            />
        </div>
    );
}

...

export default function CommonRouter() {
    return (
        <BrowserRouter>
            <Routes>
                <Route element={<MainLayout />}>
                    ...
                    {/* Settings  */}
                    <Route element={<Settings />}>
                        <Route
                            path='/settings/user'
                            element={<UserSettings />}
                        />
                        <Route
                            path='/settings/options'
                            element={<OptionSettings />}
                        />
                    </Route>
                </Route>
            </Routes>
        </BrowserRouter>
    );
}
```



### 에러. react router nested routing 404 Not Found

하지만 바로 뜨고 마는 404 Not Found.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

처음에는 내가 라우팅 설정을 잘못했나  `<Outlet/>` 위치가 잘못 되었나 이것 저것 찾아보고 수정해봤는데 원인은 webpack 환경 설정 쪽.



### 해결. webpack publicPath 설정

webpack 설정 파일의 output의 `publicPath`를 `'/'` 으로 설정해준다.

```typescript
output: {
        publicPath: '/',
},
```



자세한 원인은 출처들 참고!



#### 출처

* [https://stackoverflow.com/questions/63592478/react-router-v4-v5-v6-nested-routing-404-neterr-aborted-404](https://stackoverflow.com/questions/63592478/react-router-v4-v5-v6-nested-routing-404-neterr-aborted-404)
* [https://min-kyung.tistory.com/m/152](https://min-kyung.tistory.com/m/152)



