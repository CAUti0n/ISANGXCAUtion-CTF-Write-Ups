# Gotcha

![Untitled](https://user-images.githubusercontent.com/37824335/223039884-f30128f0-0733-4098-a586-069d7a63167f.png)

# #문제 파악

![Untitled 1](https://user-images.githubusercontent.com/37824335/223039918-4f3514b8-18e9-4df7-8196-c2c4f321165b.png)

문제 페이지에 접속하면 위와 같은 화면을 볼 수 있다. 간단한 덧셈 연산식이 주어져 있고 해당 답을 맞추면 되는 것으로 보인다. 

![Untitled 2](https://user-images.githubusercontent.com/37824335/223039961-44d8c82c-a692-4f80-a31f-d139936cd960.png)

그러나 Time이 2초로 제한되어 있고 해당 시간 내에 맞추지 못하면 Time OUT 글자와 함께 문제가 풀리지 않는다. 이를 위해서는 연산식을 2초 안에 입력해야만 한다.

문제는 셀레니움을 사용해도 좋으나, 간단하게 개발자도구의 console 탭을 이용하여 풀어보겠다.

# #Exploit

문제를 푸는 시나리오는 다음과 같다.

1. 문제 페이지에 접속하자마자 개발자 도구의 console 탭 접속한다
2. 주어진 연산식을 계산한다.
3. input 박스에 해당 답을 삽입한다.
4. form 제출을 한다.

2번 과정에서 주어진 연산식을 계산하기 위해서는 연산식 string을 구해야한다. xpath로 element에 접근할 수 있도록 다음 함수를 선언해두자

```bash
function getElementByXpath(path) {
  return document.evaluate(path, document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue;
}
```

![Untitled 3](https://user-images.githubusercontent.com/37824335/223040183-b51f053a-da11-4390-9a47-e880676ef8e7.png)

그 다음 연산식이 태그의 XPath를 복사한다

```bash
/html/body/form/p[1]
```

![Untitled 4](https://user-images.githubusercontent.com/37824335/223040215-96a93b6e-b206-4937-9262-6783b50988f8.png)

console 탭에서 함수 선언 후 XPath를 인자로 넘겨주면 해당 element를 받아올 수 있고, innerHTML 속성으로 string을 가져오자.

![Untitled 5](https://user-images.githubusercontent.com/37824335/223040244-92a68992-c90a-411e-894a-696a2658807c.png)

javascript 함수 중 `eval` 함수를 이용하여 받아온 연산식을 계산하자

![Untitled 6](https://user-images.githubusercontent.com/37824335/223040266-1d71d779-3b49-4b1a-8b35-06afdc48fcc6.png)

이제 input 박스에 계산한 답을 채워넣는다.

![Untitled 7](https://user-images.githubusercontent.com/37824335/223040302-2549cbec-dc34-47fa-99d8-c181fb0b324e.png)

form을 submit하는 코드를 마지막에 실행시키면 된다.

### Exploit code

```jsx
function getElementByXpath(path) {
   return document.evaluate(path, document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue;
};
getElementByXpath('/html/body/form/div[2]/input[1]').value = eval(getElementByXpath("/html/body/form/p[1]").innerHTML.slice(0, -4));
document.getElementsByClassName('container')[0].submit()
```

문제 페이지 새로 고침 후 잽싸게 console에 붙여넣기하면 문제가 풀린다.
