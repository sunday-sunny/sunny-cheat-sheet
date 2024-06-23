---
description: 사용자 정의 타입 가드를 사용하여 여러 타입이 섞여 있는 배열에서 map 사용하기
---

# 여러 타입이 섞여 있는 배열에서의 map 함수 사용

## 에러.

API response 값으로 여러 타입이 섞여 있는 반환 값을 받게 되었다.&#x20;

어떤 값들은 단순 boolean으로, 어떤 값들은 선택된 값과 선택할 수 있는 배열을 객체로 받는 형태이다.

```typescript
interface MessageSetting {
    [key: string]: boolean | Setting;
}

interface Setting {
    selectedValue: string;
    settings: string[];
}

// ex) response data
const messageSettings = {
    appPush: true,
    alerts: {
        selectedValue: '진동',
        settings: ['팝업', '진동', '벨'],
    },
    marketingPush: true,
    marketing: {
        selectedValue: '앱 푸시',
        settings: ['문자', '이메일', '앱 푸시'],
    },
};
```



여기서 타입이 `Setting`일 때만 map을 사용하여 radioGroup에 들어갈 데이터를 만드려고 하니 에러가 난다.&#x20;

```typescript
const getRadioData = (settingItem: Setting, radioGroupName: string) => {
  return settingItem
      ? settingItem.settings.map((item) => ({
            name: radioGroupName,
            value: item,
            label: item,
            checked: settingItem.selectedValue === item,
        }))
      : [];
};
```

받는변수 타입은 Setting인데 넘어가는 타입은 `boolean | Setting` 이니 받을 수가 없는 것이다. boolean일 때는 해당 함수의 매개변수로 넘길 수 없기에 사용하는 쪽에서 에러가 난다.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



## 사용자 정의 타입 가드 사용

그래서 **사용자 정의 타입 가드** 함수를 만들어 해당 타입인지 확인하고, 맞다면 map을 수행하도록 변경하였다.&#x20;

primitive 타입은 `typeof` 로 검사가 가능하지만 사용자가 만든 타입은 내부에 특정 프로퍼티가 있는지 등을 이용하여 해당 타입이 맞는지 확인하여야한다.&#x20;

반환 값이 true면 `target is Setting` 이라고 지정해 놓았듯이 target은 Setting 이라고 타입을 돌려준다. 때문에 map 내에서 사용할 때도 해당 타입임이 보장 되었기 때문에 selectedValue 등의 프로퍼티가 자동완성으로 뜬다.

```typescript
const isSettingType = (target: boolean | Setting): target is Setting => {
    return (target as Setting).selectedValue !== undefined;
};
```

```typescript
const getRadioData = (
    settingItem: boolean | Setting,
    radioGroupName: string
) => {
    return settingItem && isSettingType(settingItem)
        ? settingItem.settings.map((item) => ({
              name: radioGroupName,
              value: item,
              label: item,
              checked: settingItem.selectedValue === item,
          }))
        : [];
};
```





#### 참고

* [https://seokzin.tistory.com/entry/TypeScript-%ED%83%80%EC%9E%85-%EA%B0%80%EB%93%9C-Type-Guard](https://seokzin.tistory.com/entry/TypeScript-%ED%83%80%EC%9E%85-%EA%B0%80%EB%93%9C-Type-Guard)



