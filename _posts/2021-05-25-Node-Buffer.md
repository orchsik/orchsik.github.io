---
layout: single
title: "Node의 Buffer를 알아보자."

categories: node
tags: Buffer
typora-copy-images-to: ../images/2021-05-25
---

## 1. Buffer가 왜 필요해?
- 브라우저의 Javascript 환경이면 문자열만 가지고 놀아도 충분하지만...

- 서버에서는 TCP Stream을 다루고, Filesystem을 이용해 file을 읽고 쓰는 작업이 필요하다.

- 이런 작업에는 **Raw Binary Data**를 가지고 놀아야 한다.

- 이 때, **Buffer**가 필요하다.

- Raw Binary Data를 인코딩해서 Binary String을 가지고 놀면 안 되나요?
  - 느림. 구림. 오류 발생 확률 높음.
  
  


## 2. Buffer가 뭐에요?
- Raw Binary Data를 다루라고 Node에서 만들어줬다.
- 정수 배열과 비슷한데 **다르다**.
  - Raw Binary Data에 특화된 메소드가 있다.
  - Buffer의 크기를 수정할 수 없다.
  - 원소는 각각 바이트를 나타내며, 16진법으로 표시된다. 
  - 0에서 255까지의 값으로 제한된다. (8bit = 1byte)
- 버퍼는 일반적으로 스트림에서 오는 이진 데이터의 컨텍스트에서 볼 수 있다.
  - 예를 들면 fs.createReadStream
- Buffer는 V8엔진 바깥에 memory에 할당된다.



## 3. Buffer 어떻게 사용함?
  ### 1. CREATE
  ```javascript
  var buffer;

  // 1byte 아이템 * 8개
  buffer = Buffer.alloc(8);
  console.log(buffer);
  // <Buffer 00 00 00 00 00 00 00 00>

  // 배열의 인자는 0~255 사이의 정수. 256 표현 못함.
  buffer = Buffer.from([0, 255, 256]);
  console.log(buffer);
  // <Buffer 00 ff 00>

  // 문자열도 가능. 두번째 인자는 인코딩 타입
  buffer = Buffer.from('I;am a string!', 'utf-8');
  console.log(buffer);
  // <Buffer 49 3b 61 6d 20 61 20 73 74 72 69 6e 67 21
  ```



  ### 2. WRITE

  ```javascript
  var buffer;
  var result;
  buffer = Buffer.alloc(16);

  result = buffer.write('Hello', 'utf8');
  console.log(result); // 5 (write에 사용한 byte 개수)

  // 두번째 인자를 주면 해당 인덱스부터 write.
  buffer.write(' world!', 5, 'utf8');
  console.log(buffer.toString('utf8')); // Hello world!

  // write범위: 시작인덱스, 끝인덱스
  console.log(buffer.toString('utf8', 0, 5)); // Hello

  // 배열처럼 요로코롬 줄 수도 있음.
  buffer[12] = buffer[11];
  buffer[13] = '!'.charCodeAt();
  buffer[14] = buffer[13];
  buffer[15] = 33;
  console.log(buffer.toString('utf8')); // Hello world!!!!!
  ```

  ### 3. MORE
  ```javascript
  var result;
  var buffer = Buffer.alloc(16);
  var santa = '🎅';

  // 너 Buffer니?
  console.log(Buffer.isBuffer(santa)); // false

  // write할 때, 필요한 Buffer 크기
  console.log(Buffer.byteLength(santa)); // 4

  // 버퍼 전체 크기
  console.log(buffer.length); // 16

  /**
  * buffer.copy(target, targetStart=0, sourceStart=0, sourceEnd=buffer.length)
  */
  var myHome = Buffer.alloc(24);
  result = myHome.write('Merry Christmas!', 'utf8');
  console.log(result); // 16

  var santa_buffer = Buffer.from(santa, 'utf-8');
  result = santa_buffer.copy(myHome, 16);
  console.log(result); // 4

  console.log(myHome.toString('utf8', 0, 16 + 4));
  // Merry Christmas!🎅

  /**
  buffer.slice(start, end=buffer.length)
  Array.prototype.slice와 비슷하다.
  단! slice의 return값이 새로운 객체가 아니라 동일 객체다.
  수정하면 원본 Buffer도 수정된다.
  */
  var father = myHome.slice(16, 20);
  console.log(father.toString()); // 🎅

  father.write('👨');
  console.log(father.toString()); // 👨
  console.log(myHome.toString()); // Merry Christmas!👨
  ```
