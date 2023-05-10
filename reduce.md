### 2023-05-10

## Reduce 추가 예시

callback function은 다음과 같이 4가지의 인수를 가질 수 있다.
- accumulator - accumulator는 callback함수의 반환값을 누적.
- current - 배열의 현재 요소
- index(Optional) - 배열의 현재 요소의 인덱스
- array(Optional) - 호출한 배열

ex:) 1. 숫자 배열 합산
```javascript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce(
    (accumulator, current) => accumulator + current, 0
    );
console.log(sum); // Output: 15
```
배열의 모든 값 (1부터 5까지) 더하는 예제


예 2: 배열 연결 하기
```javascript
Copy code
const arrays = [[1, 2], [3, 4], [5, 6]];
const flattened = arrays.reduce((accumulator, current) => accumulator.concat(current), []);
console.log(flattened); // Output: [1, 2, 3, 4, 5, 6]
```
초깃 값 [](빈 배열)을 선언하고 concat 메소드를 통해 값을 연결하는 예제

예 3: 문자열에서 단어의 발생 횟수 세기
```javascript
const sentence = "the quick brown fox jumps over the lazy dog";
const wordCounts = sentence.split(" ").reduce((accumulator, current) => {
  accumulator[current] = (accumulator[current] || 0) + 1;
  return accumulator;
}, {});
console.log(wordCounts); 
// Output: { the: 2, quick: 1, brown: 1, fox: 1, jumps: 1, over: 1, lazy: 1, dog: 1 }
```
split 메소드를 사용해 단어를 나눈다.

['the', 'quick', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']

초깃 값 {}(객체)를 선언한다.
current를 통해 각 단어가 들어오게 되고

각 객체 마다 해당 단어의 key로 저장한다. 

- undefined(값이 없을 경우) ||(or) 문에 의해 0+1로 카운트 하여 1개가 추가된다.
- 값이 존재할 경우 해당 값 + 1을 저장한다.