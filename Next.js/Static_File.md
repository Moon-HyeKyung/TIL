# Static File(정적 파일)
- static file(정적 파일)들은 root directory에 있는 'public' 폴더 아래에 위치(ex: image)
- 'public' 내부 파일은 기본 URL('/')에서 시작하는 code로 참조될 수 있다.
> public/me.png

```javascript
import Image from 'next/image'

function Avatar() {
  return <Image src="/me.png" alt="me" width="64" height="64" /> // public/me
}

export default Avatar
```
- '\*.txt', '\*.ico', '\*.html'등 저장 가능
- 'public'은 static assets를 저장하는 유일 dirrectory
- 'public'과 같은 디렉토리, 파일 존재하면 안됨
