---
layout  : post
title   : "Prettier를 사용하여 코드를 Airbnb 코딩 규칙을 적용하게 설정하는 방법"
date    : 2021-05-13 22:44:38 +0900
category: JavaScript
---

JavaScript에는 Airbnb, Google, jQuery, JavaScript Standard 등등 여러 가지 코딩 규칙(Coding Convention)이 있습니다.  
오늘은 이 많은 코딩 규칙 중에 Prettier를 사용하여 코드를 Airbnb 코딩 규칙([https://github.com/airbnb/javascript](https://github.com/airbnb/javascript))으로 적용하는 방법을 소개하도록 하겠습니다.

## 설정 방법

1. Visual Studio Code를 실행하고, '확장 기능'에 들어가서 'Prettier'를 설치합니다.  
![Install Prettier]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_1.png)

2. <kbd>Ctrl</kbd> + <kbd>,</kbd>를 눌러 설정에 들어갑니다.  
'Default Formatter'를 검색하고, '없음'에서 'Prettier - Code formatter'로 변경합니다.  
![Default Formatter Setting]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_2.png)

3. 프로젝트 최상단에 `.prettierrc.yml` 파일을 생성하고 아래와 같이 입력합니다.  
각 설정에 대한 설명은 아래에서 하도록 하겠습니다.  
```yml
arrowParens: "always"
bracketSpacing: true
jsxBracketSameLine: false
jsxSingleQuote: false
printWidth: 100
proseWrap: "always"
quoteProps: "as-needed"
semi: true
singleQuote: true
tabWidth: 2
trailingComma: "es5"
useTabs: false
```

4. Airbnb 코딩 규칙을 적용할 코드를 열고 <kbd>F1</kbd>를 누른 뒤, 'Format Document'를 입력하고 실행합니다.  
![Format Document]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_3.png)

5. 예시로 Airbnb 코딩 규칙에 어긋나는 코드를 아래와 같이 작성해보았습니다.  
![Bad Code]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_4.png)  
여기서 'Format Document'를 하면 아래와 같이 Airbnb 코딩 규칙에 맞게 적용되는 것을 확인 할 수 있습니다.  
![Good Code]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_5.png)  

## 설정에 대한 설명

- `"quoteProps": "as-needed"` :  
![quoteProps]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_6.png)

- `"singleQuote": true` :  
![singleQuote]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_7.png)

- `"arrowParens": "always"` :  
![arrowParens]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_8.png)

- `"tabWidth": 2`, `"useTabs": false` :  
![tabWidth and useTabs]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_9.png)

- `"bracketSpacing": true` :  
![bracketSpacing]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_10.png)

- `"trailingComma": "es5"` :  
![trailingComma]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_11.png)

- `"semi": true` :  
![semi]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_12.png)

- `"jsxSingleQuote": false` :  
![jsxSingleQuote]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_13.png)

- `"jsxBracketSameLine": false` :  
![jsxBracketSameLine]({{ site.url }}/assets/image/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting/2021-05-13-Prettier-Airbnb-Javascript-Style-Setting_14.png)
