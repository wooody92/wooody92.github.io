---
title:  "알고리즘 저장소"
excerpt: "알고리즘 학습 및 풀이 저장소"
header:

categories:
  - Algorithm
tags:
  - algorithm
  - 알고리즘
  - backjoon
  - programmers
last_modified_at: 2020-09-11 20:00:00
---

<br>

# algorithm

[알고리즘 풀이 저장소](https://github.com/wooody92/algorithm)

-----

## Backjoon

1. [13458 - 시험감독](https://www.acmicpc.net/problem/13458)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/test_supervisor_13458/Main.java)
   - 주어지는 결과값이 int의 범위를 초과 할 수 있다.
   - 자료형 `int` -> `long`으로 변경 후 해결

2. [2309 - 일곱 난쟁이](https://www.acmicpc.net/problem/2309)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/seven_dwarf_2309/Main.java)
   - 난쟁이 7명 키의 합이 100이하가 아니고 100이다.

3. [1476 - 날짜 계산](https://www.acmicpc.net/problem/1476)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/date_calculate_1476/Main.java)
   - 최소공배수

4. [9095 - 1,2,3 더하기](https://www.acmicpc.net/problem/9095)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/add_one_two_three_9095/Main.java)
   - `점화식`
   - `DP`
   - `Bottom-up` & `Top-down`
   - 배열 동적으로 초기화 시 런타임 에러 발생

5. [14501 - 퇴사](https://www.acmicpc.net/problem/14501)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/resignation_14501/Main.java)
   - `DP`
   - 왜 틀린지 모르겠음. 다음에 다시풀어보기.

6. [14889 - 스타트와 링크](https://www.acmicpc.net/problem/14889)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/start_link_14889/Main.java)

   - 조합을 이용하여 푸는 문제이다. 이를 이용하려면 재귀와 백트래킹에 대해 학습해야 한다.

   - 풀이 방식이 정리가 안돼서 아래 블로그를 참고하여 해결하였다.

     https://bcp0109.tistory.com/30

7. [14888 - 연산자 끼워넣기](https://www.acmicpc.net/problem/14888)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/add_operator_14888/Main.java)

   - 연산자의 경우의 수를 순열(`permutation`)로 모두 구한 후 계산하여 풀이하였다. 

   - 테스트에는 통과하였지만 나의 풀이 경우에는 같은 연산자가 여러번 반복되면 그만큼 중복되어 계산하여 시간복잡도가 올라간다.

   - 다른 사람의 풀이를 보니 다음과 같은 방식으로 시간복잡도를 줄였다.

     ```java
     // 1. 연산자를 갯수를 int로 받아서 그대로 사용한다.
     st = new StringTokenizer(br.readLine());
     for (int i = 0; i < 4; i++) {
       operator[i] = Integer.parseInt(st.nextToken());
     }
     
     // 2. 순열이 아닌 DFS 백트래킹을 이용하여 해결하였다.
     https://www.acmicpc.net/source/21639755
     https://zoonvivor.tistory.com/98
     https://brenden.tistory.com/18
     ```

   - 완전탐색(`DFS`, `BFS`, `Backtracking`, `permutation` 등)과 그 방법들의 관계에 대해서 명확하게 이해가 필요하다.

8. [14502 - 연구소](https://www.acmicpc.net/problem/14502)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/laboratory_14502/Main.java)
   - 완전탐색 `DFS` 문제임을 파악하지 못했다. 백트래킹과 좌표계 탐색을 학습하기 좋은 문제여서 학습 후 정리했다.
   - [https://wooody92.github.io/algorithm/Algorithm-backtracking/](https://wooody92.github.io/algorithm/Algorithm-backtracking/)

9. [14503 - 로봇 청소기](https://www.acmicpc.net/problem/14503)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/backjoon/robotic_vacuum_14503/Main.java)
   - 방향을 바꾸면서 완전 탐색하는 방식까지 구현했는데, 후진 및 종료조건의 경우를 구현하지 못해 구글을 참고하여 풀었다.
   - 완전탐색이 아닌 `DFS`를 학습하기 좋은 문제라고 생각하여 블로그에 고민했던 점들을 정리하였다.
   - [https://wooody92.github.io/algorithm/Algorithm-DFS/](https://wooody92.github.io/algorithm/Algorithm-DFS/)

<br><br>

## Programmers

#### LEVEL 1

1. [42576 - 완주하지 못한 선수](https://programmers.co.kr/learn/courses/30/lessons/42576)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/unfinished_player_42576/Solution.java)

2. [42840 - 모의고사](https://programmers.co.kr/learn/courses/30/lessons/42840)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/mock_exam_42840/Solution.java)
   - 확장성 없이 하드코딩 한 것 같다. 

3. [42862 - 체육복](https://programmers.co.kr/learn/courses/30/lessons/42862)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/sweatsuit_42862/Solution.java)
   - Greedy
   - 그리디 사용은 잘 모르겠고, 전체 순회하여 조건으로 필터링하여 풀었다.

4. [42748 - k번째 수](https://programmers.co.kr/learn/courses/30/lessons/42748)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/kth_number_42748/Solution.java)
   - `Arrays.copyOfRange`, `Arrays.sort` 등 잘 사용하지 않았던 배열 관련 메서드들을 알게 되었다.

5. [12901 - 2016](https://programmers.co.kr/learn/courses/30/lessons/12901)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/date_of_2016_12901/Solution.java)
   - 나머지를 이용한 간단한 문제

6. [17681 - 비밀지도 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17681)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/secret_map_kakao_17681/Solution.java)
   - 주어진 길이 `n` 보다 2진수 자릿수가 적을 경우 0은 생략되기 때문에 가장 앞자리에 공백추가되지 않는 경우를 고려해야한다.
   - `Integer.toBinaryString` 로 2진수로 변환 할 수 있다.
   - `String.format` 로 자릿수와 타입 고정 할 수 있다.

7. [12906 - 같은 숫자는 싫어](https://programmers.co.kr/learn/courses/30/lessons/12906)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/number_compression_12906/Solution.java)

8. [12903 - 가운데 글자 가져오기](https://programmers.co.kr/learn/courses/30/lessons/12903)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/get_center_string_12903/Solution.java)
   - `String.subString` 내장함수로 간단하게 풀이 가능하다.

9. [12910 - 나누어 떨어지는 숫자 배열](https://programmers.co.kr/learn/courses/30/lessons/12910)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/divisor_array_12910/Solution.java)
   - `for loop` 사용 : 코드가 길어지나, 속도가 빠르다.
   - `Stream` 사용 : 직관적이고 코드가 간결하나, 속도가 느리다.
   - 알고리즘 테스트에서는 어떤게 유리할까? `for loop`

10. [12912 - 두 정수 사이의 합](https://programmers.co.kr/learn/courses/30/lessons/12912)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/sum_of_integer_12912/Solution.java)
    - 일정 구간의 정수를 순차적으로 더하는 경우 가우스 공식을 사용하여 시간을 단축 시키도록 한다.
    - `for loop` : `10ms` -> `Gauss` : `0.5ms`
    - 간단한 알고리즘에도 수학공식을 적용하면 효율을 향상 시킬 수 있다는 것을 알게 되었다.

11. [12915 - 문자열 내 마음대로 정렬하기](https://programmers.co.kr/learn/courses/30/lessons/12915)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/custom_strings_sorting_12915/Solution.java)
    - `Comparator Interface` 사용이 미숙하여 구글링 했다. 다시 풀어보기.
    - `Arrays.sort()` 에 람다식으로 조건을 넣을 수 있다.
    - `Comparator & Arrays.sort` 의 원리를 알아야 아래 내장 함수를 활용 할 수 있겠다.
    - `Comparator - compareTo`, `Charactor.compare`

12. [12916 - 문자열 내 p와 y의 개수](https://programmers.co.kr/learn/courses/30/lessons/12916)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/number_of_py_12916/Solution.java)

13. [12917 - 문자열 내림차순으로 배치하기](https://programmers.co.kr/learn/courses/30/lessons/12917)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/descending_sort_12917/Solution.java)
    - `String` 문자열 로직 처리 시 (`Sorting` 등) `StringBuilder`를 사용하면 시간을 많이 단축 시킬 수 있다.

14. [12918 - 문자열 다루기 기본](https://programmers.co.kr/learn/courses/30/lessons/12918)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/number_filtering_12918/Solution.java)
    - `ASCII code`를 이용하여 풀었다.
    - `char`는 `int` 형 변환 하면 `ascii code`를 확인 할 수 있다.
    - 정규식으로 푸는 방법도 있다.

15. [12919 - 서울에서 김서방 찾기](https://programmers.co.kr/learn/courses/30/lessons/12919)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/finding_kim_12919/Solution.java)

16. [12921 - 소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/12921)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/number_of_prime_12921/Solution.java)
    - 주어진 수 `N` 보다 작은 소수로만 나누는 조건으로 했을 경우 효율성 검사에서 실패했다.
    - 구글링으로 아래 수학적 조건을 이용하여 효율성 검사를 통과하였다. 수학적 지식이 없었으면 효율성에서 계속 떨어졌을 것 같다.
    - 주어진 수 `N`이 `2`부터 `N 제곱근` 범위에서 나누었을 때 `0`이 되는 수가 있다면, 그 수는 소수가 아니다. 즉, 이 범위내 나누어지는 수가 없다면 그 수는 소수이다.

17. [12922 - 수박수박수박수박수박수?](https://programmers.co.kr/learn/courses/30/lessons/12922)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/watermelon_12922/Solution.java)
    - `String vs. StringBuffer vs. StringBuilder` 차이점 복습
    - `String` :`Immutable` 불변성, 힙 영역에 생성되고 (GB가 동작하는 영역),한번 생성되면 할당 된 메모리 공간이 변하지않고, 추가한 만큼 객체가 생성된다.
    - `StringBuffer` : `Mutable`, 멀티 스레드 환경에서 동기화를 지원한다. 스레드에 안전한 프로그램이 필요할 때 사용한다.
    - `StringBuilder` : `Mutable`, 단일 스레드 환경에서 사용하고, 동기화를 보장하지 않는다. 연산이 많을 경우 가장 성능이 좋다.
    - 알고리즘에서 `String` 문자열 로직 처리 시 `StringBuilder`를 사용하도록 하자. 

18. [12925 - 문자열을 정수로 바꾸기](https://programmers.co.kr/learn/courses/30/lessons/12925)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/string_to_integer_12925/Solution.java)

19. [12928 - 약수의 합](https://programmers.co.kr/learn/courses/30/lessons/12928)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/sum_of_divisor_12928/Solution.java)
    - `n`의 약수는 `n/2`보다 클 수 없으므로 루프 범위를 줄일 수 있다.

20. [12926 - 시저 암호](https://programmers.co.kr/learn/courses/30/lessons/12926)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/caesar_cipher_12926/Solution.java)
    - `char`는 자동으로 `ascii` 치환되기 때문에 굳이 `ascii` 테이블 검색해서 숫자로 작성할 필요가 없다.
    - 대문자는 대문자로, 소문자는 소문자로만 돌아가기 때문에 분기처리 하였다.

21. [64061 - 크레인 인형뽑기 게임 : kakao 2019](https://programmers.co.kr/learn/courses/30/lessons/64061)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/puppeteer_64061/Solution.java)
    - `List`를 이용하여 풀었는데, `Stack`을 이용하여 풀이하면 더 좋을 것 같아 동일 한 코드를 `Stack` 구조로 변경하였다. 

22. [12930 - 이상한 문자 만들기](https://programmers.co.kr/learn/courses/30/lessons/12930)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/weird_string_12930/Solution.java)
    - 문제에 주어진 입출력 에제가 하나밖에 없고 고려해야 할 제한조건이 정확하게 제시되어 있지 않아 시간이 조금 걸렸다.
    - `Character.toUpperCase()`를 이용하여 대소문자로 바로 변환 가능하다.

23. [12931 - 자릿수 더하기](https://programmers.co.kr/learn/courses/30/lessons/12931)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/sum_of_digit_12931/Solution.java)
    - 가독성은 형변환 방식에 비해 떨어지지만, 나누기 산수계산으로 처리하면 효율은 조금 더 좋다.

24. [12932 - 자연수 뒤집어 배열로 만들기](https://programmers.co.kr/learn/courses/30/lessons/12932)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/reverse_number_12932/Solution.java)
    - `StringBuilder.reverse`로 풀 수 있었지만, 수학적으로 풀어보았다.

25. [12933 - 정수 내림차순으로 배치하기](https://programmers.co.kr/learn/courses/30/lessons/12933)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/descending_integer/Solution.java)

26. [12934 - 정수 제곱근 판별](https://programmers.co.kr/learn/courses/30/lessons/12934)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/squar_discriminator_12934/Solution.java)
    - `double`에서 ` int`로 타입 캐스팅 시 데이터 손실 주의하자.

27. [12935 - 제일 작은 수 제거하기](https://programmers.co.kr/learn/courses/30/lessons/12935)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/remove_minimum_12935/Solution.java)
    - `Stream` 사용 할 수 있으나 느리므로 `for loop`로 해결했다.

28. [12937 - 짝수와 홀수](https://programmers.co.kr/learn/courses/30/lessons/12937)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/even_odd_number_12937/Solution.java)

29. [67256 - 키패드 누르기 : kakao 2020](https://programmers.co.kr/learn/courses/30/lessons/67256)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/touch_keypad_67256/Solution.java)
    - 로컬 테스트 시 log 결과값은 잘 나오는데 프로그래머스 테스트 시 결과값이 다르게 나와서 실패한다. 이유를 잘 모르겠다.
    - 현재 좌, 우 위치에서 목표 번호까지의 거리를 구하는 로직은 구글링으로 블로그를 참고했다. [링크](https://0pencoding.github.io/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/2020/07/03/%ED%82%A4%ED%8C%A8%EB%93%9C%EB%88%84%EB%A5%B4%EA%B8%B0_level1.html)

30. [12940 - 최대공약수와 최소공배수](https://programmers.co.kr/learn/courses/30/lessons/12940)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/gcd_lcm_12940/Solution.java)

    - 유클리드 호제법을 이용하여 재귀로 풀 수 있다.

      ```
      n을 m으로 나눈 나머지를 n이라 하면 (단 n>m), n과 m의 최대공약수는 m과 r의 최대공약수와 같다.
      ```

    - 최대공약수는 두 수 작은 수나 두 수의 차보다 작다.

    - 최대공약수 (`gcd`) : `유클리드 호제법`

    - 최소공배수 (`lcm`) : `(a * b) / gcd(a, b)`

31. [12943 - 콜라츠 추측](https://programmers.co.kr/learn/courses/30/lessons/12943)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/collatz_12943/Solution.java)
    - 재귀를 이용하여 해결했다.

32. [12944 - 평균 구하기](https://programmers.co.kr/learn/courses/30/lessons/12944)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/mean_value_12944/Solution.java)

33. [12947 - 하샤드 수](https://programmers.co.kr/learn/courses/30/lessons/12947)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/harshad_12947/Solution.java)

34. [12948 - 핸드폰 번호 가리기](https://programmers.co.kr/learn/courses/30/lessons/12948)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/hiding_phone_number_12948/Solution.java)

35. [12950 - 행렬의 덧셈](https://programmers.co.kr/learn/courses/30/lessons/12950)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/sum_or_matrix_12950/Solution.java)
    - 2차원 배열을 동적으로 초기화 해줄 때, 전체 `column`에 대해 선언해주고 반복 루프를 이용하여 각각의 `row`에 대해 초기화 해준다.

36. [12954 - x만큼 간격이 있는 n개의 숫자](https://programmers.co.kr/learn/courses/30/lessons/12954)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/number_list_12954/Solution.java)
    - `int` 자료형끼리 연산을 진행 후 해당 결과값을 `long` 자료형에 넣어주면 연산한 결과값이 `int` 범위보다 클 때 데이터 손실이 발생 할 수 있다.
    - 연산 시 임시로 형변환을 해주거나, `long` 자료형을 이용하여 덧셈 연산으로 해결한다.

37. [12969 - 직사각형 별찍기](https://programmers.co.kr/learn/courses/30/lessons/12969)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/print_rectangle_star_12969/Solution.java)

38. [12982 - 예산](https://programmers.co.kr/learn/courses/30/lessons/12982)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/budget_12982/Solution.java)

39. [17682 - 다트 게임 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17682)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/dart_17682/Solution.java)
    - 이런 규칙이 없는 문제는 `if - loop` 의 `depth`를 줄이고 깔끔한 코딩 스타일로 작성할 수는 없을까하고 고민했지만 다른 사람의 풀이를 보니 비슷했다.
    - `10점`의 조건이 데이터 파싱에 있어 조건을 추가하게 만들었다.

40. [42889 - 실패율 : kakao 2019](https://programmers.co.kr/learn/courses/30/lessons/42889)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_1/failure_rate_42889/Solution.java)
    - `stage`에 도달한 `player`가 없을 경우 실패율은 0으로 처리하는 조건을 고려하지 않았었다.
    - 문제는 통과했지만 실패율을 내림차순 정렬하고 그 값의 인덱스를 찾는 과정 등 복잡도를 낮춰야 할 것 같다.

-----------

#### LEVEL 2

1. [42587 - 프린터](https://programmers.co.kr/learn/courses/30/lessons/42587)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/print_42587/Solution.java)
   - `Deque`를 이용하여 풀이를 워하는 문제였으나 `List`를 이용하여 풀었다.
   - `bucket size`가 1인 경우도 고려해서 작성해야 루프에 빠지지 않는다.

2. [42584 - 주식가격](https://programmers.co.kr/learn/courses/30/lessons/42584)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/stock_42584/Solution.java)
   - `ArrayList`를 이용하여 해결했으나 시간복잡도로 인해 효율성 테스트에서 실패했다.
   - 주어진 데이터를 조회만 하면 되므로 배열로만 처리하여 효율성 테스트를 통과하였다.

3. [42588 - 탑](https://programmers.co.kr/learn/courses/30/lessons/42588)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/tower_42588/Solution.java)
   - 굳이 `stack`으로 풀어야 하나 싶다. 연습을 위해서는 `stack`사용을 연습해야 할까?

4. [42839 - 소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/42839)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/find_prime_number_42839/Solution.java) 재도전하기

   - 재귀와 순열 알고리즘 학습이 필요하다.

     ```
     https://bcp0109.tistory.com/14
     ```

   - 순열 알고리즘이 정확히 이해가 안가서 다시 풀어봐야겠다.

5. [12899 - 124 나라의 숫자](https://programmers.co.kr/learn/courses/30/lessons/12899)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/country_of_124_12899/Solution.java)

   - 3진법은 기존의 2진법을 구하는 방식과 다른 점이 있다. 값을 3으로 나누었을 때 나머지가 0일 경우 `몫에 -1`을 더해주고 몫이 0이 될때까지 계속 진행한다.

   - 수학적으로 3진수에서 다음 몫을 구하는 방식

     ```
     n = (n - 1) / 3;
     ```

6. [42883 - 큰 수 만들기](https://programmers.co.kr/learn/courses/30/lessons/42883)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/make_big_number_42883/Solution.java)
   - `num[i]`가 `num[i+k]`까지 비교하여 그중에 `num[i]`보다 큰 값이 있으면 `num[i]`는 `result String`에 추가하지 않는다.
   - 다른 사람의 풀이를 보니 스택으로 많이 구현했다. 

7. [42583 - 다리를 지나는 트럭](https://programmers.co.kr/learn/courses/30/lessons/42583)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/truck_passing_by_bridge_42583/Solution.java) 재도전하기

8. [42586 - 기능개발](https://programmers.co.kr/learn/courses/30/lessons/42586)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/progresses_42586/Solution.java)
   - 남은 시간을 계산한 `workingTime` 배열을 가지고 현재 인덱스가 가진 값보다 큰 값이 나올 때까지 카운트하여 결과값을 저장한다.
   - 남은 일수가 모두 같거나, 작업이 하나인 경우를(현재 인덱스보다 큰 값이 나오지 않을 경우) 고려하여 조건문을 추가했다.

9. [42578 - 위장](https://programmers.co.kr/learn/courses/30/lessons/42578)

   - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/camouflage_42578/Solution.java)
   - `HashMap`을 이용한 문제였는데 배열스럽게 풀었다. 결과를 보고 `Hash`스럽게 코드를 개선하였다.

10. [42577 - 전화번호 목록](https://programmers.co.kr/learn/courses/30/lessons/42577)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/phone_book_42577/Solution.java)

    - 접두어 비교시 `Array` 메서드인 `startsWith`에 대하여 알게 되었다.

    - `String` 숫자를 `sorting` 하면 앞자리부터 비교하기 때문에 바로 다음 값만 비교할 수 있다.

      ```java
      // before
      [11955, 97674223, 1195524421, 9, 333]
      // after
      [11955, 1195524421, 333, 9, 97674223]
      ```

11. [42746 - 가장 큰 수](https://programmers.co.kr/learn/courses/30/lessons/42746)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/biggest_number_42746/Solution.java)

    - 좋은 생각이 나지 않아 구글링하였다. `compareTo` 메서드를 적절히 활용한 좋은 문제같다.

    - `compareTo` 메서드를 이용하여 두 String 객체를 더해서 큰 수에 우선순위가 있도록 정렬한다.

    - 아래 예시 코드가 정확히 어떤 방식으로 정렬되는지 이해해야 한다.

      ```java
      String[] num = {"11", "10", "33", "303", "909", "99"};
      Arrays.sort(num, (o1, o2) -> (o2 + o1).compareTo(o1 + o2));
      
      // result가 0이나 음수이면 자리바꿈을 하지 않고, 양수이면 자리바꿈을 수행한다.
      // 아래의 result = 9이므로 자리바꿈을 수행한다.
      ("99" + "909").compareTo("909" + "99")
      ```

12. [42885 - 구명보트](https://programmers.co.kr/learn/courses/30/lessons/42885)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/life_boat_42885/Solution.java)
    - 처음에 `ArrayList`를 이용하여 풀었으나, 효율성 테스트 중 하나를 통과하지 못했다. `Collection`을 사용해서 시간이 초과된 것으로 판단된다.
    - 가장 큰 `index`의 사람 한 명만 타거나 가장 작은 `index`와 가장 큰 `index`의 사람이 같이 타는 경우밖에 없으므로,  `Array`의 `index`만 변경하는 방식을 사용하여 다시 풀이하였다.

13. [42860 - 조이스틱](https://programmers.co.kr/learn/courses/30/lessons/42860)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/joystick_42860/Solution.java) 재도전하기
    - 좌, 우로 최소로 이동하는 알고리즘이 어렵다.
    - 우로 이동하다 좌로 이동할 수 있다. 좌로 이동하다 우로 이동할 수 있나?
    - 다음에 다시 풀어보도록 한다.
    - 풀이 참고 : https://keepgoing0328.tistory.com/71

14. [42842 - 카펫](https://programmers.co.kr/learn/courses/30/lessons/42842)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/carpet_42842/Solution.java)
    - 전체 타일 갯수의 약수들의 집합을 구하고, 약수와 몫의 합이 가장 작은 경우를 판별하여 풀이하였다.
    - 결과값이 갈색 타일의 갯수와 일치하는 지 여부도 판별해야 모든 테스트 케이스를 통과할 수 있다.

15. [12911 - 다음 큰 숫자](https://programmers.co.kr/learn/courses/30/lessons/12911)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/next_number_12911/Solution.java)
    - `Integer.bitCount()` 메서드를 이용하여 주어진 수의 1의 갯수를 구할 수 있다.

16. [12924 - 숫자의 표현](https://programmers.co.kr/learn/courses/30/lessons/12924)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/serial_number_12924/Solution.java)

17. [12909 - 올바른 괄호](https://programmers.co.kr/learn/courses/30/lessons/12909)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/braket_12909/Solution.java)
    - `()))((()` 와 같이 갯수가 일치해도 문법적으로 맞지 않는 경우도 확인해야 한다.

18. [42888 - 오픈채팅방 : kakao 2019](https://programmers.co.kr/learn/courses/30/lessons/42888?language=java)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/open_chat_42888/Solution.java)
    - `HashMap`과 `List`를 이용하여 어렵지않게 풀었다.

19. [42890 - 후보키 : kakao 2019](https://programmers.co.kr/learn/courses/30/lessons/42890)

    - [Solution] 재도전하기
    - 비트마스크에 대해 학습

20. [17683 -방금그곡 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17683)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/shazam_17683/Solution.java)
    - 처음 풀이에서 몇가지 테스트케이스에 통과하지 못했다. 다른 사람의 풀이를 보니 `C#`의 경우 `c`로 치환하여 풀이하였다.
    - 데이터를 저장하는 `Music` 클래스와 `C# -> c` 치환을 이용하여 다시 풀었다.

21. [17680 - 캐시 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17680)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/cache_17680/Solution.java)

    - `Queue`로 풀이하였으나 몇가지 테스트에 통과하지 못해서 `List`로 풀이하였다.

    - 두 가지의 실수 포인트가 있었다.

      ```java
      1. cache에 city값이 있는경우 LRU(Least Recently Used)이므로 현재 위치의 데이터를 지우고, List의 가장 뒤(tail)로 변경한다. Queue의 경우는 Last로 변경한다.
        
      2. 처음 cache를 주어진 cacheSize만큼 추가할 때 cache가 cacheSize만큼 채워지지 않고 cache hit하는 경우가 생길 수 있음을 고려해야한다.
      ```

22. [60057 - 문자열 압축 : kakao 2020](https://programmers.co.kr/learn/courses/30/lessons/60057)

    - [Solution] 재도전하기
    - 이상하게 안풀린다. 다음에 풀기로 결심했다.

23. [17687 - n진수 게임 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17687)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/n_game_17687/Solution.java)
    - 특정한 수 `number`를 `n`진수로 변환하는 메서드 코드를 작성해야 했는데, 이전에 [124 나라의 숫자](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/country_of_124_12899/Solution.java) 에서 3진수 구하는 문제를 풀었던 기억이 났다.
    - 진수변환하는 `nConvert`메서드에서 `StringBuilder`를 사용하면 `0.1ms`가 걸렸고, `String`을 사용하면 `15ms` 가까이 걸렸다.
    - 이를 보고 `String`을 사용해서 객체를 계속 추가하면 단순히 메모리 공간을 더 차지하는 수준이(`mutable`과 `immutable` 관점에서) 하니고 속도에도 영향을 준다는 것을 알게 되었다.

24. [17677 - 뉴스 클러스터링 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17677)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/news_clustering_17677/Solution.java)

    - `ArrayList`도 `Array`와 같이 단순히 `list1 = list2`로 복사하면 `call by reference` 주소값으로 복사가 된다. `list1.addAll(list2)`로 `value` 값을 복사해서 사용했다.

    - 영문자 이외의 모든 문자를 허용하지 않을 때(숫자, 공백, 특수문자 제거) 정규식과 `match` 메서드를 이용해보았다.

      ```java
      String regex = "^[a-zA-Z]*$";
      if (str.matches(regex)) {
        // str이 숫자, 공백, 특수문자 등 영문자 이외의 값을 갖고 있다면 false
        // str이 오직 영문자만 갖고있다면 true
      }
      ```

25. [17686 - 파일명 정렬 : kakao 2018](https://programmers.co.kr/learn/courses/30/lessons/17686)

    - [Solution](https://github.com/wooody92/algorithm/blob/master/src/main/java/programmers/level_2/sorting_files_17686/Solution.java)
    - 정규식을 이용하였고, 클래스를 생성하여 객체지향적으로 풀었다.
    - 문자열을 파싱하고 조건에 맞도록 `Comparator`와 `compareTo`를 사용하여 정렬하는 문제였다. `Comparator` 사용법을 이해하여 블로그에 정리하였다.
    - [https://wooody92.github.io/algorithm/Algorithm-Comparator/](https://wooody92.github.io/algorithm/Algorithm-Comparator/)

26. [60058 - 괄호 변환 : kakao 2020](https://programmers.co.kr/learn/courses/30/lessons/60058)

    - [Solution]
    - 재귀를 이용한 문자열 처리 문제였고, 재귀 로직을 문제에서 지문 형식으로 알려주었다.
    - 지문을 보고 어떤 구조인지 이해가 안가서 구글링으로 지문을 이해했다.

27. [67257 - 수식 최대화 : kakao 2020](https://programmers.co.kr/learn/courses/30/lessons/67257)

    - [Solution] 재도전하기

    - 빠르게 풀어보려고 하드코딩으로 정리 없이 풀었는데, 테스트케이스에서 실패했다. 다시 천천히 풀어봐야겠다.

    - 다른 풀이들도 참고해보기

      ```
      https://codingjuny.tistory.com/42
      https://msmk530.tistory.com/147
      ```

    

